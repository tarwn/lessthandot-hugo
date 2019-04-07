---
title: 'Danger in Design: Why bother with Architecture ?'
author: damber
type: post
date: 2008-06-30T10:36:11+00:00
ID: 53
url: /index.php/architect/hardwareinfrastructuredesign/why-bother-with-architecture/
views:
  - 15153
rp4wp_auto_linked:
  - 1
categories:
  - Designing Multi-Application Solutions
  - Designing Software
  - Enterprise Architecture
  - Hardware and Infrastructure Design
  - Information and Integration Architecture
  - Introduction to Architecture and Design
tags:
  - architecture
  - methodologies
  - rationale

---
Creativity is a wonderful thing. It&#8217;s also something different for each of us, which is why sometimes our perspectives on the world can produce conflicting ideas on what is the right way and the wrong way to do things. This is a very common facet of the IT world, in particular making computer software, solutions and services. 

# We don&#8217;t need architects! &#8230;Do we ?

It&#8217;s important for us to remember that we develop software to &#8220;do something&#8221; that we want it to do. In which case we need to continually review the success of that software or solution by evaluating it&#8217;s ability to meet our needs. But who gets to say what those needs are, and how well they are met? More importantly, what makes IT solutions good or bad ? Is it how big they are ? Or how easy to use they are ? Or maybe how efficiently they operate ? Or what about how well measured and managed they are ? Or even how easy it is to change the way they work ? What about all of the above, and much more&#8230;?

Getting to a point where a) you understand what success looks like, and b) you have a solution that delivers that success, can be achieved by using an Architect to facilitate and coordinate the construction of a design and resulting solution. But that&#8217;s not always what happens. Apart from the many varying definitions of an architect&#8217;s role (and the different types of architects.. e.g. Enterprise Architect, Solutions Architect, Application Architect, Technical Architect,etc), there&#8217;s also often a perception from developers that architects are generally away with the fairies, dreaming up blue sky designs that are just not grounded in reality or worthwhile bothering with. Additionally, a lot of businesses (too many unfortunately), just don&#8217;t understand the value of using an architect&#8230; Why pay someone to design the solution, when you&#8217;ve got business systems analysts and developers that can work it out between themselves ?



# Winchester Mystery House, San Jose, CA

Why indeed&#8230; well, I thought it may be useful to post a little analogy with the &#8216;real world&#8217; &#8211; where architects and builders have been working together for many years to produce wonderful (and not so wonderful) buildings.

Firstly, let me introduce the Winchester Mystery House, of the Winchester Guns family fame:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/Architect/images/winchesterhouse.png" alt="" title="" width="440" height="286" />
</div>



## Functionally Rich

  * 160 Rooms
  * 47 Fireplaces
  * 17 Chimneys
  * 6 kitchens
  * 10,000 windows



## State of the Art Technology (at the time)

  * Wall Insulation
  * Push button gas lights
  * No-clog sink patent
  * Intercoms
  * Modern Heating & Sewage



## Total Cost

