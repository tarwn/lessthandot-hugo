---
title: How to enable image conversions in hybris 5
author: Axel Achten (axel8s)
type: post
date: 2013-10-02T12:34:00+00:00
ID: 2164
excerpt: |
  With hybris 5, you can use ImageMagick to convert your images. ImageMagick is open source software and comes with the hybris 5 platform. Now how do you enable the mediaconversion in hybris running on a Microsoft Windows OS?
  By default mediaconversion i&hellip;
url: /index.php/datamgmt/pim/how-to-enable-image-conversions/
views:
  - 19239
rp4wp_auto_linked:
  - 1
categories:
  - PIM
tags:
  - hybris
  - imagemagick
  - mediaconversion

---
With [hybris][1] 5, you can use [ImageMagick][2] to convert your images. ImageMagick is open source software and comes with the hybris 5 platform. Now how do you enable the mediaconversion in hybris running on a Microsoft Windows OS?
  
By default mediaconversion is not enabled in hybris 5 as you can see in the picture from the hMC.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ImgMag1.png?mtime=1380714712"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ImgMag1.png?mtime=1380714712" width="147" height="164" /></a>
</div>



# Make your Windows ready

To use ImageMagick you need to install the Visual C++ 2010 Redistributable Package. Also note that on a 64-bit Windows OS both the x86 and the x64 package need to be installed.
  
The necessary packages can be found here:

  * [Visual C++ 2010 Redistributable Package (x86)][3]
  * [Visual C++ 2010 Redistributable Package (x64)][4]



# Change the hybris configuration

Open the localextensions.xml file in 'hybris\_install\_dir'config and lookup the following tag:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ImgMag2.png?mtime=1380714712"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ImgMag2.png?mtime=1380714712" width="439" height="69" /></a>
</div>

Uncomment the tag and add the location of the mediaconversion extension:

```xml
<extension name="mediaconversion" />
```


# Rebuild hybris

Open a command prompt and navigate to the 'hybris\_install\_dir'binplatform directory.
  
Execute the following commands

```CMD
setantenv.bat
ant clean all
```



# Update the system

Last step is to go to the hybris Administration Console and perform a System Update.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ImgMag3.png?mtime=1380714713"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ImgMag3.png?mtime=1380714713" width="687" height="329" /></a>
</div>



# Check your system

Open hMC and navigate to the Multimedia folder. If everything went well you will see the **Conversion Groups**. Also note the availabe **Conversion Media Format** while right-clicking the **Media Formats**.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ImgMag4.png?mtime=1380714713"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ImgMag4.png?mtime=1380714713" width="419" height="239" /></a>
</div>

 [1]: http://www.hybris.com/
 [2]: http://www.imagemagick.org/
 [3]: http://www.microsoft.com/en-us/download/details.aspx?id=5555
 [4]: http://www.microsoft.com/en-us/download/details.aspx?id=14632