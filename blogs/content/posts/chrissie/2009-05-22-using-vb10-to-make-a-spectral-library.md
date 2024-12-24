---
title: Using VB10 to make a spectral library
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-22T11:39:52+00:00
ID: 441
url: /index.php/desktopdev/mstech/using-vb10-to-make-a-spectral-library/
views:
  - 10465
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net
  - vb10
  - visual studio 2010
  - vs2010

---
Most of you guys don&#8217;t know what a spectral library is and I forgive ye. But I needed something to test this brand new thing. So I choose something I know and something I know is hard to find.

But this is all about the new VB10. So I started of by downloading the also new Windows 7 RC and VS2010 beta 1. I installed windows 7 on a Virtual machine for Virtual PC. 

I already had some code for VS2008 and I used that to start. I will be uploading the whole thin gin the near future.

I will use the following frameworks. 

  * [StructureMap][1]
  * [nHibernate][2]
  * [fluent nHibernate][3]
  * [Rhino mocks][4]
  * [nUnit][5]

**Short brief** for the project: Open spectrum files and save them in a database or export them to another file. Show the spectrum on the screen in a graph. Do this for the following type MSP, [FT-IR][6] and [Raman][7] spectrometer results. Do some basic manipulation on the spectra.

All three types are different. and have there own set of rules.

But lets move on. 

I open the VS2008/Framework 3.5 solution in VS2010 and the same import wizard shows up as we had it in VS2008 so click finish and be done with it.

It then goes on to open my solution in the VS2010 format (I guess) and I get the first errors.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/VS2010/VS2010FirstProblem.png" alt="" title="" width="558" height="119" />
</div>

It seems as though it can&#8217;t find System.Windows.Forms.DataVisualization but I&#8217;m sure I read somewhere that this is now part of the 4.0 framework. So I guess like VS2008 VS2010 keeps the framework your project was compiled for. Time to change that. 

Right-click on the project pick Properties, pick Compile, blah blah, jada jada, see picture.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/VS2010/VS2010ChangeFramework.png" alt="" title="" width="720" height="450" />
</div>

We change the framework to 4.0 and voila there we have it the first problem solved everything is now ready to go for us. Lets see if it runs.

and it does.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/VS2010/VS2010RunFirst.png" alt="" title="" width="628" height="534" />
</div>

Next step was to test those lambda expressions.

I had this in VB9 to configure structuremap.

```vbnet
Public Shared Sub Initialize()
            StructureMap.ObjectFactory.Configure(Function(x) Configure(x))
        End Sub

        Private Shared Function Configure(ByVal x As StructureMap.ConfigurationExpression) As Boolean
            x.ForRequestedType(Of Forms.Interfaces.IMain).AsSingletons.TheDefault.Is.OfConcreteType(Of Forms.Main)()
            x.ForRequestedType(Of Forms.Interfaces.IMsp).TheDefault.Is.OfConcreteType(Of Forms.Msp)()
            x.ForRequestedType(Of Controllers.Interfaces.INavigation).TheDefault.Is.OfConcreteType(Of Controllers.Navigation)()

            x.SetAllProperties(Function(y) SetTheProperties(y))
        End Function

        Private Shared Function SetTheProperties(ByVal y As StructureMap.Configuration.DSL.SetterConvention) As Boolean
            y.OfType(Of Forms.Interfaces.IMain)()
            y.OfType(Of Forms.Interfaces.IMsp)()
            Return True
        End Function```
And yes we can now do this.

```vbnet
Public Shared Sub Initialize()
            StructureMap.ObjectFactory.Configure(Sub(x)
                                                     x.ForRequestedType(Of Forms.Interfaces.IMain).AsSingletons.TheDefault.Is.OfConcreteType(Of Forms.Main)()
                                                     x.ForRequestedType(Of Forms.Interfaces.IMsp).TheDefault.Is.OfConcreteType(Of Forms.Msp)()
                                                     x.ForRequestedType(Of Controllers.Interfaces.INavigation).TheDefault.Is.OfConcreteType(Of Controllers.Navigation)()

                                                     x.SetAllProperties(Sub(y)
                                                                            y.OfType(Of Forms.Interfaces.IMain)()
                                                                            y.OfType(Of Forms.Interfaces.IMsp)()
                                                                        End Sub)
                                                 End Sub)
        End Sub```
Woohoo. Let&#8217;s make that VS2009 shall we ;-).

 [1]: http://structuremap.sourceforge.net/Default.htm
 [2]: https://www.hibernate.org/343.html
 [3]: http://fluentnhibernate.org/
 [4]: http://ayende.com/projects/rhino-mocks.aspx
 [5]: http://www.nunit.org/index.php
 [6]: http://en.wikipedia.org/wiki/Fourier_transform_spectroscopy
 [7]: http://en.wikipedia.org/wiki/Raman_spectroscopy