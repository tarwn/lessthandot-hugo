---
title: My First Android Application (Ubuntu Linux)
author: Rob Earl
type: post
date: 2011-01-05T17:46:00+00:00
excerpt: |
  This post details my first adventure into developing for the Android platform and the steps taken to get the HelloWorld application to run.
  
  Download the Linux SDK from: http://developer.android.com/sdk/index.html
  Extract the archive and take note of&hellip;
url: /index.php/desktopdev/suntech/java/my-first-android-application-ubuntu/
views:
  - 10385
rp4wp_auto_linked:
  - 1
categories:
  - Java
tags:
  - android
  - java
  - linux
  - ubuntu

---
This post details my first adventure into developing for the Android platform and the steps taken to get the HelloWorld application to run.

  1. Download the Linux SDK from: http://developer.android.com/sdk/index.html
  2. Extract the archive and take note of the directory
  3. Install Eclipse (\`apt-get install eclipse\`)
  4. Install Eclipse plugin 
      1. Help->Install Software
      2. Add repository: https://dl-ssl.google.com/android/eclipse
      3. Select Developer Tools
      4. Read and accept licenses
  5. Configure Eclipse 
      1. Windows->Preferences->Android
      2. Set SDK Location
      3. Select OK
  6. Setup SDK and Virtual Device 
      1. From Eclipse: Window->Android SDK and AVD Manager->Available Packages
      2. Select Android Repository->Install Selected
      3. Select Virtual devices->New: Name the device and select the target API version
  7. Create Android Project in Eclipse 
      1. Project name: HelloAndroid
      2. Application name: Hello
      3. Package name: com.example.helloandroid
      4. Create Activity: HelloAndroid
      5. Build Target: Same as the target as selected for Virtual Device
  8. Add the code below to src/com.example.helloandroid/HelloAndroid.java
  9. Create a new Runtime configuration 
      1. Run->Run Configurations->Android->New
      2. Project: HelloAndroid
      3. Select Target tab and choose the Virtual Device configured earlier
 10. Run!
 11. After a couple of minutes the emulator should boot and display the HelloWorld application (You may need to unlock the screen first)

 

## HelloAndroid.java

<pre>package com.example.helloandroid;

import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;

public class HelloAndroid extends Activity {
   /** Called when the activity is first created. */
   @Override
   public void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       TextView tv = new TextView(this);
       tv.setText("Hello, Android");
       setContentView(tv);
   }
}</pre>

For more information see the Android development documentation:

http://developer.android.com/sdk/installing.html  
http://developer.android.com/sdk/eclipse-adt.html#installing  
http://developer.android.com/resources/tutorials/hello-world.html