---
title: 'Small presenter tip: changing display mode automatically after presenter view'
author: Koen Verbeeck
type: post
date: 2015-03-04T09:52:02+00:00
ID: 3282
url: /index.php/uncategorized/small-presenter-tip-changing-display-mode-automatically-after-presenter-view/
views:
  - 5595
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
This morning Adam Machanic ([blog][1] | [twitter][2]) asked the following question on Twitter:

<blockquote class="twitter-tweet" lang="en">
  <p>
    Anyone know if it's possible to configure PowerPoint 2013 to use speaker mode but then go back to duplicate displays on exit?
  </p>
  
  <p>
    &mdash; Adam Machanic (@AdamMachanic) <a href="https://twitter.com/AdamMachanic/status/573043480563462145">March 4, 2015</a>
  </p>
</blockquote>

A very interesting question, as I was wondering this myself at my last session. I was presenting the slides using presenter mode, which means the projector screen is an extension of your desktop. PowerPoint does this automatically. However, when exiting the slideshow I would like to go back to duplicated screens, as it's much easier to do demos when your screen is duplicated instead of extended. I have heard other presenters with the same issue as well.

A quick search with my favorite search engine led me to a [Q&A on the Microsoft website][3]. Apparently all you have to do is add a registry setting (of course, we're still using Windows). At [HKEY\_CURRENT\_USER\Software\Microsoft\Office\15.0\PowerPoint\Options] the following DWORD value has to be added: “RestoreTopology”=dword:00000001. The site I linked to has an easy update script that you can use.

Adam has already tested it and it works perfectly!

<blockquote class="twitter-tweet" lang="en">
  <p>
    <a href="https://twitter.com/Ko_Ver">@Ko_Ver</a> just tried it&#8211;works great! Thank you!
  </p>
  
  <p>
    &mdash; Adam Machanic (@AdamMachanic) <a href="https://twitter.com/AdamMachanic/status/573045360094015488">March 4, 2015</a>
  </p>
</blockquote>

 [1]: http://sqlblog.com/blogs/adam_machanic/
 [2]: https://twitter.com/AdamMachanic
 [3]: http://answers.microsoft.com/en-us/office/forum/office_2013_release-powerpoint/powerpoint-2013-changes-display-setup/3ca470a0-d896-4577-b724-a360b6bf1c4e