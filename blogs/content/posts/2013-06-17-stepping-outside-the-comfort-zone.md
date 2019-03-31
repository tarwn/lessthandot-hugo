---
title: Stepping Outside the Comfort Zone
author: Jes Borland
type: post
date: 2013-06-17T20:39:00+00:00
excerpt: 'I love and am very familiar with SQL Server. But, as a consultant, I need to step outside of that box pretty often, and advise people on applications, storage, and networking. I have general knowledge of those topics, gathered from many years in IT, see&hellip;'
url: /index.php/itprofessionals/professionaldevelopment/stepping-outside-the-comfort-zone/
views:
  - 6797
rp4wp_auto_linked:
  - 1
categories:
  - Consulting
  - Professional Development

---
<img style="vertical-align: top;" src="/wp-content/uploads/users/grrlgeek/your-comfort-zone.jpg?mtime=1322622240" alt="" width="380" height="220" />

I love and am very familiar with SQL Server. But, as a consultant, I need to step outside of that box pretty often, and advise people on applications, storage, and networking. I have general knowledge of those topics, gathered from many years in IT, seeing a lot of systems, and always being willing to learn.

But having to work with some things on my own is the hardest part. I’ve been struggling with virtualization for some months. I have WMware Workstation on my personal laptop &#8211; I went and bought a full license because I really needed the full capabilities &#8211; and I decided to conquer some of my fears.

### Cloning

I wanted to clone virtual machines, because I thought it would be really nice to install my OS and applications, like SQL, and be able to use the base image again. I thought this was a hard thing to do. It turns out it’s this easy: open a virtual machine, go to Manage > Clone, and follow a wizard. The hardest part is deciding if you want a linked clone or a full clone. That’s it. No magic, no voodoo, nothing scary. I now have my base image cloned and can use it any time I need it.

### Managing Disks

I’ve also been struggling with setting up a virtual machine to have multiple disks, so I can show performance differences between a rotational hard drive and a solid state drive in filegroups presentations. I could not figure this out. I did something I have a hard time with: I asked for help. At SQL Saturday Fargo, I sat and chatted with David Klee ([b][1] | [t][2]) for a while. David is one of the smartest virtualization people I know (and was awarded as a VMware vExpert for 2013 recently!). I talked over some of the problems I was having with him, and he said he’d be happy to help me. So I emailed him. He replied with screenshots, telling me to add another hard disk to the virtual machine.

I did that, but didn’t see the disk added when I started the vm up. Then I remembered things like I have to bring the disk online, and initialize it, and make it a new volume. I did that, all on my own. I remembered to go to permissions and give my SQL service account read and write rights.

Next thing I know, I’m creating databases on multiple disks.

I was excited. No, really, I was! There are some people reading this that are probably thinking, &#8220;That was really easy. Why did she have to ask for help?&#8221; But this is stuff I don’t do every day, or even every week. I am not good at asking for help, but I did it. I called up skills from way back in the day and applied them.

I’m a happy geek.

Take some time to step outside your comfort zone, and learn something new today.

 [1]: http://www.davidklee.net/
 [2]: https://twitter.com/kleegeek