---
title: My Path to the Dark Side part 4 – the Repositories
author: Alex Ullrich
type: post
date: 2008-07-28T11:07:07+00:00
ID: 87
url: /index.php/desktopdev/mstech/my-path-to-the-dark-side-part-4-the-repo/
views:
  - 5597
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - nhibernate
  - unit testing

---
Previous posts can be found here:

  * [Part One – The Beginning][1]
  * [Part Two – The Domain Model][2]
  * [Part Three – Testing the Schema][3]

Setting up the repositories for our objects is where this really starts to get fun for me. This is what allows us to work with the persisted objects so easily from our application code, without all the SQL getting in the way. The first thing we want to think about here is what we need the repository to do. Add/Delete/Update all come to mind of course. As well as retrieval of single objects and collections. These will be pretty much standard behaviors across most of our objects. So lets' look at the interface first:

```csharp
using System;
using System.Collections.Generic;
using RecipeTracker.Model;

namespace RecipeTracker.Interfaces
{
    public interface IRecipeRepository
    {
        void Add(Recipe recipe);
        void Update(Recipe recipe);
        void Remove(Recipe recipe);
        Recipe GetByID(int id);
        ICollection<Recipe> GetByFamily(string family);
        ICollection<Recipe> GetAll();
        void Dispose();
    }
}
```

Looking at all those methods, there is really only one (GetByFamily) that we won't need for any repository that we create. So we can put all the other methods into a BaseRepository class. We will need use generics so we can return all the different types however. But first, we need to get a session, so we can add a class for that.

```csharp
using System;
using NHibernate;
using NHibernate.Cfg;

namespace RecipeTracker.Repositories
{
    public class SessionProvider<T> where T:new()
    {
        private static ISessionFactory _sessionFactory;

        private static ISessionFactory SessionFactory
        {
            get
            {
                if (_sessionFactory == null)
                {
                    var configuration = new Configuration();
                    configuration.Configure();
                    configuration.AddAssembly(typeof(T).Assembly);
                    _sessionFactory = configuration.BuildSessionFactory();
                }

                return _sessionFactory;
            }
        }

        public static ISession OpenSession()
        {
            return SessionFactory.OpenSession();
        }
    }
}
```

The SessionFactory part should look familar from part 3, the only difference here is that we are initializing the configuration using TypeOf(T) to determine which assembly to find the configuration in rather than TypeOf(MyType). We could probably get away with the latter for this purpose, because there probably won't be more than one assembly in the application, but why be lazy right? After all, we do need to use generics to deal with the return types anyways. 

So now this little bit of code doesn't need to be handled by our repository, and it can focus on what it does best. So here's the BaseRepository, it's nice and simple since it doesn't need to get its' own sessions anymore:

```csharp
using System;
using System.Collections.Generic;
using NHibernate;
using NHibernate.Cfg;

namespace RecipeTracker.Repositories
{
    public abstract class BaseRepository<T> where T: new()
    {
        protected ISession _session = SessionProvider<T>.OpenSession();

        public T GetByID(int id)
        {
            return _session.Get<T>(id);
        }

        public ICollection<T> GetAll()
        {
            var products = _session
                .CreateCriteria(typeof(T))
                .List<T>();
            return products;
        }

        public void Update(T toUpdate)
        {
            using (ITransaction transaction = _session.BeginTransaction())
            {
                _session.Update(toUpdate);
                transaction.Commit();
            }
        }

        public void Add(T toAdd)
        {
            using (ITransaction transaction = _session.BeginTransaction())
            {
                _session.Save(toAdd);
                transaction.Commit();
            }
        }

        public void Remove(T toRemove)
        {
            using (ITransaction transaction = _session.BeginTransaction())
            {
                _session.Delete(toRemove);
                transaction.Commit();
            }
        }

        public void Dispose()
        {
            _session.Close();
            _session.Dispose();
        }
    }
}
```

Now, look how simple that is to do what we need with our object? No building SQL queries, no creating parameter arrays, or anything. A nice simple bit of code that does just what we need it to do, without all the hassles. We just need a session and the simple commands that it offers, and we can do anything we need. Beautiful, right?

But what if we wanted to do something like get all recipes from a certain family? This will be specific to the object type we need, so we can do that in our implementation of the baseclass (hey, gotta have something in there right!). And this is in fact all that our RecipeRepository class has, one method:

