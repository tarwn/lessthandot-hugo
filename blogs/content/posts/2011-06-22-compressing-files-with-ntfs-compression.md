---
title: Compressing files with NTFS compression by running compact from the command line
author: SQLDenis
type: post
date: 2011-06-22T14:11:00+00:00
excerpt: |
  You know that you can right click on a file/folder, select properties, click advanced and then compress contents to save disk space(see image).
  
  
  
  
  Did you know that you can call this from the command line also? I just saw this on  Raymond Chen's b&hellip;
url: /index.php/sysadmins/os/windows/vista/compressing-files-with-ntfs-compression/
views:
  - 13221
rp4wp_auto_linked:
  - 1
categories:
  - 2003 Server
  - 2008 Server
  - Vista
  - Windows 7
  - XP
tags:
  - compression
  - ntfs
  - zip

---
You know that you can right click on a file/folder, select properties, click advanced and then compress contents to save disk space(see image).

[<img src="http://farm6.static.flickr.com/5277/5860004203_c847a70cac.jpg" width="394" height="300" alt="compress" />][1]

Did you know that you can call this from the command line also? I just saw this on Raymond Chen&#8217;s blog today: http://blogs.msdn.com/b/oldnewthing/archive/2011/06/22/10177613.aspx

I decided to try it out

The command is compact , here are all the switches

<pre>C:UsersSQLDenis&gt;compact /?
Displays or alters the compression of files on NTFS partitions.

COMPACT [/C | /U] [/S[:dir]] [/A] [/I] [/F] [/Q] [filename [...]]

  /C        Compresses the specified files.  Directories will be marked
            so that files added afterward will be compressed.
  /U        Uncompresses the specified files.  Directories will be marked
            so that files added afterward will not be compressed.
  /S        Performs the specified operation on files in the given
            directory and all subdirectories.  Default "dir" is the
            current directory.
  /A        Displays files with the hidden or system attributes.  These
            files are omitted by default.
  /I        Continues performing the specified operation even after errors
            have occurred.  By default, COMPACT stops when an error is
            encountered.
  /F        Forces the compress operation on all specified files, even
            those which are already compressed.  Already-compressed files
            are skipped by default.
  /Q        Reports only the most essential information.
  filename  Specifies a pattern, file, or directory.

  Used without parameters, COMPACT displays the compression state of
  the current directory and any files it contains. You may use multiple
  filenames and wildcards.  You must put spaces between multiple
  parameters.</pre>

I have a temp folder on my C drive, first I want to see if anything is compressed, I will run **compact /Q** to get some information

<pre>c:Temp&gt;compact /Q

 Listing c:Temp
 New files added to this directory will not be compressed.


Of 8 files within 1 directories
0 are compressed and 8 are not compressed.
1,833,822 total bytes of data are stored in 1,833,822 bytes.
The compression ratio is 1.0 to 1.</pre>

Now, I want to compress those files, I will do that with the **compact /C** command

<pre>c:Temp&gt;compact /C

 Setting the directory c:Temp to compress new files [OK]

 Compressing files in c:Temp

ChartingUsage.abf      352256 :    143360 = 2.5 to 1 [OK]
ipconfig.txt            28482 :      8192 = 3.5 to 1 [OK]
Test.pcf.vpn              982 :       982 = 1.0 to 1 [OK]
test.csv                 8375 :      4096 = 2.0 to 1 [OK]
test.htm                25204 :      8192 = 3.1 to 1 [OK]
test2.csv                8375 :      4096 = 2.0 to 1 [OK]
TestDB.BAK            1395200 :    258048 = 5.4 to 1 [OK]
tshoot.txt              14948 :      8192 = 1.8 to 1 [OK]

9 files within 2 directories were compressed.
1,833,822 total bytes of data are stored in 435,158 bytes.
The compression ratio is 4.2 to 1.</pre>

Now I can run **compact /Q** again to get some information

<pre>c:Temp&gt;compact /Q

 Listing c:Temp
 New files added to this directory will be compressed.


Of 8 files within 1 directories
8 are compressed and 0 are not compressed.
1,833,822 total bytes of data are stored in 435,158 bytes.
The compression ratio is 4.2 to 1.</pre>

As you can see, the files are compressed and the ratio is 4.2 to 1

Hopefully this will be useful for some of you that copy files and then want to compress files after the copy process is done

 [1]: http://www.flickr.com/photos/denisgobo/5860004203/ "compress by Denis Gobo, on Flickr"