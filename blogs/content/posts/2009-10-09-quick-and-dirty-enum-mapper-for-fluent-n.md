---
title: Quick and Dirty Enum Mapper for Fluent NHibernate
author: Alex Ullrich
type: post
date: 2009-10-10T01:54:00+00:00
excerpt: "I ran into a funny issue recently using Fluent NHibernate.  Saw that it was storing enums as Strings in the database, or more accurately storing them as MySQL's Enum Data Type.  I would prefer to store them as integers so that behavior is the same as ot&hellip;"
url: /index.php/enterprisedev/appserver/quick-and-dirty-enum-mapper-for-fluent-n/
views:
  - 20682
rp4wp_auto_linked:
  - 1
categories:
  - Application Server
  - nHibernate (.Net)

---
I ran into a funny issue recently using Fluent NHibernate. Saw that it was storing enums as Strings in the database, or more accurately storing them as MySQL&#8217;s [Enum Data Type][1]. I would prefer to store them as integers so that behavior is the same as other databases, and refactoring gets easier. Hacking around in Fluent NHibernate I found the following:

<pre>Map(x =&gt; x.Property).CustomType&lt;SomeType&gt;();</pre>

It had a slightly different name in the previous versions (I think it was CustomTypeIs), not sure if the same thing happened there (I did not notice it until using the latest). Anyway I was mapping my properties like shown above, and it seemed all was well. Until I noticed a strange thing in the SQL written to the output window. What I was expecting to see was a single select statement for the primary entity, and another three select statements to fill a couple of collections contained in the object. I saw these, and one unwelcome guest.

