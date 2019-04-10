---
title: Upgrading from MVC3 to MVC4 and MVC4 Async
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-07-16T18:00:00+00:00
ID: 2131
excerpt: "The other day I succumbed to the urge to upgrade my customized version of MVC Music store from ASP.Net MVC3 to MVC4. I had just finished watching Steve Sanderson's excellent TechDays 2012 session, C#5, ASP.NET MVC 4, and asynchronous Web applications (if you're doing ASP.Net MVC and haven't watched it, queue it up, awesome presentation)."
url: /index.php/webdev/serverprogramming/aspnet/upgrading-from-mvc3-to-mvc4/
views:
  - 20439
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - asp.net
  - load test
  - mvc 4
  - mvc music store

---
The other day I succumbed to the urge to upgrade my customized version of [MVC Music store][1] from ASP.Net MVC3 to MVC4. I had just finished watching Steve Sanderson's excellent TechDays 2012 session, [C#5, ASP.NET MVC 4, and asynchronous Web applications][2] (if you're doing ASP.Net MVC and haven't watched it, queue it up, awesome presentation). During the presentation, Steve used Apache Bench to show the improvements as he upgraded his small project from synchronous MVC4 to asynchronous MVC4, which got me wondering what effect MVC4 and Asynchronous MVC4 would show during the [wcat load test step][3] in my MVC Music Store build process.

## The Upgrade to MVC4

Upgrading from MVC3 to MVC4 was easy. I followed the instructions from the [MVC4 release notes][4] and committed my changes. 

I installed Visual Studio 2012 on my build server (including .Net 4.5) so the build would run. Installing a copy on build servers is allowed under visual studio licensing models and while some people try to create a 'pure' non-VS build experience, I think the only advantage one gets from this model is ensuring the production application is compiled in a different manner from the one the developers built and tested under.

VS upgraded, .Net upgraded, MVC upgraded, time to commit and push.

Build running, deploying for smoke test, failed. Page Won't Load.

### Still Copying MVC3 DLLs to Build Output

The CI build step runs the build and creates a publishable package that will be archived for all later build stages and final deployment. The last step of the CI stage deploys the package to a VM and does an HTTP GET on the front page, just to ensure it's deployable. Turns out the site deployed fine, but the front page refused to display.

<img src="http://tiernok.com/LTDBlog/MVC4/folders.png" alt="Project folders" style="margin: .5em .5em .5em 0; float: left;" />Deploying MVC3 early on took advantage of a special folder named \_bin\_deployableAssemblies, so MSBuild was overwriting the MVC4 assemblies with the contents of this folder (MVC3) and then giving me errors on the razor stack when I tried to actually load the page. The build server didn't mind because it was using either GAC'd or Reference Assemblies instead of the local DLLs.

I initially delete the folder to fix this, which turned out to be overkill because I also had SQL Server CE assemblies in there. The final result was to clear out just the MVC3 assemblies from the folder, leaving the SQL Server CE ones.

I ran several builds through the load test step, with interesting results that I'll show at the bottom.

## The Upgrade to Asynchronous

I'll be the first to call my changes hacky. In a prior post, I [switched SQL CE for full SQL Server][5] to fix a performance bottleneck. With the known bottleneck removed, I wasn't sure what the current one was or if Asynchronous MVC would improve things further, but I had high hopes.

You can see the final result on [BitBucket][6], but here's a sample of the quick and dirty surgery:

**MVCMusicStore.Main / MvcMusicStore / Controllers / HomeController.cs** ([see file][7])

```text
using System.Collections.Generic;
using System.Linq;
using System.Web.Mvc;
using MvcMusicStore.Models;
using System.Diagnostics.CodeAnalysis;
using System.Threading.Tasks;

namespace MvcMusicStore.Controllers {
        [SuppressMessage("Gendarme.Rules.Exceptions", "UseObjectDisposedExceptionRule", Justification = "If the controller is disposed mid-call we have bigger issues")]
        public class HomeController : ControllerBase {

                public HomeController() { }
                public HomeController(IMusicStoreEntities storeDb) : base(storeDb) { }

                //
                // GET: /Home/
                public async Task<ActionResult> IndexAsync() {
                        // Get most popular albums
                        var albums = await GetTopSellingAlbumsAsync(5);
                        
                        return View(albums);
                }

                private async Task<List<Album>> GetTopSellingAlbumsAsync(int count) {
                        // Group the order details by album and return
                        // the albums with the highest count
                        return await Task.Factory.StartNew<List<Album>>(() =>
                        {
                                return StoreDB.Albums
                                                                .OrderByDescending(a => a.OrderDetails.Count())
                                                                .Take(count)
                                                                .ToList();
                        });
                }
        }
}
```
Each action has been converted to an async method returning a Task<ActionResult> and my ControllerBase object now inherits from AsyncController instead of Controller.

