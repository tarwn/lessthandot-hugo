---
title: 'Cycle Detection in F# – Fun With Immutability'
author: Alex Ullrich
type: post
date: 2011-04-11T13:00:00+00:00
ID: 1102
excerpt: "I've been going through a lot of the problems from Project Euler in F#, and finding myself needing to change my way of thinking more and more often.  I recently finished doing one problem that required use of a Cycle Detection algorithm, and as the solu&hellip;"
url: /index.php/desktopdev/mstech/cycle-detection-in-f-fun-with-immutability/
views:
  - 6711
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies

---
I&#8217;ve been going through a lot of the problems from [Project Euler][1] in F#, and finding myself needing to change my way of thinking more and more often. I recently finished doing one problem that required use of a [Cycle Detection][2] algorithm, and as the solution evolved I thought it would be a good example of writing immutable code.

### Algorithm Being Examined

For the example, I&#8217;ll use Brent&#8217;s variation on the tortoise-and-hare algorithm described at the link because it&#8217;s a little shorter. The basic idea of the tortoise-and-hare algorithm is basically what you&#8217;d expect, a function is repeatedly applied (at different rates) to a value. The way the value changes in response can help to identify patterns in the function&#8217;s behavior. If you&#8217;d like to know more about it, check out the wikipedia page. This algorithm is demonstrated with the following code:

```python
def brent(f, x0):
    # main phase: search successive powers of two
    power = lam = 1
    tortoise = x0
    hare = f(x0) # f(x0) is the element/node next to x0.
    while tortoise != hare:
        if power == lam:   # time to start a new power of two?
            tortoise = hare
            power *= 2
            lam = 0
        hare = f(hare)
        lam += 1
 
    # Find the position of the first repetition of length lambda
    mu = 0
    tortoise = hare = x0
    for i in range(lam):
    # range(lam) produces a list with the values 0, 1, ... , lam-1
        hare = f(hare)
    while tortoise != hare:
        tortoise = f(tortoise)
        hare = f(hare)
        mu += 1
 
    return lam, mu
```
The tuple returned contains the cycle length (lam) and the point at which the cycle starts (mu). A literal translation of this code to F# looks like this:

```fsharp
let brent f x =
    let mutable power = 1
    let mutable lam = 1
    let mutable tortoise = x
    let mutable hare = f x 

    while not (tortoise = hare) do
        if power = lam then
            tortoise <- hare
            power <- power * 2
            lam <- 0
        hare <- f hare
        lam <- lam + 1

    let mutable mu = 0
    tortoise <- x
    hare <- x

    for i in [1 .. lam] do
        hare <- f hare

    while not (tortoise = hare) do
        tortoise <- f tortoise
        hare <- f hare
        mu <- mu + 1

    lam, mu
```

This code isn&#8217;t necessarily bad, and actually performed quite well for my purpose (at least compared to the nasty C# string hacks I used the last time I tried this one). It doesn&#8217;t feel like idiomatic functional code though, primarily due to the heavy use of mutable state. Immutability is a big part of functional programming, because it leads to side-effect free code. This is (in theory) less likely to contain bugs, and easier to run in parallel. My friend [Matt][3] put it quite well, saying that immutability for him is all about peace of mind.
  


> Who cares if I don&#8217;t synchronize access to the object! It&#8217;s immutable!</p>
With all this in mind, once I had the problem solved I decided to see how hard it&#8217;d be to implement the same algorithm without any mutable values &#8211; I&#8217;m not sure if it will end up as idiomatic F# or not, but I hope it&#8217;s at least a step in the right direction.

### Getting Rid of Mutable State

When programming in a functional style, recursion is one of the most powerful tools we&#8217;ve got for avoiding mutable state, and the complications that come with it. The F# compiler can perform [tail call optimization][4], which can help avoid the stack overflows you can run into with normal recursive methods. Looking over the code above, the prime offenders for using mutable state appear to be the two main loops opened with 

```python
while tortoise != hare:
```

So let&#8217;s look at how these can be converted to recursive functions. The upper loop is probably the most complex because of the if statement it contains, and it can be converted to something like the following:

```fsharp
let rec applyUntilEqual (power, lam, tortoise, hare) =
    if not (tortoise = hare) then
        if (power = lam) then
            applyUntilEqual (power * 2, 0, hare, hare)
        else 
            applyUntilEqual (power, lam + 1, tortoise, f hare)
    else
        (power, lam, tortoise, hare)
```

Note that we don&#8217;t need to pass &#8216;f&#8217; (the function passed into our brent implementation) into this function. Because this method is declared inside the brent function, it will have access to all of the data passed in. In all cases except when tortoise and hare are equal, the function calls itself with altered values. The first half of the inner if statement handles the preprocessing that is done when power and lam are equal &#8211; multiplying the power by 2 and replacing the tortoise with the hare. The second half handles the normal processing, incrementing lam and applying the function to the hare. The final case simply returns the same tuple that it receives. 

This is a step in the right direction, but it still seems to have 1 branch too many. So let&#8217;s take a look at what we&#8217;re really trying to do with this recursion. It&#8217;s not necessarily the processing of values, but the while loop itself. With a few changes we can make the method focus a bit more on what we need it to do. The most important change is to allow it to take another function as an argument.

```fsharp
let rec applyUntilEqual func (power, lam, mu, tortoise, hare) =
    if (tortoise = hare) then
        (power, lam, mu, tortoise, hare)
    else
        applyUntilEqual func (func (power, lam, mu, tortoise, hare))
```

