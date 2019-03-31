---
title: My Path to the Dark Side part 2 – The Domain Model
author: Alex Ullrich
type: post
date: 2008-07-15T10:24:00+00:00
url: /index.php/desktopdev/mstech/my-path-to-the-dark-side-part-2-the-doma/
views:
  - 3782
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
  * [Part One &#8211; The Beginning][1]

In part one we discussed what has brought me to the shameful point of using an object-relational mapper. At the risk of being ostracized from the database community, I really think this is going to be helpful for my project. 

The next step is to actually build up the domain model, and set up the mappings for NHibernate. I won&#8217;t be pasting all the code in here, but I will be attaching the project itself to the next post if anyone&#8217;s interested. I ended up working ahead of myself so I had to kind of go backwards to create a &#8220;part one&#8221; project (I got too carried away with working, forgot to check in for a few days 🙁 ). First let us look at the &#8220;recipe&#8221; domain object, which is at the center of everything.

<pre>using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace RecipeTrackerPartOne.Model
{
    public class Recipe
    {
        private Int32 _id;
        private String _name;
        private DateTime _attemptDate;
        private String _family;
        private String _style;
        private Double _originalGravity;
        private Double _finalGravity;
        private Int32 _boilTime;
        private Int32 _fermentationTime;
        private Int32 _fermentationTemperature;
        private Int32 _secondaryFermentationTime;
        private String _yeast;
        private Int32 _batchSize;
        private String _note;
        private Person _brewer;

        //rating info
        private IList&lt;Impression&gt; _recipeImpressions;


        //list of ingredients
        private IList&lt;Ingredient&gt; _ingredientsUsed;

        public Recipe()
        {
            _recipeImpressions = new List&lt;Recipe&gt;();
            _ingredientsUsed = new List&lt;Recipe&gt;();
        }

        public Int32 Id
        {
            get { return _id; }
            set { _id = value; }
        }

        public String Name
        {
            get { return _name; }
            set { _name = value; }
        }

        public DateTime AttemptDate
        {
            get { return _attemptDate; }
            set { _attemptDate = value; }
        }

        public String Family
        {
            get { return _family; }
            set { _family = value; }
        }

        public String Style
        {
            get { return _style; }
            set { _style = value; }
        }

        public Double OriginalGravity
        {
            get { return _originalGravity; }
            set { _originalGravity = value; }
        }

        public Double FinalGravity
        {
            get { return _finalGravity; }
            set { _finalGravity = value; }
        }

        public Int32 BoilTime
        {
            get { return _boilTime; }
            set { _boilTime = value; }
        }

        public Int32 FermentationTime
        {
            get { return _fermentationTime; }
            set { _fermentationTime = value; }
        }

        public Int32 FermentationTemperature
        {
            get { return _fermentationTemperature; }
            set { _fermentationTemperature = value; }
        }

        public Int32 SecondaryFermentationTime
        {
            get { return _secondaryFermentationTime; }
            set { _secondaryFermentationTime = value; }
        }

        public String Yeast
        {
            get { return _yeast; }
            set { _yeast = value; }
        }

        public Int32 BatchSize
        {
            get { return _batchSize; }
            set { _batchSize = value; }
        }

        public String Note
        {
            get { return _note; }
            set { _note = value; }
        }

        public IList&lt;Impression&gt; RecipeImpressions
        {
            get { return _recipeImpressions; }
            set { _recipeImpressions = value; }
        }

        public IList&lt;Ingredient&gt; IngredientsUsed
        {
            get { return _ingredientsUsed; }
            set { _ingredientsUsed = value; }
        }

        public Person Brewer
        {
            get { return _brewer; }
            set { _brewer = value; }
        }

        public void AddIngredient(Model.Ingredient i)
        {
            _ingredientsUsed.Add(i);
        }

        public void RemoveIngredient(Model.Ingredient i)
        {
            _ingredientsUsed.Remove(_ingredientsUsed.Single&lt;Model.Ingredient&gt;(x =&gt; x == i));
        }

        public void AddImpression(Model.Impression i)
        {
            _recipeImpressions.Add(i);
        }

        public void ChangeBrewer(Model.Person p)
        {
            _brewer = p;
        }

        public Double AlcoholByWeight()
        {
            return (76.08 * Convert.ToDouble(_originalGravity - _finalGravity)) / (1.775 - Convert.ToDouble(_originalGravity));
        }

        public Double AlcoholByVolume()
        {
            return AlcoholByWeight() * (Convert.ToDouble(_finalGravity) / 0.794);
        }
    }
}</pre>

Its&#8217; a pretty simple object, it has some data, and just a couple methods. The most interesting properties to look at here are RecipeImpressions, IngredientsUsed, and Brewer. Why are these the most interesting? Because each is of another type (Impression, Ingredient, and Person, respectively) that we will need to persist in our database. Now that we&#8217;ve got an object in mind, lets&#8217; start thinking about setting this up. 

**Setting Up The Project**

We need to do some housekeeping before we proceed. 

First, adding our references to the following:

  * NHibernate (I used the most recent beta version since this is not exactly a critical app)
  * Sql Server Compact (System.Data.SqlServerCe)

Once these references are added, we need to make sure both have their &#8220;Copy Local&#8221; property set to True (by default this will be false for SqlServerCe). 

After adding the references, we need to add a Sql Server Compact database to our project (in the root directory). I called it &#8220;BeerRecipes.sdf&#8221;. You can skip that Data Set Wizard thing that pops up.

Once this is done, we are ready to set up NHibernate.

**Setting Up NHibernate Configuration**

NHibernate is driven by an xml file added to the project called hibernate.cfg.xml. So what are you waiting for, add that file! (again to the root directory) I promise to be here when you get back. Ok, that was not so hard, right? Now, what do we need to add to this file? For this simple example, not all that much. We need to define a &#8220;session-factory&#8221; class, which is basically a more sophisticated connection string. Properties we need to set include:

  * Provider
  * Dialect &#8211; SQL &#8220;flavor&#8221; for NHibernate to use
  * Driver &#8211; Driver NHibernate uses to identify (and connect to) Provider
  * Connection String &#8211; where is the database located?
  * Show Sql &#8211; whether to output SQL to the console

So, what does this file look like? Glad you asked:

<pre>&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;hibernate-configuration xmlns="urn:nhibernate-configuration-2.2"&gt;
  &lt;session-factory&gt;
    &lt;property name="connection.provider"&gt;NHibernate.Connection.DriverConnectionProvider&lt;/property&gt;
    &lt;property name="dialect"&gt;NHibernate.Dialect.MsSqlCeDialect&lt;/property&gt;
    &lt;property name="connection.driver_class"&gt;NHibernate.Driver.SqlServerCeDriver&lt;/property&gt;
    &lt;property name="connection.connection_string"&gt;Data Source=BeerRecipes.sdf&lt;/property&gt;
    &lt;property name="show_sql"&gt;true&lt;/property&gt;
  &lt;/session-factory&gt;
&lt;/hibernate-configuration&gt;</pre>

For the most part these are pretty self explanatory. The only one I&#8217;ve really been messing with is the &#8220;show_sql&#8221; property, which controls whether the sql generated by NHibernate is output to the console or not. I like to set this to true when I am running my unit tests manually, so I can look at the SQL in case a test fails. Otherwise, I would set it to false.

**Setting up NHibernate Mappings**

This is where it gets interesting. We now need to tell NHibernate which tables within the database to store our objects in, and define any relationships between the objects. We also need to tell NHibernate which assembly to look for our types in. We&#8217;ll do this in a mappings file. I created a special &#8220;mappings&#8221; folder within the project for this. The naming convention for these files is myfile.hbm.xml. Because I&#8217;m lazy I just created one file, called RecipeMappings.hbm.xml and threw everything in there. So lets&#8217; just get the whole thing on the table first, then talk about it (only 2 classes included for brevity&#8217;s sake, but they&#8217;re all there I swear).

<pre>&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;hibernate-mapping xmlns="urn:nhibernate-mapping-2.2"
                   assembly="RecipeTracker"
                   namespace="RecipeTracker.Model"&gt;

  &lt;class name="Recipe" table="Recipes"&gt;
    &lt;id name="Id" column="Id" type="Int32"&gt;
      &lt;generator class="native" /&gt;
    &lt;/id&gt;
    &lt;property name="Name" type="String(35)"/&gt;
    &lt;!-- we can also do without specifycing type and let nhibernate do the work--&gt;
    &lt;property name="AttemptDate"/&gt;
    &lt;property name="Family"/&gt;
    &lt;property name="Style"/&gt;
    &lt;property name="OriginalGravity"/&gt;
    &lt;property name="FinalGravity"/&gt;
    &lt;property name="BoilTime"/&gt;
    &lt;property name="FermentationTime"/&gt;
    &lt;property name="FermentationTemperature"/&gt;
    &lt;property name="SecondaryFermentationTime"/&gt;
    &lt;property name="Yeast"/&gt;
    &lt;property name="BatchSize"/&gt;
    &lt;property name="Note"/&gt;
    &lt;!-- nhibernate will set up a foreign key relationship between tables for us --&gt;
    &lt;bag name="IngredientsUsed" cascade="all" lazy="true"&gt;
      &lt;key column="RecipeID"/&gt;
      &lt;one-to-many class="Ingredient"/&gt;
    &lt;/bag&gt;
    
    &lt;!-- lazy = true is default, don't necessarily need it--&gt;
    &lt;bag name="RecipeImpressions" cascade="all"&gt;
      &lt;key column="RecipeID"/&gt;
      &lt;one-to-many class="Impression"/&gt;
    &lt;/bag&gt;

    &lt;many-to-one name="Brewer" column="Brewer" class="Person"/&gt;

  &lt;/class&gt;
&lt;/hibernate-mapping&gt;</pre>

The first item of interest here is the &#8220;hibernate-mapping&#8221; tag. This obviously is responsible for identifying the file as an NHibernate mapping file. Its&#8217; also handy to be able to define the assembly and namespace in this tag as well. This is similar to a &#8220;using&#8221; directive in C# code, in that you won&#8217;t need to specify the assembly or namespace for each class you define.

After this, the class definitions are fairly straightforward. It is important to note that we need to specify the table that each class maps to however. In this example we are not specifying the data width for any of our columns of type &#8220;string&#8221; after the first one. This is because we will be relying on NHibernate to create our schema.

Its&#8217; also important to note that we need to define an Id column for each type. While we won&#8217;t really be using this in our programming, NHibernate needs it to keep track of objects, and manage the relationships.

Finally, we get down to where the mappings are established. As we saw in the domain diagram in part 1, each recipe is related to three other types in the application. A recipe can have any number of Ingredients, any number of Impressions, and one brewer (of type Person). The first two relationships need to be defined as one to many, and the third as a many to one (because one brewer could have any number of recipes). We used a &#8220;bag&#8221; because this corresponds to an IList. You can also use &#8220;set&#8221; which corresponds to an IEnumerable. 

Now, we have defined our domain, and thus our database schema. Next part is to set up our first unit test, to ensure that NHibernate can create the schema we&#8217;ve defined.

<font color="Red">Need help with C#? Come and ask a question in our <a href="http://forum.ltd.local/viewforum.php?f=40">C# Forum</a></font>

 [1]: /index.php/DesktopDev/MSTech/the-path-to-nhibernate-aamp-tdd-part-1-t