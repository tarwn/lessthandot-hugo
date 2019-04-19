---
title: Visual Studio 2010 Concurrency Profiling And Parallel Programming
author: SQLDenis
type: post
date: 2010-03-14T11:03:56+00:00
ID: 726
excerpt: "Visual Studio 2010 and .NET 4.0 are almost released, one of the new things that ship with this release is Parallel Programming. Since you can't buy a machine anymore with just one core it is time that we developers get intimate with concurrent programmi&hellip;"
url: /index.php/architect/enterprisearchitecture/visual-studio-2010-concurrency-profiling/
views:
  - 14413
rp4wp_auto_linked:
  - 1
categories:
  - Enterprise Architecture
tags:
  - 'c#'
  - concurrency
  - multi core
  - parallel programming
  - pling
  - profiling

---
Visual Studio 2010 and .NET 4.0 are almost released, one of the new things that ship with this release is Parallel Programming. Since you can't buy a machine anymore with just one core it is time that we developers get intimate with concurrent programming. I decided to play around with this a little today, this is not a real technical post, I mostly show you how you can get started and what new tools are available.

## Getting to know Parallel Programming with the .NET Framework 4

The best way to learn about new additions to a framework is to look at some code. There are 22 samples for Parallel Programming with the .NET Framework 4 available on the msdn code library. You can download the .NET Framework 4 Parallel Programming samples here: [Samples for Parallel Programming with the .NET Framework 4][1]

Here is a screen shot of the Mandelbrot Fractals sample.
  
<img src="/wp-content/uploads/blogs/Architect//Fractal.png" alt="" title="" width="407" height="405" />

If you run the sample on a 2 core machine you will see that the parallel execution is about 60% faster than the sequential one.

After I was done with the Mandelbrot Fractals sample, I decided to open up the ComputePi sample, after all it is Pi day today (2010.3.14)

Here is the code for the different ways of calculating Pi which is part of the ComputePi sample.

**Estimates the value of PI using a LINQ-based implementation**.

```csharp
static double SerialLinqPi()
    {
        double step = 1.0 / (double)num_steps;
        return (from i in Enumerable.Range(0, num_steps)
                let x = (i + 0.5) * step
                select 4.0 / (1.0 + x * x)).Sum() * step;
    }
```
**Estimates the value of PI using a PLINQ-based implementation.**

```csharp
static double ParallelLinqPi()
    {
        double step = 1.0 / (double)num_steps;
        return (from i in ParallelEnumerable.Range(0, num_steps)
                let x = (i + 0.5) * step
                select 4.0 / (1.0 + x * x)).Sum() * step;
    }

```
**Estimates the value of PI using a for loop.**

```csharp
static double SerialPi()
    {
        double sum = 0.0;
        double step = 1.0 / (double)num_steps;
        for (int i = 0; i < num_steps; i++)
        {
            double x = (i + 0.5) * step;
            sum = sum + 4.0 / (1.0 + x * x);
        }
        return step * sum;
    }

```
**Estimates the value of PI using a Parallel.For.**

```csharp
static double ParallelPi()
    {
        double sum = 0.0;
        double step = 1.0 / (double)num_steps;
        object monitor = new object();
        Parallel.For(0, num_steps, () => 0.0, (i, state, local) =>
        {
            double x = (i + 0.5) * step;
            return local + 4.0 / (1.0 + x * x);
        }, local => { lock (monitor) sum += local; });
        return step * sum;
    }

```
**Estimates the value of PI using a Parallel.ForEach and a range partitioner.**

```csharp
static double ParallelPartitionerPi()
    {
        double sum = 0.0;
        double step = 1.0 / (double)num_steps;
        object monitor = new object();
        Parallel.ForEach(Partitioner.Create(0, num_steps), () => 0.0, (range, state, local) =>
        {
            for (int i = range.Item1; i < range.Item2; i++)
            {
                double x = (i + 0.5) * step;
                local += 4.0 / (1.0 + x * x);
            }
            return local;
        }, local => { lock (monitor) sum += local; });
        return step * sum;
    }
}
```

Here are the results when you run the sample. The first column is the time in seconds and the second column has the method name.

<pre>03.3369869: SerialLinqPi
02.1516130: ParallelLinqPi
01.0522855: SerialPi
00.6457441: ParallelPi
00.7098180: ParallelPartitionerPi</pre>

Again these were from my dual core laptop, you might get a bigger difference if you run it on a quad core box.

## Profiling Performance

Visual Studio 2010 ships with a couple of tools that will make your life easier if you do parallel programming. Launch the Performance Wizard from Tools–>Launch Performance Wizard
  
  
<img src="/wp-content/uploads/blogs/Architect//LaunchWizard.PNG" alt="" title="" width="479" height="217" />