<pre>NHibernate: SELECT this_.Id AS Id12_6_, this_.AttemptDate AS AttemptD2_12_6_, this_.BatchSize AS BatchSize12_6_, this_.ConditioningTimeDays AS Conditio4_12_6_, this_.TotalBoilTimeMinutes AS TotalBoi5_12_6_, this_.Family AS Family12_6_, this_.FermentationTemperature AS Fermenta7_12_6_, this_.FermentationTimeDays AS Fermenta8_12_6_, this_.FinalGravity AS FinalGra9_12_6_, this_.Name AS Name12_6_, this_.Note AS Note12_6_, this_.OriginalGravity AS Origina12_12_6_, this_.SecondaryFermentationTimeDays AS Seconda13_12_6_, this_.Style AS Style12_6_, this_.Type AS Type12_6_, this_.Brewer_id AS Brewer16_12_6_, this_.Filter_id AS Filter17_12_6_, this_.Lauter_id AS Lauter18_12_6_, this_.Mash_id AS Mash19_12_6_, this_.YeastUsed_id AS YeastUsed20_12_6_, person2_.Id AS Id9_0_, person2_.About AS About9_0_, person2_.Location AS Location9_0_, person2_.DateOfBirth AS DateOfBi4_9_0_, person2_.Email AS Email9_0_, person2_.UserName AS UserName9_0_, person2_.Type AS Type9_0_, filter3_.Id AS Id2_1_, filter3_.Type AS Type2_1_, filter3_.Note AS Note2_1_, lauter4_.Id AS Id6_2_, lauter4_.SpargeType AS SpargeType6_2_, lauter4_.Volume AS Volume6_2_, lauter4_.Note AS Note6_2_, mash5_.Id AS Id16_3_, mash5_.MashInTemp AS MashInTemp16_3_, mash5_.MashOutTemp AS MashOutT3_16_3_, mash5_.Note AS Note16_3_, mash5_.Volume AS Volume16_3_, yeastuse6_.Id AS Id15_4_, yeastuse6_.Note AS Note15_4_, yeastuse6_.StarterTime AS StarterT3_15_4_, yeastuse6_.StarterUsed AS StarterU4_15_4_, yeastuse6_.YeastUsed_id AS YeastUsed5_15_4_, yeast7_.Id AS Id14_5_, yeast7_.Brand AS Brand14_5_, yeast7_.Description AS Descript3_14_5_, yeast7_.Strain AS Strain14_5_, yeast7_.Style AS Style14_5_ FROM `Recipe` this_ LEFT OUTER JOIN `Person` person2_ ON this_.Brewer_id=person2_.Id LEFT OUTER JOIN `Filter` filter3_ ON this_.Filter_id=filter3_.Id LEFT OUTER JOIN `Lauter` lauter4_ ON this_.Lauter_id=lauter4_.Id LEFT OUTER JOIN `Mash` mash5_ ON this_.Mash_id=mash5_.Id LEFT OUTER JOIN `YeastUse` yeastuse6_ ON this_.YeastUsed_id=yeastuse6_.Id LEFT OUTER JOIN `Yeast` yeast7_ ON yeastuse6_.YeastUsed_id=yeast7_.Id
NHibernate: SELECT maltsused0_.Recipe_id as Recipe8_1_, maltsused0_.Id as Id1_, maltsused0_.Id as Id8_0_, maltsused0_.AddTime as AddTime8_0_, maltsused0_.BoilTime as BoilTime8_0_, maltsused0_.Note as Note8_0_, maltsused0_.Quantity as Quantity8_0_, maltsused0_.WhenUsed as WhenUsed8_0_, maltsused0_.MaltUsed_id as MaltUsed7_8_0_ FROM `MaltUse` maltsused0_ WHERE maltsused0_.Recipe_id=?p0;?p0 = 1
NHibernate: SELECT hopsused0_.Recipe_id as Recipe9_1_, hopsused0_.Id as Id1_, hopsused0_.Id as Id4_0_, hopsused0_.AddTime as AddTime4_0_, hopsused0_.BoilTime as BoilTime4_0_, hopsused0_.HopIngredientType as HopIngre4_4_0_, hopsused0_.Note as Note4_0_, hopsused0_.Quantity as Quantity4_0_, hopsused0_.WhenUsed as WhenUsed4_0_, hopsused0_.HopUsed_id as HopUsed8_4_0_ FROM `HopUse` hopsused0_ WHERE hopsused0_.Recipe_id=?p0;?p0 = 1
NHibernate: SELECT additionsu0_.Recipe_id as Recipe8_2_, additionsu0_.Id as Id2_, additionsu0_.Id as Id1_1_, additionsu0_.AddTime as AddTime1_1_, additionsu0_.BoilTime as BoilTime1_1_, additionsu0_.Note as Note1_1_, additionsu0_.Quantity as Quantity1_1_, additionsu0_.WhenUsed as WhenUsed1_1_, additionsu0_.AdditionUsed_id as Addition7_1_1_, addition1_.Id as Id0_0_, addition1_.Brand as Brand0_0_, addition1_.Description as Descript3_0_0_, addition1_.Name as Name0_0_ FROM `AdditionUse` additionsu0_ left outer join `Addition` addition1_ on additionsu0_.AdditionUsed_id=addition1_.Id WHERE additionsu0_.Recipe_id=?p0;?p0 = 1
NHibernate: UPDATE `HopUse` SET AddTime = ?p0, BoilTime = ?p1, HopIngredientType = ?p2, Note = ?p3, Quantity = ?p4, WhenUsed = ?p5, HopUsed_id = ?p6 WHERE Id = ?p7;?p0 = 15, ?p1 = 45, ?p2 = 0, ?p3 = 'note', ?p4 = 56.6990462, ?p5 = 3, ?p6 = NULL, ?p7 = 2</pre>

Now, what the heck is that update doing there? A quick googling showed that it is a common problem associated with flushing the session. The test method shown even has a killer name: [The Ghostbuster][2]. In a nutshell it means that if something needs to be done behind the scenes that &#8220;changes&#8221; a property on your object (like converting an integer to an enum) the object is marked as dirty and when the session is flushed it will need to be updated. You can see how this would get expensive!

That&#8217;s all well and good, but how to fix it? The first thing that came to mind was a mapping convention for enums. But I ran into another problem there &#8211; the examples I saw for setting up conventions used the IProperty class, [which is not cls-compliant][3]. I&#8217;m running on Mono, so this was not an option for me. Going back to the drawing board, I remembered the IUserType from &#8220;old school&#8221; NHibernate ([ably explained here][4]).

