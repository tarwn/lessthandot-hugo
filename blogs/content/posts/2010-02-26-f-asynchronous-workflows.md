---
title: 'F# Asynchronous Workflows'
author: chaospandion
type: post
date: 2010-02-26T17:00:07+00:00
ID: 713
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

    
    <span class="MT_blue">new</span> Client().Connect(settings, (c1, ex1) => {
        <span class="MT_green">// prepare data to send</span>
        c.Send(sendData1, (c2, ex2) => {
            c2.Receive((c3, dt1, ex3) => {
                <span class="MT_green">// parse data and prepare response</span>
                c3.Send(sendData2, (c4, ex4) => {
                    c4.Disconnect((c5, ex5) => {
                        c5.Dispose();
                    });
                });
            });
        });
    });
    

There is a good reason you never see code like this. For one, it is highly indented and this makes it a bit awkward to read. More importantly, this approach puts your code at risk of a great deal of name conflicts, as the compiler will create a closure for each lambda and capture all of the values in the previous levels. This means you have to come up with a different name for the parameters in each lambda. Resolving this issue could involve creating an additional class to contain the necessary methods, potentially complicating a task that seems like it should be simple. This is where F# asynchronous work flows come to play.

    
    async {
        <span class="MT_blue">try</span>
            <span class="MT_blue">use</span> client = <span class="MT_blue">new</span> Client()
            <span class="MT_blue">do!</span> client.Connect settings
            <span class="MT_green">// prepare data to send</span>
            <span class="MT_blue">do!</span> client.Send sendData1
            <span class="MT_blue">let!</span> dt1 = client.Receive ()
            <span class="MT_green">// parse data and prepare response</span>
            <span class="MT_blue">do!</span> client.Send sendData2
            <span class="MT_blue">do!</span> client.Disconnect ()
        <span class="MT_blue">with</span>
        | ex ->
            <span class="MT_green">// handle exception</span>       
    } |> Asnyc.Start <span class="MT_green">// run in the thread pool</span> 
    



This is almost a literal translation of the C# except for one thing. Notice how the C# version threads through an exception parameter? In F# you have first class error handling in asynchronous work flows so the first failure will will call your exception handling code.

The first class support for asynchronous work flows is an amazing feature of F#. It allows you to create simple elegant code right where you need it. Think about some of your synchronous projects that just don't have the performance you need and consider F# as a powerful solution to you problem.

[F# &#8211; Microsoft Research][1]
  
[F# &#8211; MSDN][2]

 [1]: http://research.microsoft.com/en-us/um/cambridge/projects/fsharp/
 [2]: http://msdn.microsoft.com/en-us/fsharp/default.aspx