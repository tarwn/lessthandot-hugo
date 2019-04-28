---
title: 'Monodevelop and VB.Net and Ubuntu: how to install'
author: Christiaan Baes (chrissie1)
type: post
date: 2014-06-24T09:24:12+00:00
ID: 2785
excerpt: Monodevelop on Ubuntu for VB.Net.
url: /index.php/uncategorized/monodevelop-and-vb-net-and-ubuntu-how-to-install/
views:
  - 10198
categories:
  - Uncategorized

---
Last week I switched from Windows 8 to Ubuntu 14.04 because Win 8 was constantly freezing my machine and is a shitty Operating system for the desktop. 

So now was the time to play a bit more with mono and monodevelop. 

Here are my steps to get it working.

Some of it I got from [here][1] and some from http://superuser.com/questions/309168/error-visual-basic-net-compiler-not-found-mono-2-6-7-first-mononet-app.<pre lang=bash> sudo apt-get install git automake gnome-sharp2 mono-xsp4 git clone https://github.com/mono/monodevelop.git cd monodevelop sudo apt-get install libglade2.0-cil-dev sudo apt-get install monodoc-base sudo apt-get install monodevelop mono-vbnc ./configure -–profile=stable sudo make </pre> 

And when all that is done. You can.<pre lang=bash>sudo make run</pre> 

Now you can create a new consoleapplication.

[<img src="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-111835-300x180.png" alt="Screenshot from 2014-06-24 11:18:35" width="300" height="180" class="alignnone size-medium wp-image-2787" srcset="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-111835-300x180.png 300w, /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-111835.png 930w" sizes="(max-width: 300px) 100vw, 300px" />][2]

And see the magic happen when we click the run button.

[<img src="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-111850-300x225.png" alt="Screenshot from 2014-06-24 11:18:50" width="300" height="225" class="alignnone size-medium wp-image-2786" srcset="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-111850-300x225.png 300w, /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-111850.png 1000w" sizes="(max-width: 300px) 100vw, 300px" />][3]
  
And now you are wondering where your output is.
  
So was I.
  
I was stumped.
  
But not to worry it is there in the bash window which you used to do make run.

[<img src="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-112157-300x191.png" alt="Screenshot from 2014-06-24 11:21:57" width="300" height="191" class="alignnone size-medium wp-image-2788" srcset="/wp-content/uploads/2014/06/Screenshot-from-2014-06-24-112157-300x191.png 300w, /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-112157.png 562w" sizes="(max-width: 300px) 100vw, 300px" />][4]

All&#8217;s well that ends well.

 [1]: http://anangbakti.wordpress.com/2014/05/16/install-monodevelop-5-1-on-fresh-ubuntu-14-04-lts-64bit/
 [2]: /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-111835.png
 [3]: /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-111850.png
 [4]: /wp-content/uploads/2014/06/Screenshot-from-2014-06-24-112157.png