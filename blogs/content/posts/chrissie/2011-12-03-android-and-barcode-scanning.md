---
title: Android and barcode scanning
author: Christiaan Baes (chrissie1)
type: post
date: 2011-12-03T08:06:00+00:00
ID: 1408
excerpt: |
  Barcode scanning is something I need to do a lot for work. But reading the barcodes is hard and in .Net I usually buy a framework to do the heavy lifting for me. 
  
  In essence barcodereaders are nothing more then imagers that give an image of bits to t&hellip;
url: /index.php/desktopdev/suntech/java/android-and-barcode-scanning/
views:
  - 7139
categories:
  - Java
tags:
  - android
  - zxing

---
Barcode scanning is something I need to do a lot for work. But reading the barcodes is hard and in .Net I usually buy a framework to do the heavy lifting for me. 

In essence barcodereaders are nothing more then imagers that give an image of bits to the software, the software then tries to make sense of that. Most Android devices have such an imager. That imager is called the camera. 

So making a Lob application that scans barcodes should be easy enough if they let you use the camera.

And what do you know. There is such a thing available to you for free and open source. It&#8217;s called [Zxing][1]. It scans all these types of barcodes.

UPC-A and UPC-E
      
EAN-8 and EAN-13
      
Code 39
      
Code 93
      
Code 128
      
QR Code
      
ITF
      
Codabar
      
RSS-14 (all variants)
      
Data Matrix
      
PDF 417 (&#8216;alpha&#8217; quality)
      
Aztec (&#8216;alpha&#8217; quality) 

which is more than enough for what I need.

So I made a demo application with that library and roboguice. All the code is on [Github][2].

I use eclipse.

I am using the zxing activity to do all the heavy lifting for me. And I use my Acer Incosia A100 to test it on, since you can&#8217;t use the camera in the emulator. I installed the zxing application on the machine and then connected the machine to my computer. You then make sure to set so you can install apps on that machine and allow remote debugging too.

Now that we have that all setup we can run the application.

Sadly enough the following pictures are not that good a quality, having only two hands really limits ones ability.

This is the app. It has a scan button and a delete all button. It adds the scanned code into a listview and into an SQLite database.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/android/Zxing1.png?mtime=1322906310"><img alt="" src="/wp-content/uploads/users/chrissie1/android/Zxing1.png?mtime=1322906310" width="356" height="580" /></a>
</div>

Here you see the scanner in action. The red line is where the barcode should go.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/android/Zxing2.png?mtime=1322906325"><img alt="" src="/wp-content/uploads/users/chrissie1/android/Zxing2.png?mtime=1322906325" width="613" height="354" /></a>
</div>

When it is able to read a code it shows a green line and then returns to your app.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/android/Zxing3.png?mtime=1322906337"><img alt="" src="/wp-content/uploads/users/chrissie1/android/Zxing3.png?mtime=1322906337" width="377" height="230" /></a>
</div>

The important lines of code are these.

```java
public class DoScanOnClickListener implements OnClickListener {
	@Inject Activity activity;
	
	@Override
	public void onClick(View arg0) {
		IntentIntegrator integrator = new IntentIntegrator(activity);
		integrator.initiateScan();
	}
}```
Where we open the barcode reader activity.

```java
public void onActivityResult(int requestCode, int resultCode, Intent intent) {
    	  IntentResult scanResult = IntentIntegrator.parseActivityResult(requestCode, resultCode, intent);
    	  if (scanResult != null) {
    		Toast.makeText(getApplicationContext(), "Scanned: " + scanResult.getContents() , Toast.LENGTH_SHORT).show();
    		barCodeAdapter.createBarCode(scanResult.getContents(), "Scanned on " + CurrentDate.DateAndTime());
    		fillList();
    	  }
    }```
Where we do somthing with the result.

It&#8217;s dead easy to use and it can make the lives of your users so much simpler.

 [1]: http://code.google.com/p/zxing/
 [2]: https://github.com/chrissie1/Barcodescanning