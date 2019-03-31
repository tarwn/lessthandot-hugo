---
title: My Path to the Dark Side part 3 – Testing the Schema
author: Alex Ullrich
type: post
date: 2008-07-21T13:59:00+00:00
url: /index.php/desktopdev/mstech/my-path-to-the-dark-side-part-3-testing/
views:
  - 3587
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

  * [Part One &#8211; The Beginning][1]
  * [Part Two &#8211; The Domain Model][2]

In part two we set up our domain model. Now, before we can test nhibernate&#8217;s ability to work with and persist objects, we need to ensure that we&#8217;ve defined our schema well enough that NHibernate can create the Schema for us (since that was kind of the point). Now is where NUnit starts to become very useful.

First step we&#8217;re going to take to set up NUnit is to add another project to our solution. I named this project the very creative RecipeTracker.Tests. When setting up this project, it is important to add all the same references as we used in the main project. In addition we need to add a reference to the main project itself and a reference to nunit.framework. For the main project, we&#8217;ll need to again set the &#8220;CopyLocal&#8221; option to true. Finally, we also need a copy of our Sql Compact database in this project.

Our first unit test will just be to see that we can export the schema. Here it is:

<pre>using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using NHibernate.Cfg;
using NHibernate.Tool.hbm2ddl;
using NUnit.Framework;
using RecipeTrackerPartOne;

namespace RecipeTrackerPartOne.Tests
{
    [TestFixture]
    public class GenerateSchema_Fixture
    {
        [Test]
        public void Can_generate_schema()
        {
            var cfg = new Configuration();
            cfg.Configure();
            cfg.AddAssembly(typeof(Model.Recipe).Assembly);

            new SchemaExport(cfg).Execute(false, true, false, false);
        }
    }
}</pre>

There&#8217;s a lot of new stuff going on here. Most noticeable is the directive to use nunit.framework, and the attributes added to our class and our methods. These attributes are used when we load the project into NUnit, so that the NUnit application knows which code to execute as a test. We need to identify our class as a TestFixture, then our method within the class as a Test (this setup allows us to include supporting code that is not necessarily a test). Also notable is the directive to use NHibernate.Tool.hbm2ddl. This is what allows NHibernate to create the schema for us (by converting Hibernate mappings to Data Definition Language).

The most interesting line, as far as I am concerned, is the &#8220;AddAssembly&#8221; line. This is what associates our Configuration with a given assembly, and therefore that assembly&#8217;s hibernate.cfg.xml file. So, what we are really testing here is really the schema we have defined in the xml file.

If everything is set up right, when you fire up the NUnit GUI, load the RecipeTracker.Tests assembly, and run the test, you should see a nice green bar. Unfortunately, we broke the first rule of Unit Testing, that the first time we run a test it should fail. Well, I didn&#8217;t, but I wanted to spare you gentle readers some of the pain of wrestling with the mapping file. But fear not, because now it is time to experience the joy that is the red, &#8220;you screwed up&#8221; bar. Remember, we want to confirm that NUnit will in fact tell us when we do screw up!

For this part we&#8217;ll need to start setting up our repositories to move the objects to and from the database. This gets pretty involved, so this will have to be continued in Part 4.

Here is the sample project (so far). Next one will be where it gets interesting! [Sample Project &#8211; Part 1][3]

 [1]: /index.php/DesktopDev/MSTech/the-path-to-nhibernate-aamp-tdd-part-1-t
 [2]: /index.php/DesktopDev/MSTech/my-path-to-the-dark-side-part-2-the-doma
 [3]: http://www.mediafire.com/?vetm5z4ou4d