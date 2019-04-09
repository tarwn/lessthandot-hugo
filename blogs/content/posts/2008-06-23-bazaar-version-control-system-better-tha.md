---
title: Bazaar Version Control System – better than Subversion ?
author: damber
type: post
date: 2008-06-23T08:41:25+00:00
ID: 68
url: /index.php/itprofessionals/softwareandconfigmgmt/bazaar-version-control-system-better-tha/
views:
  - 4285
rp4wp_auto_linked:
  - 1
categories:
  - Software and Configuration Management

---
## Version Control?

A Version Control System (VCS) is an essential tool for a development team (or even individual developer) to manage their code. The more it can allow multiple developers to work together easily on the same code and manage the various states of that code, the better. 

One of the most popular Version Control systems in the past was CVS, however in recent times this has been superseded by Subversion (SVN), which is a highly popular VCS that is well supported by a variety of client tools and is often embedded within software configuration management applications to manage file versioning (e.g. [trac][1], [collabNet][2], etc)

What is sometimes an issue with SVN is the ability to do local commits for each developer before a full commit to the main branch, making the main branch often unreliable and full of partially complete work, or local developers with limited versioning control etc. 

## Bazaar

One VCS tool that tackles this problem is [Bazaar][3], which offers no less than [7 different Version Control workflows][4] depending on your particular scenario. 

Bazaar is developed and used by [Canonical][5] (the Ubuntu people), and has recently [claimed Sun's MySQL as a new user][6]. Bazaar is one of many VCS tools, but it's flexibility in workflow patterns is certainly something that makes it an interesting tool to consider. 

## Further Info

  * [General Information on Revision Control][7]
  * [Comparison of Revision Control Systems][8]
  * [Bazaar Documentation][9]

 [1]: http://trac.edgewall.org/
 [2]: http://www.collab.net/
 [3]: http://bazaar-vcs.org/
 [4]: http://bazaar-vcs.org/Workflows
 [5]: http://canonical.com/
 [6]: http://www.theregister.co.uk/2008/06/19/mysql_dumps_bitkeeper/
 [7]: http://en.wikipedia.org/wiki/Revision_control
 [8]: http://en.wikipedia.org/wiki/Comparison_of_revision_control_software
 [9]: http://bazaar-vcs.org/Documentation