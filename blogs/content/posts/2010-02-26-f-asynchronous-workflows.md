---
title: 'F# Asynchronous Workflows'
author: chaospandion
type: post
date: 2010-02-26T17:00:07+00:00
url: /index.php/desktopdev/mstech/f-asynchronous-workflows/
views:
  - 10453
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - 'f#'

---
Asynchronous work flows are a very powerful tool in programming. They allow your threads to do other work while you wait for results from a long running piece of work. How would you write an asynchronous work flow in C#? Logically you might consider chaining together callbacks.

<pre>&lt;code&gt;
&lt;span class="MT_blue"&gt;new&lt;/span&gt; Client().Connect(settings, (c1, ex1) =&gt; {
    &lt;span class="MT_green"&gt;// prepare data to send&lt;/span&gt;
    c.Send(sendData1, (c2, ex2) =&gt; {
        c2.Receive((c3, dt1, ex3) =&gt; {
            &lt;span class="MT_green"&gt;// parse data and prepare response&lt;/span&gt;
            c3.Send(sendData2, (c4, ex4) =&gt; {
                c4.Disconnect((c5, ex5) =&gt; {
                    c5.Dispose();
                });
            });
        });
    });
});
&lt;/code&gt;</pre>

There is a good reason you never see code like this. For one, it is highly indented and this makes it a bit awkward to read. More importantly, this approach puts your code at risk of a great deal of name conflicts, as the compiler will create a closure for each lambda and capture all of the values in the previous levels. This means you have to come up with a different name for the parameters in each lambda. Resolving this issue could involve creating an additional class to contain the necessary methods, potentially complicating a task that seems like it should be simple. This is where F# asynchronous work flows come to play.

<pre>&lt;code&gt;
async {
    &lt;span class="MT_blue"&gt;try&lt;/span&gt;
        &lt;span class="MT_blue"&gt;use&lt;/span&gt; client = &lt;span class="MT_blue"&gt;new&lt;/span&gt; Client()
        &lt;span class="MT_blue"&gt;do!&lt;/span&gt; client.Connect settings
        &lt;span class="MT_green"&gt;// prepare data to send&lt;/span&gt;
        &lt;span class="MT_blue"&gt;do!&lt;/span&gt; client.Send sendData1
        &lt;span class="MT_blue"&gt;let!&lt;/span&gt; dt1 = client.Receive ()
        &lt;span class="MT_green"&gt;// parse data and prepare response&lt;/span&gt;
        &lt;span class="MT_blue"&gt;do!&lt;/span&gt; client.Send sendData2
        &lt;span class="MT_blue"&gt;do!&lt;/span&gt; client.Disconnect ()
    &lt;span class="MT_blue"&gt;with&lt;/span&gt;
    | ex -&gt;
        &lt;span class="MT_green"&gt;// handle exception&lt;/span&gt;       
} |&gt; Asnyc.Start &lt;span class="MT_green"&gt;// run in the thread pool&lt;/span&gt; 
&lt;/code&gt;</pre>



This is almost a literal translation of the C# except for one thing. Notice how the C# version threads through an exception parameter? In F# you have first class error handling in asynchronous work flows so the first failure will will call your exception handling code.

The first class support for asynchronous work flows is an amazing feature of F#. It allows you to create simple elegant code right where you need it. Think about some of your synchronous projects that just don&#8217;t have the performance you need and consider F# as a powerful solution to you problem.

[F# &#8211; Microsoft Research][1]
  
[F# &#8211; MSDN][2]

 [1]: http://research.microsoft.com/en-us/um/cambridge/projects/fsharp/
 [2]: http://msdn.microsoft.com/en-us/fsharp/default.aspx