I didn&#8217;t want to have to do this for every enum in the application &#8211; something I could use more widely was in order. Unable to find any examples (could be weak google-fu) I decided to try my own. I ended up with a generic class called &#8220;EnumMapper&#8221; implementing the IUserType interface, that looks awfully similar to Ray Houston&#8217;s example cited above. It isn&#8217;t perfect (I think I might be able to go back and clean it up some) but its not too awful I don&#8217;t think. It might just be getting too late for me, but I couldn&#8217;t think of a good way to limit it to enums. Anyway, it does its job as long as I don&#8217;t give it a bad parameter. Here it is in all its ugliness:

<pre>public class EnumMapper&lt;T&gt; : IUserType
{
    public Boolean IsMutable { get { return false; } }
    public Type ReturnedType { get { return typeof(T); } }
    public SqlType[] SqlTypes { get { return new[] { SqlTypeFactory.Int16 }; } }

    public object NullSafeGet(IDataReader rs, string[] names, object owner)
    {
        var tmp = NHibernateUtil.Int32.NullSafeGet(rs, names[0]).ToString();

        return Enum.Parse(typeof(T), tmp);
    }

    public void NullSafeSet(IDbCommand cmd, object value, int index)
    {
        if (value == null)
        {
            ((IDataParameter)cmd.Parameters[index]).Value = DBNull.Value;
        }
        else
        {
            ((IDataParameter)cmd.Parameters[index]).Value = (Int32)value;
        }
    }

    public object DeepCopy(object value)
    {
        return value;
    }

    public object Replace(object original, object target, object owner)
    {
        return original;
    }

    public object Assemble(object cached, object owner)
    {
        return cached;
    }

    public object Disassemble(object value)
    {
        return value;
    }

    public new bool Equals(object x, object y)
    {
        if (ReferenceEquals(x, y))
        {
            return true;
        }

        if (x == null || y == null)
        {
            return false;
        }

        return x.Equals(y);
    }

    public int GetHashCode(object x)
    {
        return x == null ? typeof(T).GetHashCode() : x.GetHashCode();
    }
}</pre>

I hope to clean this up eventually, but it looks like it will work for now. The mapping was changed to look like this:

<pre>Map(x =&gt; x.Property).CustomType&lt;EnumMapper&lt;SomeType&gt;&gt;();</pre>

I started up the app and checked the output again, and saw just what I was looking for:

