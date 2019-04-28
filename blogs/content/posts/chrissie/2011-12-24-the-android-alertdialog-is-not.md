---
title: The android alertdialog is not the same as a messagebox in winforms.
author: Christiaan Baes (chrissie1)
type: post
date: 2011-12-24T10:06:00+00:00
ID: 1462
excerpt: |
  For my android application I made a class that contains an alertdialog with just a yes and a no button.
  
  I use winforms a lot and I was thinking the alertdialog in android was the same. Well it isn't.
url: /index.php/desktopdev/suntech/java/the-android-alertdialog-is-not/
views:
  - 4087
categories:
  - Java
tags:
  - alertdialog
  - android

---
For my android application I made a class that contains an alertdialog with just a yes and a no button.

I use winforms a lot and I was thinking the alertdialog in android was the same. Well it isn&#8217;t.

```java
public class YesNoAlertDialogImpl implements YesNoAlertDialog {

    @Override
    public boolean show(View view, String title, String message)
    {
        boolean result = false;
        AlertDialog.Builder alert = new AlertDialog.Builder(view.getContext());

        alert.setTitle(title);
        alert.setMessage(message);

        alert.setPositiveButton("Yes",new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {
                result = true;
            }
        });

        alert.setNegativeButton("No", null);
        alert.show();
        return result;
    }
    
}```
Which I would then use as such.

```java
if(!yesNoAlertDialog.show(view, "title", "message") return;
//do your thing here```
But the above does not work because unlike the messagebox, the alertdialog does not run on the UIthread and your code will not wait for the alertdialog to return for you. 

But it is this I needed to do.

```java
public class YesNoAlertDialogImpl {

    @Override
    public void show(View view, String title, String message, DialogInterface.OnClickListener positiveClickListener, DialogInterface.OnClickListener negativeClickListener)
    {
        AlertDialog.Builder alert = new AlertDialog.Builder(view.getContext());

        alert.setTitle(title);
        alert.setMessage(message);

        alert.setPositiveButton("Yes",positiveClickListener);

        alert.setNegativeButton("No", negativeClickListener);
        alert.show();
    }
    
}```
With this as the implementation.

```java
yesNoAlertDialog.show(view, "title", "message" ,new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {
                //do your thing here
            }
        },null);```
If one doesn&#8217;t make mistakes, one doesn&#8217;t learn.