<p style="font-size: 1em;">
  $5.5m (~<span style="font-weight:800;">$300m</span> today) over <span style="font-weight: 800;">38 years</span> (1884 &#8211; 1922)
</p>



## But&#8230; <span style="color:#ff0000;">No Interoperability</span>

  * 65 doors to blank walls
  * 13 abandoned staircases
  * 24 skylights in floors
  * Rooms &#8220;build-around&#8221; other rooms
  * Enough keys to fill 2 large buckets



Ouch.



## Due to the division of labour

  * Builders: 147
  * Architects: <span style="font-weight:800;text-decoration:underline;"></span>



## Sound Familiar ?

We&#8217;ve all worked on a project with something like this. Usually because the system wasn&#8217;t really &#8216;designed&#8217; it just &#8216;happened&#8217;. Maybe if we spent a little more time designing, and a little less building things would work a little better. I bet they had some very talented builders working on this too &#8211; but their perspective wasn&#8217;t to make a solid overall design, it was to simply get on and do what they were tasked with.. sound familiar ? How often do users come to a technical forum and ask for a solution to their problem to be faced with answers like &#8220;you need to normalise your database&#8221; etc, to which they simply retort &#8220;that&#8217;s not part of this project&#8221; ? Too many, but sadly understandably so.

A favourite Project Manager quote is &#8220;Failing to Plan is Planning to Fail&#8221;. And they are generally right &#8211; not having a consistent, well thought out and planned design can lead to very complicated and difficult to manage, change or even use software. Most architects will have come across solutions or applications that weren&#8217;t &#8216;designed&#8217; and just evolved, and will know just how much of a problem those applications really are. This is [Software&#8217;s Dirty Little Secret][1] &#8211; many systems have just &#8220;happened&#8221; without real thought to the design of the software. And for that matter, haven&#8217;t thought about the application as part of a composite solution either &#8211; this is actually quite rare, even today with all the SOA hype of the last few years. 

### The Architect&#8217;s hand

Sometimes it might seem strange to developers that an architect wants to make a project use standards compliant technologies, standardised, re-usable or existing components, such as a Business Rules Engine, Reporting & BI, Service Bus, Messaging Gateway, Portlets, etc &#8211; but this is what architecture is all about &#8211; not thinking about the one, single application or issue, but thinking about it&#8217;s co-existence in a much bigger world &#8211; not just the here and now functional requirements, but tomorrows new direction and demands. Simplifying and standardising, Rationalising and Re-Using, Conceptualising and Componentising&#8230; the best architectures are easily understood, simple solutions that achieve a kind of elegance in concept and practicality in implementation. 

There are several methodologies to use for developing an architecture &#8211; personally I use TOGAF which is more for generalist Enterprise & Solutions Architecture development but the <acronym title="Architecture Development Methodology">ADM</acronym> can be applied in concept to designing most solutions/systems/applications. 

## So we do need to &#8220;do architecture&#8221; then?

In short, Yes. But how you actually &#8220;do architecture&#8221; depends greatly on the context. There is a simple lesson here &#8211; architecture is all about simplifying and standardising the problem domain to provide an efficient, re-usable and consistent answer. The important difference between a formal &#8220;architect&#8221; and a &#8220;builder&#8221; is not of technical expertise, but of perspective, so don&#8217;t assume that your most expert Java Programmer is naturally the architect to your solutions. This doesn&#8217;t mean that an architect of all types shouldn&#8217;t possess good technical abilities and experience.

There is a nice [overview of the different types of Architects][2] at the Microsoft Architecture Journal (which is often surprisingly not MS biased) written by IASA Sweden, however this misses out the technical/infrastructure architect from their Roles diagram, which is a key architect in any IT implementation. Also, another article on the Architect Journal talks about [what an architect actually is][3], however is a little Software Architect focused. Both interesting reads, especially for non-architects to understand how to &#8216;use&#8217; an architect (well, work with one effectively at least 😉 ). 

### Some Exceptions..

There are times when a formal architecture process is more of a hindrance than a help, and that is in R & D contexts where rapid prototyping and proof of concept solutions are all about unrestricted creativity &#8211; in these cases architecture experience and knowledge is a good backdrop to help organise and focus ideas and channel that creativity into creating something unique. (But&#8230; Remember that this is not production ready, it is a prototype in need of architectural design to create a production capable solution. This is where too many rapid prototyping approaches get a bad name.)

One thing I will write about soon is the risk of &#8216;over-designing&#8217; a solution, because sometimes it is easy to get carried away with design-pattern addiction and framework-itis and actually miss the whole point of the exercise. Doing &#8216;too much&#8217; architecture can be as bad, and even worse than doing none. 

## And Finally&#8230;

So&#8230; Yes &#8211; Sometimes it&#8217;s nice to just set out in your car and drive wherever the wind takes you until the fuel runs out. But other times, and almost always when it is on &#8216;business time&#8217;, wandering aimlessly in the hope of finding something that might be useful is just not acceptable. Having that vision, aspiration, direction and roadmap an architecture can bring to your project might seem like a chore and &#8220;impossible&#8221; to achieve to some people.. but how else will you get there, without ever actively and knowingly moving toward it ? Chance ? Good Luck with that&#8230;

So, the next time you think about just winging it, and not bothering with architecture, just think about your software product as your own house that you wish to build &#8211; would you really just start building ? Or even just draw up your own plans on visio and ask the builders to &#8216;sort it out&#8217; ? 

_N.B. In the Winchester House example, strange as it may seem, the design actually met the brief &#8211; the needs of the owner weren&#8217;t really to design an architecturally great building, but just to have something creative to do.. it doesn&#8217;t matter that it wasn&#8217;t consistent, or standardised or even usable &#8211; it was the pleasure of doing that was needed, not the objective of greatness in form and function. But have a think about that&#8230; when was the last time your boss suggested you design something for the fun of it, at the company&#8217;s expense, regardless of whether it results in some sort of throw-away chimera ?_

 [1]: http://www.sciam.com/article.cfm?id=softwares-dirty-little-secret&sc=rss
 [2]: http://msdn.microsoft.com/en-us/arcjournal/cc505968.aspx
 [3]: http://msdn.microsoft.com/en-us/arcjournal/cc505974.aspx