Because the query logic for this application is spread all over the place (which is common for entity framework in my experience), in some places I have tidy await calls to Async methods (like in the IndexAsync method above), and in some cases I have an inline await Task.Factory call (GetTopSellingAlbumsAsync action). [Entity Framework 6][8] will have asynchronous Query and Save capabilities, but I personally don't care much for Entity Framework, so it's unlikely I'll go through the effort of upgrading this project from EF4 to EF6. In the meantime, I have added my own SaveAsync call to the IMusicStoreEntities interface and DbContext implementation and have added some dirty await/async calls throughout the codebase.

Final result? 

## Graphs and Numbers, woohoo!

So what happened when I upgrade to MVC4? faster, slower, … ? And how much faster did Asynchronous actions make it? 1 order of magnitude? 2 orders?

What happened was object lessons in why having load measurements are useful and why identifying your performance bottlenecks is critical, instead of treating asynchronous actions as magic pixie dust.

<div style="text-align:center;">
  <img src="http://tiernok.com/LTDBlog/MVC4/rate.png" alt="Load test - Request Rates" />
</div>

On the left side of this graph you can see that the MVC 3 rate has averaged 75 requests/second with a pretty high level of variability. Switching to MVC4 moved us to a much more consistent 95-100 requests/second average, then throwing async in the mix reduced the rate to the 80's. In each case, the requests are all of the files our browser would request when we browse the index page, select a genre to the genre page, select an album to the album page, and then check out, so some of this is dynamic content and some is static image and script files.

<div style="text-align:center;">
  <img src="http://tiernok.com/LTDBlog/MVC4/responseTime.png" alt="Load test - Response Times" />
</div>

The other interesting number is the lack of difference in Response Times. Somehow MVC4 unlocked more throughput for the system without impacting the Response Time for those requests. There was also no change at all in Response Times at all after adding Async actions, something that underlines and bolds the fact that the database calls were not the performance bottleneck for these test VMs.

## I Should Upgrade to MVC4 Now!

Well, yes, you should, but don't expect a matching 30% performance improvement. This improvement was in my test systems which are all VMs residing on the same Hyper-V server, running simulated loads that don't match what real traffic would look like on a real site with real networks and real servers. That being said, the point of the load test step is to let me know when something significant occurs and there was definitely a measurable, positive performance improvement when I performed the upgrade. Given the fact that I don't have a pouch of magic pixie dust, I have to assume that either MVC 4 or .Net 4.5 made some improvements.

The moral of the async upgrade is to be aware of your assumptions. In the real world, had I made async-ified my codebase to get a performance improvement, I would have just added a great deal of extra code complexity for a net loss in performance. There was no science to my guess that async would make things faster, it was pure guesswork.

 [1]: /index.php/All/mvc+music+store: "All LTD blog posts on MVC Music Store"
 [2]: http://channel9.msdn.com/Events/TechDays/Techdays-2012-the-Netherlands/2287 "C#5, ASP.NET MVC 4, and asynchronous Web applications on Channel9"
 [3]: /index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-adding-the-load "Continuous Delivery - Adding the Load testing Stage"
 [4]: http://www.asp.net/whitepapers/mvc4-release-notes#_Toc303253806
 [5]: /index.php/DesktopDev/MSTech/CSharp/pick-the-right-storage-all "Pick the Right Storage: All SQL is Not Equal"
 [6]: https://bitbucket.org/tarwn/mvcmusicstore.main/src "MVCMusicStore.Main on BitBucket"
 [7]: https://bitbucket.org/tarwn/mvcmusicstore.main/src/a3f3c943c685906a8986c2ce94c79db8bc04a577/MvcMusicStore/Controllers/HomeController.cs?at=default "File on BitBucket"
 [8]: http://entityframework.codeplex.com/wikipage?title=Roadmap "Entity Framework Roadmap"