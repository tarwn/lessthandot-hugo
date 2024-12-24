---
title: Opening jp2 (jpeg2000) files in .Net
author: Christiaan Baes (chrissie1)
type: post
date: 2009-10-30T07:08:16+00:00
ID: 599
url: /index.php/desktopdev/mstech/opening-jp2-jpeg2000-files-in-net/
views:
  - 8568
categories:
  - Microsoft Technologies
tags:
  - freeimage
  - jp2
  - jpeg2000
  - vb.net

---
Yesterday I got a request to open some jp2 files, so that they could be renamed depending on the exif information and then saved as jpeg. 

Jpeg2000 was once supposed to replace jpeg because it was so much better than jpeg. Never happened and never will. But the format is out there and .Net has no native support for it. 

These are the formats .Net supports.

<table border="1">
  <tr>
    <td>
      Bmp
    </td>
    
    <td>
      Gets the bitmap (BMP) image format.
    </td>
  </tr>
  
  <tr>
    <td>
      Emf
    </td>
    
    <td>
      Gets the enhanced metafile (EMF) image format.
    </td>
  </tr>
  
  <tr>
    <td>
      Exif
    </td>
    
    <td>
      Gets the Exchangeable Image File (Exif) format.
    </td>
  </tr>
  
  <tr>
    <td>
      Gif
    </td>
    
    <td>
      Gets the Graphics Interchange Format (GIF) image format.
    </td>
  </tr>
  
  <tr>
    <td>
      Icon
    </td>
    
    <td>
      Gets the Windows icon image format.
    </td>
  </tr>
  
  <tr>
    <td>
      Jpeg
    </td>
    
    <td>
      Gets the Joint Photographic Experts Group (JPEG) image format.
    </td>
  </tr>
  
  <tr>
    <td>
      MemoryBmp
    </td>
    
    <td>
      Gets a memory bitmap image format.
    </td>
  </tr>
  
  <tr>
    <td>
      Png
    </td>
    
    <td>
      Gets the W3C Portable Network Graphics (PNG) image format.
    </td>
  </tr>
  
  <tr>
    <td>
      Tiff
    </td>
    
    <td>
      Gets the Tagged Image File Format (TIFF) image format.
    </td>
  </tr>
  
  <tr>
    <td>
      Wmf
    </td>
    
    <td>
      Gets the Windows metafile (WMF) image format.
    </td>
  </tr>
</table>

No jp2 as you can see. So I went to google for a solution. And I found one. It&#8217;s called [FreeImage][1]. 

Which supports these formats.

<table border="1">
  <tr>
    <td>
      Supported formats
    </td>
  </tr>
  
  <tr>
    <td>
      BMP files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      Dr. Halo CUT files [reading] *
    </td>
  </tr>
  
  <tr>
    <td>
      DDS files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      EXR files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      Raw Fax G3 files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      GIF files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      HDR files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      ICO files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      IFF files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      JBIG [reading, writing] **
    </td>
  </tr>
  
  <tr>
    <td>
      JNG files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      JPEG/JIF files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      JPEG-2000 File Format [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      JPEG-2000 codestream [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      KOALA files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      Kodak PhotoCD files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      MNG files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      PCX files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      PBM/PGM/PPM files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      PFM files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      PNG files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      Macintosh PICT files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      Photoshop PSD files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      RAW camera files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      Sun RAS files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      SGI files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      TARGA files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      TIFF files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      WBMP files [reading, writing]
    </td>
  </tr>
  
  <tr>
    <td>
      XBM files [reading]
    </td>
  </tr>
  
  <tr>
    <td>
      XPM files [reading, writing]
    </td>
  </tr>
</table>

* only grayscale
  
** only via external plugin, might require a commercial license

> en Source library project for developers who would like to support popular graphics image formats like PNG, BMP, JPEG, TIFF and others as needed by today&#8217;s multimedia applications. FreeImage is easy to use, fast, multithreading safe, compatible with all 32-bit versions of Windows, and cross-platform (works both with Linux and Mac OS X).

It isn&#8217;t a managed .Net library but there is a wrapper for .Net. 

So after downloading the whole thing I went to work. 

You first set a reference to FreeImageNet.dll. And then you make sure that the FreeImage.dll appears in your application folder. Either by copy pasting it or by making it a part of your project and setting its &#8220;Copy to Output Directory&#8221; property to Copy Always and Build Action to none.

Now you just have to use the library.

```vbnet
If Not FreeImage.IsAvailable() Then
            MessageBox.Show("FreeImage.dll seems to be missing. Aborting.")
        Else

            Dim o As New OpenFileDialog
            o.Filter = "jpeg2000|*.jp2"
            If o.ShowDialog = Windows.Forms.DialogResult.OK Then
                If Not dib.IsNull Then
                    FreeImage.Unload(dib)
                End If
                dib = FreeImage.LoadEx(o.FileName)
                Me.PictureBox1.Image = FreeImage.GetBitmap(dib)
                Me.PictureBox1.SizeMode = PictureBoxSizeMode.StretchImage
            End If
        End If```
The above code checks if the library works, opens a filedialog so you can select your file, opens the image in freeimage format, makes it into .Net Image and shows it in a picturebox in stretchmode.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/freeimage/freeimage.png" alt="" title="" width="563" height="358" />
</div>

And that&#8217;s it easy as pie. 

> <span class="MT_red">But of course it didn&#8217;t work the first time around. Not because it doesn&#8217;t work but because I forgot to read the signs, big signs at that. My system is Win7 64x and the FreeImage dll is 32 bits so it kinda didn&#8217;t like that. Easily solved though. You just set the Active Solution Platform to x86 and you&#8217;re done.</span>
> 
> <div class="image_block">
>   <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/freeimage/freeimage2.png" alt="" title="" width="717" height="450" />
> </div>

 [1]: http://freeimage.sourceforge.net/index.html