<pre>NHibernate: SELECT this_.Id as Id12_6_, this_.AttemptDate as AttemptD2_12_6_, this_.BatchSize as BatchSize12_6_, this_.ConditioningTimeDays as Conditio4_12_6_, this_.TotalBoilTimeMinutes as TotalBoi5_12_6_, this_.Family as Family12_6_, this_.FermentationTemperature as Fermenta7_12_6_, this_.FermentationTimeDays as Fermenta8_12_6_, this_.FinalGravity as FinalGra9_12_6_, this_.Name as Name12_6_, this_.Note as Note12_6_, this_.OriginalGravity as Origina12_12_6_, this_.SecondaryFermentationTimeDays as Seconda13_12_6_, this_.Style as Style12_6_, this_.Type as Type12_6_, this_.Brewer_id as Brewer16_12_6_, this_.Filter_id as Filter17_12_6_, this_.Lauter_id as Lauter18_12_6_, this_.Mash_id as Mash19_12_6_, this_.YeastUsed_id as YeastUsed20_12_6_, person2_.Id as Id9_0_, person2_.About as About9_0_, person2_.Location as Location9_0_, person2_.DateOfBirth as DateOfBi4_9_0_, person2_.Email as Email9_0_, person2_.UserName as UserName9_0_, person2_.Type as Type9_0_, filter3_.Id as Id2_1_, filter3_.Type as Type2_1_, filter3_.Note as Note2_1_, lauter4_.Id as Id6_2_, lauter4_.SpargeType as SpargeType6_2_, lauter4_.Volume as Volume6_2_, lauter4_.Note as Note6_2_, mash5_.Id as Id16_3_, mash5_.MashInTemp as MashInTemp16_3_, mash5_.MashOutTemp as MashOutT3_16_3_, mash5_.Note as Note16_3_, mash5_.Volume as Volume16_3_, yeastuse6_.Id as Id15_4_, yeastuse6_.Note as Note15_4_, yeastuse6_.StarterTime as StarterT3_15_4_, yeastuse6_.StarterUsed as StarterU4_15_4_, yeastuse6_.YeastUsed_id as YeastUsed5_15_4_, yeast7_.Id as Id14_5_, yeast7_.Brand as Brand14_5_, yeast7_.Description as Descript3_14_5_, yeast7_.Strain as Strain14_5_, yeast7_.Style as Style14_5_ FROM `Recipe` this_ left outer join `Person` person2_ on this_.Brewer_id=person2_.Id left outer join `Filter` filter3_ on this_.Filter_id=filter3_.Id left outer join `Lauter` lauter4_ on this_.Lauter_id=lauter4_.Id left outer join `Mash` mash5_ on this_.Mash_id=mash5_.Id left outer join `YeastUse` yeastuse6_ on this_.YeastUsed_id=yeastuse6_.Id left outer join `Yeast` yeast7_ on yeastuse6_.YeastUsed_id=yeast7_.Id
NHibernate: SELECT maltsused0_.Recipe_id as Recipe8_1_, maltsused0_.Id as Id1_, maltsused0_.Id as Id8_0_, maltsused0_.AddTime as AddTime8_0_, maltsused0_.BoilTime as BoilTime8_0_, maltsused0_.Note as Note8_0_, maltsused0_.Quantity as Quantity8_0_, maltsused0_.WhenUsed as WhenUsed8_0_, maltsused0_.MaltUsed_id as MaltUsed7_8_0_ FROM `MaltUse` maltsused0_ WHERE maltsused0_.Recipe_id=?p0;?p0 = 1
NHibernate: SELECT hopsused0_.Recipe_id as Recipe8_1_, hopsused0_.Id as Id1_, hopsused0_.Id as Id4_0_, hopsused0_.AddTime as AddTime4_0_, hopsused0_.BoilTime as BoilTime4_0_, hopsused0_.Note as Note4_0_, hopsused0_.Quantity as Quantity4_0_, hopsused0_.WhenUsed as WhenUsed4_0_, hopsused0_.HopUsed_id as HopUsed7_4_0_ FROM `HopUse` hopsused0_ WHERE hopsused0_.Recipe_id=?p0;?p0 = 1
NHibernate: SELECT additionsu0_.Recipe_id as Recipe8_2_, additionsu0_.Id as Id2_, additionsu0_.Id as Id1_1_, additionsu0_.AddTime as AddTime1_1_, additionsu0_.BoilTime as BoilTime1_1_, additionsu0_.Note as Note1_1_, additionsu0_.Quantity as Quantity1_1_, additionsu0_.WhenUsed as WhenUsed1_1_, additionsu0_.AdditionUsed_id as Addition7_1_1_, addition1_.Id as Id0_0_, addition1_.Brand as Brand0_0_, addition1_.Description as Descript3_0_0_, addition1_.Name as Name0_0_ FROM `AdditionUse` additionsu0_ left outer join `Addition` addition1_ on additionsu0_.AdditionUsed_id=addition1_.Id WHERE additionsu0_.Recipe_id=?p0;?p0 = 1</pre>

The ghost update is gone! It ain&#8217;t pretty but its&#8217; getting the job done. At least until the next time I break it 🙄 If anyone knows a better way, I would love to hear it.

 [1]: http://dev.mysql.com/doc/refman/5.0/en/enum.html
 [2]: http://nhforge.org/blogs/nhibernate/archive/2008/10/20/how-test-your-mappings-the-ghostbuster.aspx
 [3]: http://stackoverflow.com/questions/729456/argument-type-fluentnhibernate-mapping-iproperty-is-not-cls-compliant
 [4]: http://www.lostechies.com/blogs/rhouston/archive/2008/03/23/mapping-strings-to-booleans-using-nhibernate-s-iusertype.aspx