```csharp
using System;
using System.Collections.Generic;
using RecipeTracker.Model;
using NHibernate;

namespace RecipeTracker.Repositories
{
    public class RecipeRepository : BaseRepository<Recipe>, Interfaces.IRecipeRepository, IDisposable
    {
        public ICollection<Recipe> GetByFamily(string family)
        {
                var products = _session
                    .CreateCriteria(typeof(Recipe))
                    .Add(NHibernate.Criterion.Expression.Eq("Family", family))
                    .List<Recipe>();
                return products;
        }
    }
}
```

This is some wacky looking code at first. It kind of reminds me of Linq, but what its' called is HQL (Hibernate Query Language). I haven't really gotten into it all that much, so I don't feel qualified to speak about it in detail, but I do find it kinda cool. It may not be as easy as Linq, where you could do a nice Linq query such as

<code class="codespan">recipeList.Where(n => n.Family = family)</code>

But remember this needs to work on older framework versions as well. I think the HQL is reasonably succinct, and even somewhat elegant. After all reading that you can just about instantly tell what it does. It just creates a criteria on the session for Recipes, and then defines the expression to be evaluated (table.Family = family). Pretty cool. We could probably figure out how to do this with generics pretty easily, but I figure this kind of method is going to be tied to your specific type, and you want to have a descriptive name, and all that.

So after all this I think we are ready to set up some tests. This will be on the next page (damn I'm getting long-winded!)

<!--nextpage-->I cut this to only two method for the sake of brevity but all the methods (and other repositories) are included in the attached project.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using NHibernate;
using NHibernate.Cfg;
using NHibernate.Tool.hbm2ddl;
using NUnit.Framework;
using RecipeTracker.Model;
using RecipeTracker.Interfaces;
using RecipeTracker.Repositories;

namespace RecipeTracker.Tests
{
    [TestFixture]
    public class RecipeRepository_Fixture
    {
        private ISessionFactory _sessionFactory;
        private Configuration _configuration;

       [TestFixtureSetUp]
        public void TestFixtureSetUp()
        {
            _configuration = new Configuration();
            _configuration.Configure();
            _configuration.AddAssembly(typeof(Ingredient).Assembly);
            _sessionFactory = _configuration.BuildSessionFactory();
        }

        [SetUp]
        public void SetupContext()
        {
            new SchemaExport(_configuration).Execute(false, true, false, false);
            //SchemaFiller.CreateInitialData();
        }

        [TearDown]
        public void TeardownContext()
        {
            new SchemaExport(_configuration).Execute(false, false, true, false);
        }

        [Test]
        public void Can_retrieve_recipe_by_id()
        {
            IRecipeRepository repository = new RecipeRepository();

            var recipe = repository.GetByID(1);

            Assert.IsNotNull(recipe);
            Assert.AreEqual(recipe.Name, "India Pale Ale");
        }

        [Test]
        public void Can_add_new_recipe()
        {
            var recipe = new Recipe
            {
                Name="Belgian White",
                OriginalGravity=1.062,
                FinalGravity=1.017,
                FermentationTime=7,
                FermentationTemperature=70,
                AttemptDate=DateTime.Now
            };
        }    
    }
}
```
A few new things to note here. First, the [TestFixtureSetUp] attribute tag. This identifies a method that needs to be execute when we first set up the fixture. Not to be confused with [SetUp] which identifies a method that needs to be executed before **each test** is run. And then [TearDown] which indicates a method to be run after each test. In our case, TestFixtureSetup() initializes our configuration. SetupContext() creates our schema. And TeardownContext() gets rid of our schema. These are all very simple, and maybe not even necessary, but it is important to know that they are there, and see how much more could be done in these methods (see the commented out SchemaFiller.CreateInitialData() that is used for (you guessed it) creating initial data!).

Now we are ready to fire up NUnit and run our tests. And now is when we'll see that beautiful red bar (trust me it is beautiful, it means that you designed a test that will fail when its' supposed to!). When it fails you'll see something like this:

<code class="codespan">RecipeTracker.Tests.ImpressionRepository_Fixture (TestFixtureSetUp):<br />
NHibernate.InvalidProxyTypeException : The following types may not be used as proxies:<br />
RecipeTracker.Model.Recipe: method AlcoholByWeight should be virtual</code>

As it turns out we need to implement all of the methods and properties as virtual so that NHibernate can create subclasses from our object. Well, we can do this or specify lazy = "false" for our classes. But I like the lazy initialization, so I will make them virtual. After doing this, the tests should run just fine.

 [1]: /index.php/DesktopDev/MSTech/the-path-to-nhibernate-aamp-tdd-part-1-t
 [2]: /index.php/DesktopDev/MSTech/my-path-to-the-dark-side-part-2-the-doma
 [3]: /index.php/DesktopDev/MSTech/my-path-to-the-dark-side-part-3-testing-