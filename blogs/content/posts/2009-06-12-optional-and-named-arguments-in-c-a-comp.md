---
title: 'Optional and named arguments in C# a comparison with SQL Server stored procs'
author: SQLDenis
type: post
date: 2009-06-12T13:29:57+00:00
ID: 467
url: /index.php/desktopdev/mstech/optional-and-named-arguments-in-c-a-comp/
views:
  - 4627
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - .net 4.0
  - 'c#'
  - visual studio 2010

---
I was thinking about writing a post about _how C# is morphing into SQL_ because I noticed that C# has added named and optional arguments. Remember the [Compound Operators Or How T-SQL Is Morphing Into VB Or C#][1]? This is basically the same kind of thing only it goes into the other direction. 

For some bizarre reason Chrissie had the same idea yesterday and posted the following post: [What so special about Optional/named parameters][2]. From that article you will learn that VB already had this back in the vb4 days.

So let's get started. First the SQL Server version
  
Create the following stored procedure which has 3 optional parameters

sql
create procedure OptionalTest
@Param1 varchar(10),
@Param2 varchar(10) = 'Two',
@Param3 varchar(10) = 'Three',
@Param4 varchar(10) = NULL

as
begin
	print '@Param1  = ' + @Param1
	print '@Param2  = ' + @Param2
	print '@Param3  = ' + @Param3
	print '@Param4  = ' + coalesce(@Param4,'')
end
```

Execute it with just the first parameter

sql
exec OptionalTest 'bla'
```

Here is the output
  
@Param1 = bla
  
@Param2 = Two
  
@Param3 = Three
  
@Param4 = 

When executing it with values for the first two parameters the output for the 2nd parameter changes

sql
exec OptionalTest 'bla' ,'Twelve'
```

@Param1 = bla
  
@Param2 = Twelve
  
@Param3 = Three
  
@Param4 = 

When executing with three parameters the third value changes

sql
exec OptionalTest 'bla' ,'Twelve','Thirsty'
```

@Param1 = bla
  
@Param2 = Twelve
  
@Param3 = Thirsty
  
@Param4 = 

And with four parameters all the values that are passed in are printed

sql
exec OptionalTest 'bla' ,'Twelve','Thirsty','Forty'
```

@Param1 = bla
  
@Param2 = Twelve
  
@Param3 = Thirsty
  
@Param4 = Forty

If we pass in the first parameter and we olso want to pass in the third parameter we need to use named parameters, in this case we do this @Param3 = 'Thirsty'

sql
exec OptionalTest 'bla' ,@Param3 = 'Thirsty'
```

@Param1 = bla
  
@Param2 = Two
  
@Param3 = Thirsty
  
@Param4 = 

If you want to pass in the first and the fourt parameter then you need to name the fourth one

sql
exec OptionalTest 'bla' ,@Param4 = 'Thirsty'
```

@Param1 = bla
  
@Param2 = Two
  
@Param3 = Three
  
@Param4 = Thirsty

Omitting parameters won't run or parse for that matter

sql
exec OptionalTest 'bla' ,,,'Thirsty'
```

Server: Msg 102, Level 15, State 1, Line 1
  
Incorrect syntax near ','.

You can use nulls but as you can see from the output nothing gets printed at all for paraeter 2 or 3, this is because when you concatenate a NULL value with something else you get nothing back