This sure does look simpler, but surprisingly free of any interesting logic. As is so often the case, this is where things really get interesting. If tortoise and hare are equal, we simply return the tuple that was passed in. If they aren&#8217;t, then the function calls itself with the input tuple, as transformed by the function passed in. The &#8216;func&#8217; parameter that we added can carry the logic we want to apply on each pass through our &#8216;loop&#8217;. The following snippet shows the implementation of the first bit of logic, along with the way it can be called from code:

```fsharp
let phaseOne (power, lam, mu, tortoise, hare) =
    if (power = lam) then
        (power * 2, 1, mu, hare, (f hare))
    else 
        (power, lam + 1, mu, tortoise, (f hare))

let phaseOneResult = applyUntilEqual func (<tuple containing data>)
```

Because we don&#8217;t need to check whether tortoise and hare are equal, we can write this in a single if statement. This frees us to short-circuit things a little bit, accomplishing both the pre and post processing in the (power = lam) condition, so this could save us a few passes through the function. Notice this function includes the &#8216;mu&#8217; parameter, even though it is not used yet. This is because by adding that parameter, we can use the same &#8216;loop&#8217; function for both of the main loops in the algorithm. 

It takes the same bundle of data (even though it now only uses mu, tortoise and hare). The reason for this goes beyond being able to share the loop function, as we&#8217;ll see in a second. All we have to do this time is apply the function to tortoise and hare, and increment mu. 

```fsharp
let phaseTwo (power, lam, mu, tortoise, hare) =
    (power, lam, mu + 1, f tortoise, f hare)
```

Finally, we need to implement the loop that starts with _for i in range(lam):_ in the python code. This code really only needs to execute the function provided _(lam &#8211; 1)_ times. It&#8217;s not too difficult, but slightly interesting.

```fsharp
let advanceHare (power, lam, mu, tortoise, hare) =
    let rec advance times h =
        if times = 1 then
            hare
        else 
            advance(times - 1) (f h)
    (power, lam, mu, tortoise, advance lam hare)
```

The interesting part is the inner &#8216;advance&#8217; function. It only takes the number of times to execute and the current hare value. This allows us to take (and return) the same package of data that the other functions expect from the outer function. So, after tying up some loose ends we should have eliminated all of the mutable state. But lets look at the reason I alluded to in the last paragraph for passing the same bundle of data through all functions. 

### Aside: Pipeline Operators

One of my favorite features in F# is the forward pipe operator (and the less oft-used backward pipe operator), a pair of operators designed to let you string together a series of function calls. The forward pipe operator can be explained with a simple example of a function to multiply two numbers, and another function that calls it to multiply a single number by ten:

```fsharp
let multiply x y = x * y
let x10 x = multiply 10 x
```

Groundbreaking, I know. But it can be rewritten using the forward pipe operator like so:

```fsharp
let multiply x y = x * y
let x10 x = x |> multiply 10 
```

At least syntactically speaking, this transforms the code from the _function (arg1, arg2)_ syntax that&#8217;s common in most languages to something like _arg1 |> function arg2_. You can chain these together, with the operator always receiving the output of the previous function, forming a nice clean &#8216;pipeline&#8217;. So if we wanted to get really crazy, an x100 function could be written like so: _let x100 = x |> multiply 10 |> multiply 10_. This doesn&#8217;t seem that impressive with such a simple example, but it really shines when trying to compose complex functions from simple building blocks. My personal favorite use for the pipeline operator is applying a chain of functions to a lazily evaluated sequence.

Luckily, we can use the code we&#8217;ve put together to show the pipeline operator in action. Starting with a tuple containing our inputs, we can get the result we need by chaining the simple methods we&#8217;ve created together in order.

```fsharp
(1, 1, 0, x, f x) 
    |> applyUntilEqual phaseOne
    |> accelerateHare
    |> applyUntilEqual phaseTwo
    |> fun (_, lam, mu, _, _) -> lam, mu
```

This is some pretty dense code, but if I&#8217;ve done a decent job explaining it you should be able to read it alright. Notice the code following the last pipe operator &#8211; for this one we are simply using a lambda expression to extract the two values we need from our bundle of data. This is a simple operation, but it also shows that we can apply arbitrary lambda expressions as well as defined functions using the pipeline operator. I think it reads a little better with the predefined functions, though if there wasn&#8217;t any conditional logic in the short functions we have I&#8217;d probably favor using lambdas (except for the applyUntilEqual function). The original and immutable implementations are included in the attached file if you&#8217;d like to take a closer look.

### Conclusion &#8211; Immutability Comes at a Cost

So, what did this get us? I hope that it got us closer to idiomatic F# code, though I&#8217;m not the best judge of that. It undoubtedly succeeded in breaking the dependence on mutable state to implement the algorithm. This came at a cost though. In my simple use case, looking for cycles when applying a pair of functions to 1000 numeric inputs, execution time increases by about 50%. Both implementations are quick, but the one using mutable state is noticeably quicker. This wasn&#8217;t exactly a scientific test but it does appear consistent when I run on a few different machines with wildly varying specs. Because the mutable state is contained within the method I suspect that both implementations would both run in parallel without much problem.

This leaves me suspecting that careful, localized use of mutable state can be valuable when attempting to improve the performance of F# code. But it&#8217;s probably best to shoot for immutability first. As always I&#8217;d love to see ways to make it perform or read better &#8211; I can&#8217;t shake the feeling that I missed something.

 [1]: http://projecteuler.net/
 [2]: http://en.wikipedia.org/wiki/Cycle_detection
 [3]: /index.php/All/?disp=authdir&author=225
 [4]: http://en.wikipedia.org/wiki/Tail_recursion