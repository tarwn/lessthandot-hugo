---
title: Inverse Mapped Collections and NHibernate's Second-Level Cache
author: Alex Ullrich
type: post
date: 2011-02-14T13:42:00+00:00
ID: 1040
excerpt: 'In order to simplify saving some entities, I recently changed some mappings in my application to use the inverse attribute.  In less cryptic terms, this means that the collection item is responsible for maintaining the relationship to the collection own&hellip;'
url: /index.php/webdev/serverprogramming/inverse-mapped-collections-and-nhibernate-s-second-level-cache/
views:
  - 13011
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Server Programming

---
In order to simplify saving some entities, I recently changed some mappings in my application to use the **inverse** attribute. In less cryptic terms, this means that the collection item is responsible for maintaining the relationship to the collection owner. In contrast, when using a standard relationship the parent entity is responsible for managing the collection items. The reason for this is that it lets you save the collection items without going to the database (or second level cache) to get the collection's owner.

This can make things a little weird – the primary difference is that both sides of the relationship now need to be aware that they are participants. Say you have the following two classes, and the corresponding mappings (sorry, I used Fluent NHibernate):

```csharp
class Recipe {
    public long Id { get; set; }
    public string Name { get; set; }
    IList<IngredientUse> IngredientsUsed { get; set; }
}

class IngredientUse {
    public long Id { get; set; }
    public double Quantity { get; set; }
    public Ingredient Ingredient { get; set; }
}

class RecipeMap : ClassMap<Recipe> {
    Id (r => r.Id);
    Map (r => r.Name);
    HasMany (i => i.IngredientsUsed);
}

class IngredientUseMap : ClassMap<IngredientUse> {
    Id (iu => iu.Id);
    Map (iu => iu.Quantity);
    References (iu => iu.Ingredient);
}
```

This is pretty straightforward, but it does do a little bit of magic behind the scenes. For example, even though the IngredientUse entity is not referencing the Recipe, NHibernate will expect a column called "Recipe_id" on the IngredientUse table in the database that it can use to maintain the relationship between the two entities. 

