---
title: Tweaking NHibernate Mappings at Runtime With a Remapper
author: Alex Ullrich
type: post
date: 2011-09-08T22:26:00+00:00
ID: 1280
excerpt: "Every once in a while, especially on legacy systems, you can run into database-specific issues that NHibernate can't get around out of the box.  We've been looking to convert from our current ORM to NHibernate, and run into a couple.  One is self-inflic&hellip;"
url: /index.php/enterprisedev/orm/nhibernate/tweaking-nhibernate-mappings-at-runtime/
views:
  - 11323
rp4wp_auto_linked:
  - 1
categories:
  - nHibernate (.Net)

---
Every once in a while, especially on legacy systems, you can run into database-specific issues that NHibernate can't get around out of the box. We've been looking to convert from our current ORM to NHibernate, and run into a couple. One is self-inflicted (we use a DB-first codegen approach) and the other is forced on us by differences in the way Oracle and SQL Server handle ID assignment. So our problems are:

  1. NHibernate defaults to a single global sequence on Oracle – we need a named sequence per table
  2. Table names on oracle can't exceed 30 characters

We really don't want to compile mappings into their own assembly (really 2, that we could switch between), so we looked for alternatives and found a [Remapper][1] approach on Krystof Kozmic's blog. In a nutshell, this allows you to adjust your (single set of) mappings at runtime to account for platform-specific bits.

So let's take a quick look at how these problems can be addressed.  First step is to find out if we're running against Oracle (we are lucky enough to be able to assume SQL Server if it's not Oracle,  so it's a bit easier).  This can be done by examining the Properties dictionary on the configuration (in this case, in the remapper's constructor):

```csharp
Remapper (Configuration configuration) {
    config = configuration;
    isOracle = config.Properties["dialect"].ToLower ().Contains ("oracle");
}
```

Once you get this property, you should be able to resolve whatever you need. You could also get it from the "connection.driver_class" property but this seemed more sensible for me.

Once you've got the platform, the fun of remapping begins. For our purposes we are interested in two collections hanging off the configuration, the class mappings (for adjusting primary table names and removing sequences) and the collection mappings (for adjusting cross-reference table names). The method will look something like this:

```csharp
Configuration Remap () {
    foreach (var classMap in config.ClassMappings) {
        RemapTableName (classMap, isOracle);
        RemapSequence (classMap, isOracle);
    }

    foreach (var collectionMap in config.CollectionMappings) {
        RemapLinkTableName (collectionMap, isOracle);
    }

    return config;
}
```

This again is a pretty straightforward flow. We're just applying the necessary remap logic to the two collections one at a time. It may look weird that everything's private, but I hope it will make sense in a minute.

The simplest remapping we are doing is the table name remapping. Both ClassMaps and Collection(map)s have a table property that we can run the same code against to fix. The method for remapping looks like this:

```csharp
void FixOracleName (Table table) {
    if (isOracle) {
        table.Name = table.Name.Substring (0, 30);
    }
}
```

Not rocket science. Remapping the sequences was a bit trickier. We need to remove the sequence property specified in the mappings for SQL Server, but getting to it proved to be a bit difficult. There is an Identifier on the ClassMap that seemed to be (and actually was) what we needed, but we had to do some work to do to get it in a form we could use. The key ended up being casting the Identifier property to a SimpleValue, which NHibernate defines as "Any value that maps to columns". Once we had a SimpleValue, we were able to access a dictionary called IdentifierGeneratorProperties that lets us remove the sequence element. The method to remap the sequences ends up looking like this:

```csharp
void RemapSequence (PersistentClass classMap) {
    if (isOracle) return;

    if (classMap.Identifier.IsSimpleValue) {
        var simpleVal = classMap.Identifier as SimpleValue;
        simpleVal.IdentifierGeneratorProperties.Remove ("sequence");
    }
}
```

The last thing to address is the private-ness of everything. Passing around the isOracle flag between all the methods seems like it could get ugly pretty quick, so it makes sense to figure that out in the constructor and keep it accessible. However, that could make calling the remap method weird (because on subsequent calls it won't actually DO anything). The class was really intended to be used only once, but I couldn't think of a great way to enforce that without creating even more weird flags and what not. So I limited access to the class to a single static method that looks like this:

```csharp
public static Configuration Remap (Configuration config) {
    var remapper = new Remapper (config);
    return remapper.Remap ();
}
```

This does make testing kind of rough, but it isn't tremendously difficult to create an ad-hoc configuration from code (or serialize one to be used in testing later). For more complex remapping logic I would probably change the way it's structured to allow testing methods one at a time, but for this it seemed a bit silly. The static method allows it to be called like this (this example from fluent setup for tests that stores the underlying config for use in schema modifications):

```csharp
sessionFactory = Fluently.Configure ()
    .Database (SQLiteConfiguration.Standard.InMemory ().ShowSql ())
    .Mappings (m => m.FluentMappings.AddFromAssembly (assemblyContainingMapping))
    .ExposeConfiguration (cfg => {
        cfg = Remapper.Remap (cfg);
        configuration = cfg;
    })
    .BuildSessionFactory ();
```

This isn't something you'd really ever want to use if you could avoid it, but in cases like ours this kind of technique can spare you from needing separate sets of mappings for different databases. Used sparingly, it can be very effective. I hope this post gets across how easy it is to make adjustments to your NHibernate configuration from code. I think our case was simple enough to give a glimpse of what's possible, without getting bogged down in everything that is in fact possible. I've linked the entire file (with a txt extension added to appease b2evo) for convenience.

[Remapper.cs.txt][2]

 [1]: http://devlicio.us/blogs/krzysztof_kozmic/archive/2009/08/17/adjusting-nhibernate-mapping-for-tests.aspx
 [2]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/EnterpriseDev/Tweaking-NHibernate-Mappings-Remapper/Remapper.cs.txt?mtime=1315527513