After that a wizard will launch and you will see a window like the one in the sreenshot below
  
  
<img src="/wp-content/uploads/blogs/Architect//Profile Wizard.png" alt="" title="" width="647" height="556" />

Pick concurrency and visualize the behaviour of a multithreaded application.

After you are done, start your app and when you close the app, reports will be generated.
  
If you don't see the reports then do the following; click on View–>Other Windows–>Performance Explorer. 

FYI, You need to run as admin to generate these report and if you are on a 64bit machine then you need to set the platform target to x86 in order to be able to generate these reports. I was greeted with the following message: _To enable complete call stacks on x64 platforms, executive paging must be disabled. A reboot is then required. To make this change, click “Yes”, save your work, and then reboot. For more information, see http://go.microsoft.com/fwlink/?LinkId=157265_ . After I rebooted everything worked.

There are 3 types of reports that you will see. Here is what the CPU Utilization report looks like.
  
<img src="/wp-content/uploads/blogs/Architect//CPUUtil.PNG" alt="" title="" width="459" height="293" />

There is a report for threads
  
<img src="/wp-content/uploads/blogs/Architect//Threads.PNG" alt="" title="" width="741" height="596" />

Finally there is also a report for cores
  
<img src="/wp-content/uploads/blogs/Architect//Cores.PNG" alt="" title="" width="662" height="522" />

Instead of having 20 images embedded I decided that a video would be more useful. This video is about 1 minute and 52 seconds and it shows you what the tool looks like when I am clicking around in it.

Here is the HD Video version, I would suggest you click on the video and watch in on YouTube in 720P full screen format
  
[video:youtube:Bofk3ecJOtk]



## Learning more about Concurrency Profiling and Parallel Programming

To finalize this post, here are some links to technical resources that will help you with Concurrency Profiling and Parallel Programming.

The [Visual Studio Performance Tools (Profiler) msdn forum][2]

The [Parallel Computing, Profiling and Debugging blog][3]

Below is a 10 part blog post series by Reed Copsey, Jr about parallelism in .net

<a title=Introduction href="http://reedcopsey.com/2010/01/19/parallelism-in-net-introduction/">Introduction</a>

[Part 1, Decomposition][4]<u><font color=#0066cc>&nbsp;</font></u>

[Part 2, Simple Imperative Data Parallelism][5]

[Part 3, Imperative Data Parallelism: Early Termination][6]

[Part 4, Imperative Data Parallelism: Aggregation][7]

[Part 5, Partitioning of Work][8]

[Part 6, Declarative Data Parallelism][9]

[Part 7, Some Differences between PLINQ and LINQ to Objects][10]

[Part 8, PLINQ's ForAll Method][11]

[Part 9, Configuration in PLINQ and TPL][12]

[Part 10, Cancellation in PLINQ and the Parallel class][13]

So hopefully this post will spark your interest and you will take a look at these interesting technologies and tools.

Happy &#960; day ,and today is also Albert Einstein's birthday.

<img src="/wp-content/uploads/blogs/Architect//Pi_Pie.jpg" alt="" title="" width="577" height="576" />

 [1]: http://code.msdn.microsoft.com/ParExtSamples
 [2]: http://social.msdn.microsoft.com/forums/en-us/vstsprofiler
 [3]: http://blogs.msdn.com/visualizeparallel/
 [4]: http://reedcopsey.com/2010/01/19/parallelism-in-net-part-1-decomposition/ "Part 1, Decomposition"
 [5]: http://reedcopsey.com/2010/01/20/parallelism-in-net-part-2-simple-imperative-data-parallelism/
 [6]: http://reedcopsey.com/2010/01/22/parallelism-in-net-part-3-imperative-data-parallelism-early-termination/
 [7]: http://reedcopsey.com/2010/01/22/parallelism-in-net-part-4-imperative-data-parallelism-aggregation/
 [8]: http://reedcopsey.com/2010/01/26/parallelism-in-net-part-5-partitioning-of-work/
 [9]: http://reedcopsey.com/2010/01/26/parallelism-in-net-part-6-declarative-data-parallelism/
 [10]: http://reedcopsey.com/2010/01/28/parallelism-in-net-part-7-some-differences-between-plinq-and-linq-to-objects/
 [11]: http://reedcopsey.com/2010/02/03/parallelism-in-net-part-8-plinqs-forall-method/
 [12]: http://reedcopsey.com/2010/02/11/parallelism-in-net-part-9-configuration-in-plinq-and-tpl/
 [13]: http://reedcopsey.com/2010/02/17/parallelism-in-net-part-10-cancellation-in-plinq-and-the-parallel-class/