When using an inverse relationship, a few changes are needed. First, you map the collection (from the Recipe's perspective) using the inverse attribute. Also, you need to reference the Recipe from the Ingredient (and the associated mapping). So the inverse mappings look like this:

```csharp
class Recipe {
    public long Id { get; set; }
    public string Name { get; set; }
    IList<IngredientUse> IngredientsUsed { get; set; }
}

class IngredientUse {
    public long Id { get; set; }
    public double Quantity { get; set; }
    public Ingredient Ingredient { get; set; }
    public Recipe Recipe { get; set; }
}

class RecipeMap : ClassMap<Recipe> {
    Id (r => r.Id);
    Map (r => r.Name);
    HasMany (i => i.IngredientsUsed).Inverse ();
}

class RecipeMap : ClassMap<IngredientUse> {
    Id (iu => iu.Id);
    Map (iu => iu.Quantity);
    References (iu => iu.Ingredient);
    References (iu => iu.Recipe);
}
```

It's important to note that the updated mapping expects the exact same underlying DB structure. So the model is starting to look a bit more like the database. What this gets you is the ability to save an IngredientUse without first needing to retrieve the recipe you want to add it to. So instead of saving it like this:

```csharp
void Save (IngredientUse ingredientUse, long recipeId) {
    var recipe = session.Get<Recipe> (recipeId);
    recipe.AddIngredientUse (ingredientUse);
    session.SaveOrUpdate (recipe);
}
```

You now save it like this:

```csharp
void Save (IngredientUse ingredientUse, long recipeId) {
    ingredientUse.Recipe = session.Load<Recipe> (recipeId);
    session.SaveOrUpdate (ingredientUse);
}
```

The difference between Load and Get in this case is worth noting. Load will NEVER return null, it will get your entity or throw an exception. Get on the other hand can return null. So when you use Load, you are essentially telling NHibernate "this entity IS in the database, trust me". That enables NHibernate to use a proxy instead of going out to make sure your entity actually exists. And since we only need the Id, that proxy will never need to go out and load any of the actual object data. I found this especially useful when saving an IngredientUse at the end of an HTTP request.

A perfectly logical side effect (that I didn't consider) to this change is the presence of stale results in NHibernate's second level cache. Because the Recipe's associated collections are cached in relation to the Recipe, and the Recipe is never saved, the collection does not get updated in the cache. I thought I was stuck for a bit, until I found the Session Factory's ["EvictCollection"][1] method. This is one of those "use at your own risk" methods, so consider yourself warned. If abused, this could get pretty ugly (leading to a situation where you'd be better off not caching your collections at all). However, I've got an app where I expect far more reads than writes, and I don't think I'll be using it all over the place, so I think this method can be leveraged to solve my problem.

The EvictCollection method's overload that we are interested in takes a string representing the fully qualified collection name, and an object representing the Id of the **parent object** (in this case the Recipe). So the call we need to make looks something like this:

```csharp
sessionFactory.EvictCollection("MyApp.Domain.Recipe.IngredientsUsed", 1);
```

Easy enough, but the approach leaves a bit to be desired. Because of the magical string, this approach will not be refactoring-friendly, and prone to runtime errors. By using a few LINQ expressions we can do a bit better though. We'll create a class called CollectionEvictor to take care of this stuff for us. But first we'll write a test.

```csharp
[Test]
public void Evict ()
{
    var mockery = new MockRepository ();
    var sessionFactory = mockery.StrictMock<ISessionFactory> ();

    var ingredientUse = new IngredientUse {
        Recipe = new Recipe { Id = 1 }
    };

    using (mockery.Record ()) {
        sessionFactory.EvictCollection ("MyApp.Domain.Recipe.IngredientsUsed", additionUse.Recipe.Id);
    }
            
    using (mockery.Playback ()) {
        var evictor = new CollectionEvictor<IngredientUse> (
            x => x.Recipe.IngredientsUsed,
            x => x.Recipe.Id,
            sessionFactory);

        evictor.Evict(additionUse);
    }
}
```

And the class is pretty straight forward as well:

```csharp
public class CollectionEvictor<T> where T : class
{
    readonly MemberExpression collectionProperty;
    readonly Func<T, object> idFunction;
    readonly ISessionFactory sessionFactory;

    public CollectionEvictor (Expression<Func<T, object>> collectionProperty, Func<T, object> idFunction)
        : this (collectionProperty, idFunction, ObjectFactory.GetInstance<ISessionFactory> ()){ }

    public CollectionEvictor (Expression<Func<T, object>> collectionProperty, Func<T, object> idFunction, ISessionFactory sessionFactory)
    {
        this.collectionProperty = collectionProperty.Body as MemberExpression;
        this.idFunction = idFunction;
        this.sessionFactory = sessionFactory;
    }

    public void Evict (T updated)
    {
        var key = collectionProperty.Expression.Type.FullName + "." + collectionProperty.Member.Name;
        sessionFactory.EvictCollection (key, idFunction.Invoke (updated));
    }
}
```

And now we've got something that's a bit awkward, but at least checked at compile time. It seems strange that we need to go through the IngredientUse's link back to the recipe to specify the collection, but that is one of the things that allows us to do it in a generic fashion. The key to making this happen is the LINQ expression type, that allows us to store the lambda expression as **data** instead of as executable code, allowing us to get much more information about it's signature. By taking its body and casting it to a MemberExpression (thanks [Matt!][2]) it becomes easy to get the full name of the Recipe type, and the name of the property referenced in the function. We can then put these together and get our magical string. Because we don't need to execute anything but the call to get the attached Recipe's Id, we don't need to load anything from the database and can use the information our proxy already has.

Assuming there's already a base repository defined, it can be extended like this:

```csharp
public abstract class CollectionEvictingRepository<T> : BaseRepository<T> where T : class, new ()
{
    protected readonly IList<CollectionEvictor<T>> evictors = new List<CollectionEvictor<T>> ();

    public override void Save (T toSave)
    {
        base.Save (toSave);
        EvictCollections (toSave);
    }

    public override void Remove (T toRemove)
    {
        base.Remove (toRemove);
        EvictCollections (toRemove);
    }

    void EvictCollections (T toSave)
    {
        foreach (var evictor in evictors) {
            evictor.Evict (toSave);
        }
    }
}
```

As you can see, this calls the standard Save/Delete behavior, then evicts any collections that have been invalidated as a result of the operation. Implementations can then look like this:

```csharp
public sealed class AdditionUseRepository : CollectionEvictingRepository<AdditionUse>
{
	public AdditionUseRepository() 
	{
	    evictors.Add(new CollectionEvictor<IngredientUse>(
                instance => instance.Recipe.IngredientsUsed,
	        instance => instance.Recipe.Id));
	}
}
```

All told this is a pretty naive implementation (You'll need to check for null after casting to MemberExpression, etc...) but I think it shows the idea pretty well. You may not ever need to use an Inverse relationship, but if you do, and need to pair it with a second level cache, something like this might come in handy.

 [1]: http://ajava.org/online/hibernate3api/org/hibernate/SessionFactory.html#evictCollection%28java.lang.String,%20java.io.Serializable%29
 [2]: /index.php/All/?disp=authdir&author=225