sql
exec OptionalTest 'bla' ,null,null,'Thirsty'
```

@Param1 = bla

@Param4 = Thirsty

Now we can take a look at the c# version, the c# version is almost identical except that the first parameter is optional where this is not the case with the proc

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            Program p = new Program();
            Console.WriteLine("p.OptionalTest(bla);");
            p.OptionalTest("bla");

            Console.WriteLine("p.OptionalTest(bla,twelve)");
            p.OptionalTest("bla","Twelve");

            Console.WriteLine("p.OptionalTest(bla,twelve,Thirsty)");
            p.OptionalTest("bla", "Twelve","Thirsty");

            Console.WriteLine("p.OptionalTest(bla,twelve,Thirsty,Forty)");
            p.OptionalTest("bla", "Twelve", "Thirsty","Forty");

            Console.WriteLine("p.OptionalTest(bla,param3:Thirsty)");
            p.OptionalTest("bla", "Twelve", param3: "Thirsty");

            Console.WriteLine("p.OptionalTest(bla,param4:Thirsty)");
            p.OptionalTest("bla", "Twelve", param4: "Thirsty");

            //p.OptionalTest("bla", ,, "Thirsty");
            //argument missing won't compile

            Console.WriteLine("p.OptionalTest(bla,null,null,Thirsty)");
            p.OptionalTest("bla",null,null, "Thirsty");
            Console.ReadLine();

            
        }

        public int OptionalTest(string param1 = "2", string param2 = "two", string param3 = "three", string param4 = null)
        {
            Console.WriteLine("param1 = " + param1);
            Console.WriteLine("param2 = " + param2);
            Console.WriteLine("param3 = " + param3);
            Console.WriteLine("param4 = " + param4);
            Console.WriteLine("nr");
            return 0;
        }
    }
}
```

Here is the output from those calls

p.OptionalTest(“bla”);
              
param1 = bla
              
param2 = two
              
param3 = three
              
param4 =

p.OptionalTest(“bla”,”twelve”)
              
param1 = bla
              
param2 = Twelve
              
param3 = three
              
param4 =

p.OptionalTest(“bla”,”twelve”,”Thirsty”)
              
param1 = bla
              
param2 = Twelve
              
param3 = Thirsty
              
param4 =

p.OptionalTest(“bla”,”twelve”,”Thirsty”,”Forty”)
              
param1 = bla
              
param2 = Twelve
              
param3 = Thirsty
              
param4 = Forty

p.OptionalTest(“bla”,param3:”Thirsty”)
              
param1 = bla
              
param2 = Twelve
              
param3 = Thirsty
              
param4 =

p.OptionalTest(“bla”,param4:”Thirsty”)
              
param1 = bla
              
param2 = Twelve
              
param3 = three
              
param4 = Thirsty

p.OptionalTest(“bla”,null,null,”Thirsty”)
              
param1 = bla
              
param2 =
              
param3 =
              
param4 = Thirsty

As you can see there is almost no difference between SQL and C# when calling with named parameters

exec OptionalTest 'bla' ,@Param4 = 'Thirsty'
  
p.OptionalTest(“bla”,param4:”Thirsty”)

Now you might ask yourself why do we need this? Have you ever called Office COM components? Here is what you would do now

```csharp
word.Documents.Add()
```

instead of this

```csharp
word.Documents.Add(ref oTemplate, ref missing, ref missing, ref
isVisible);
```

Some of these things might take up to 50 parameters and most of them might be optional, in a case like that optional and named parameters are great!!

If you want to watch a bunch of videos about Visual Studio 2010 then check out the following link: [Learn Visual Studio 2010 now by watching these videos][3] In that post I have collected over 20 videos that will help you learn the new stuff in Visual Studio 2010 and the .NET framework 4.0

If you want to learn more about c# 4.0 check out the following video with Anders Hejlsberg
  
http://channel9.msdn.com/posts/VisualStudio/C-40-Questions-and-reasons-behind-the-answers/

Also worthwhile [C# 4.0 support for covariance and contravariance][4] and [New C# 4.0 Features Paper][5]

 [1]: /index.php/DataMgmt/DataDesign/compound-operators-or-how-t-sql-is-morph
 [2]: /index.php/DesktopDev/MSTech/what-so-special-about-optional-named-par
 [3]: /index.php/WebDev/WebDesignGraphicsStyling/learn-visual-studio-2010-now-by-watching
 [4]: http://blogs.msdn.com/charlie/archive/2008/10/28/linq-farm-covariance-and-contravariance-in-visual-studio-2010.aspx
 [5]: http://code.msdn.microsoft.com/Release/ProjectReleases.aspx?ProjectName=csharpfuture&ReleaseId=1686&wa=wsignin1.0