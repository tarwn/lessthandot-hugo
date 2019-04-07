---
title: Bulk Editing With Sed
author: Rob Earl
type: post
date: 2011-04-30T17:51:00+00:00
ID: 1148
excerpt: |
  This week, after refactoring some XML based Java classes, I was left with ~50 XML documents (used for testing) with the wrong root element. After a few seconds pondering opening vim and find/replacing 50 times I had a better idea: sed.
  
  sed can be use&hellip;
url: /index.php/sysadmins/os/linux/bulk-editing-with-sed/
views:
  - 2297
rp4wp_auto_linked:
  - 1
categories:
  - Linux

---
This week, after refactoring some XML based Java classes, I was left with ~50 XML documents (used for testing) with the wrong root element. After a few seconds pondering opening a text editor and find/replacing 50 times I had a better idea: [sed][1].

sed can be used to perform regular expressions on each line of a file (or files). To solve the problem all it took was:

```
sed -i s/oldroot/newroot/ *

-i: edits the files in place instead of printing to stdout
```
All 50 documents updated, simple as that.

 [1]: http://www.gnu.org/software/sed/manual/sed.html