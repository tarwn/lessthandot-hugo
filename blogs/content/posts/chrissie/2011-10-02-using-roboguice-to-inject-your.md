---
title: Using Roboguice to inject your dependencies in an Android app
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-02T07:02:00+00:00
ID: 1334
excerpt: After the writing of your first application and the first tests. It is time to make our application a bit more testable by using dependency injection. I choose to use RoboGuice for this, which is a subproject of Guice made especially for the Android sub-culture.
url: /index.php/desktopdev/suntech/java/using-roboguice-to-inject-your/
views:
  - 11962
categories:
  - Java
tags:
  - android
  - di
  - ioc
  - java
  - roboguice

---
## Introduction

After the writing of your first application and [the first tests][1]. It is time to make our application a bit more testable by using dependency injection. I choose to use [RoboGuice][2] for this, which is a subproject of Guice made especially for the Android sub-culture.

## Setting it up.

You can read how to set up Roboguice to work with your application on the [wiki][3]. It&#8217;s not rocket science.

I ended up with using Roboguice 1.1.2 and the guice-2.0-no_aop.jar for this little demonstration.

I ran into a little problem with the myApplication part because they don&#8217;t mention the imports you need for this to work and Eclipse did not seem to know where to look for them. So here it is.

```java
package be.baes;

import java.util.List;
import roboguice.application.RoboApplication;
import com.google.inject.*;

public class MyApplication extends RoboApplication  
{
	protected void addApplicationModules(List&lt;Module&gt; modules) 
	{
		modules.add(new MyModule());
    }

}
```
## The project

This little project is simple. Just add a tabcontrol with 3 tabs and a textview in each tab.

So here is the main.xml.

```xml
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;TabHost xmlns:android="http://schemas.android.com/apk/res/android" 
 android:id="@android:id/tabhost" 
 android:layout_width="match_parent" 
 android:layout_height="match_parent"&gt;
 &lt;LinearLayout 
  android:id="@+id/linearLayout1" 
  android:layout_width="match_parent" 
  android:layout_height="match_parent" 
  android:orientation="vertical"&gt;
   &lt;TabWidget 
    android:layout_width="match_parent" 
    android:layout_height="wrap_content" 
    android:id="@android:id/tabs"&gt;
   &lt;/TabWidget&gt;
   &lt;FrameLayout 
    android:layout_width="match_parent" 
    android:layout_height="match_parent" 
    android:id="@android:id/tabcontent"&gt;
     &lt;TextView 
      android:layout_width="fill_parent" 
      android:id="@+id/textView1" 
      android:layout_height="fill_parent" 
      android:text="This is the first tab"&gt;
     &lt;/TextView&gt;
     &lt;TextView 
      android:layout_width="fill_parent" 
      android:id="@+id/textView2" 
      android:layout_height="fill_parent" 
      android:text="This is the second tab"&gt;
     &lt;/TextView&gt;
     &lt;TextView 
      android:layout_width="fill_parent" 
      android:id="@+id/textView3" 
      android:layout_height="fill_parent" 
      android:text="This is the third tab"&gt;
     &lt;/TextView&gt;
    &lt;/FrameLayout&gt;
   &lt;/LinearLayout&gt;
&lt;/TabHost&gt;

```
and here is the Activity before the Roboguicing.

```java
package be.baes;

import android.app.TabActivity;
import android.content.res.Resources;
import android.os.Bundle;
import android.util.Log;
import android.widget.TabHost;

public class TabViewActivity extends TabActivity {
	TabHost tabHost;
	Resources res;
	
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        res = getResources();
        
        tabHost= this.getTabHost();
        tabHost.addTab(tabHost
        		.newTabSpec("tab_test1")
        		.setIndicator("TAB 1", res.getDrawable(R.drawable.tab_one))
        		.setContent(R.id.editText1)); 
        tabHost.addTab(tabHost
        		.newTabSpec("tab_test2")
        		.setIndicator("TAB 2", res.getDrawable(R.drawable.tab_one))
        		.setContent(R.id.editText2));
        tabHost.addTab(tabHost
        		.newTabSpec("tab_test3")
        		.setIndicator("TAB 3", res.getDrawable(R.drawable.tab_one))
        		.setContent(R.id.editText3));
        tabHost.setCurrentTab(0);
        
        TabHost.OnTabChangeListener tabChangeListener = new TabHost.OnTabChangeListener() 
        {

			@Override
			public void onTabChanged(String arg0) {
				Log.d("cbaes", "Tab was changed with arg" + arg0);
				
			}
        	
        };
        
        tabHost.setOnTabChangedListener(tabChangeListener);
        
    }
}```
That looks ugly. So let&#8217;s use some DI to remove some of that code.

## Injecting views and resources

First I&#8217;m going to inject some views and resources because that&#8217;s something Roboguice does very well.

```java
package be.baes;

import roboguice.activity.*;
import roboguice.inject.InjectResource;
import roboguice.inject.InjectView;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.util.Log;
import android.widget.TabHost;
import android.widget.TextView;

public class TabViewActivity extends RoboTabActivity {
	@InjectResource(R.drawable.tab_one) Drawable tab_one_res;
	@InjectView(R.id.textView1) TextView textView1;
	@InjectView(R.id.textView2) TextView textView2;
	@InjectView(R.id.textView3) TextView textView3;
	
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        TabHost tabHost= this.getTabHost();
        addTab(tabHost,"tab_test1","TAB 1",tab_one_res,textView1.getId()); 
        addTab(tabHost,"tab_test2","TAB 2",tab_one_res,textView2.getId()); 
        addTab(tabHost,"tab_test3","TAB 3",tab_one_res,textView3.getId()); 
        tabHost.setCurrentTab(0);
        
        TabHost.OnTabChangeListener tabChangeListener = new TabHost.OnTabChangeListener() 
        {

			@Override
			public void onTabChanged(String arg0) {
				Log.d("cbaes", "Tab was changed with arg" + arg0);
				
			}
        	
        };
        
        tabHost.setOnTabChangedListener(tabChangeListener);
        
    }

	private void addTab(TabHost tabHost, String tabSpec, String indicator, Drawable res, int content) {
		tabHost.addTab(tabHost
        		.newTabSpec(tabSpec)
        		.setIndicator(indicator, res)
        		.setContent(content));
	}
}```
As you can see I made the TabActivity into a RoboTabActivity and injected the 3 textViews and the resource. I also did an extract method to make it more readable.

Now I just have to get the listener out of there and prefereably inject it into our activity.

First I extract the Anonymous class we used for the OnTabChangedListener. 

```java
package be.baes;

import android.util.Log;
import android.widget.TabHost;

public class LogTabChanged implements TabHost.OnTabChangeListener {
	@Override
	public void onTabChanged(String arg0) {
		Log.d("cbaes", "Tab was changed with arg" + arg0);
		
	}
}```
Which leaves me with this in My TabViewActivity.

```java
TabHost.OnTabChangeListener tabChangeListener = new LogTabChanged();
        ```
of course I would like to get rid of that <code class="codespan">new LogTabChanged()</code> line and just inject it.

So the first thing I want to do is, is create a constructor where I will create such a class.

Something like this.

```java
package be.baes;

import roboguice.activity.*;
import roboguice.inject.InjectResource;
import roboguice.inject.InjectView;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.widget.TabHost;
import android.widget.TextView;

public class TabViewActivity extends RoboTabActivity {
	@InjectResource(R.drawable.tab_one) Drawable tab_one_res;
	@InjectView(R.id.textView1) TextView textView1;
	@InjectView(R.id.textView2) TextView textView2;
	@InjectView(R.id.textView3) TextView textView3;
	
	LogTabChanged logTabChanged;
	
	public TabViewActivity()
	{
		logTabChanged = new LogTabChanged();
	}
	
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        TabHost tabHost= this.getTabHost();
        addTab(tabHost,"tab_test1","TAB 1",tab_one_res,textView1.getId()); 
        addTab(tabHost,"tab_test2","TAB 2",tab_one_res,textView2.getId()); 
        addTab(tabHost,"tab_test3","TAB 3",tab_one_res,textView3.getId()); 
        tabHost.setCurrentTab(0);
        
        TabHost.OnTabChangeListener tabChangeListener = logTabChanged;
        
        tabHost.setOnTabChangedListener(tabChangeListener);
        
    }

	private void addTab(TabHost tabHost, String tabSpec, String indicator, Drawable res, int content) {
		tabHost.addTab(tabHost
        		.newTabSpec(tabSpec)
        		.setIndicator(indicator, res)
        		.setContent(content));
	}
}```
And now I like to have a constructor that accepts that as a parameter.

So first I will create an interface that I can inject that extands OnTabChangeListener.

<!-- codeblock lang=""java" line="1"package be.baes;

import android.widget.TabHost;

public interface ILogTabChanged extends TabHost.OnTabChangeListener {
}
<pre>





Then I make my listener implement that.

<codeblock lang="java" line="1">package be.baes;

import android.util.Log;

public class LogTabChanged implements ILogTabChanged {
	
	@Override
	public void onTabChanged(String arg0) {
		Log.d("cbaes", "Tab was changed with arg" + arg0);
		
	}
}</pre>



And now I tell Guice which implementation it has to use for that interface.


<pre lang="java">package be.baes;

import roboguice.config.AbstractAndroidModule;

public class MyModule extends AbstractAndroidModule {

	@Override
	protected void configure() {
		bind(ILogTabChanged.class).to(LogTabChanged.class);
	}

}
</pre>




And now I have to just inject it into my activity. I can't use the constructor for this but I just inject it as a field.

And this is now what my Activity looks like. 


<pre lang="java">package be.baes;

import com.google.inject.Inject;

import roboguice.activity.*;
import roboguice.inject.InjectResource;
import roboguice.inject.InjectView;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.widget.TabHost;
import android.widget.TextView;

public class TabViewActivity extends RoboTabActivity {
	@InjectResource(R.drawable.tab_one) Drawable tab_one_res;
	@InjectView(R.id.textView1) TextView textView1;
	@InjectView(R.id.textView2) TextView textView2;
	@InjectView(R.id.textView3) TextView textView3;
	@Inject ILogTabChanged logTabChanged;
		
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        TabHost tabHost= this.getTabHost();
        addTab(tabHost,"tab_test1","TAB 1",tab_one_res,textView1.getId()); 
        addTab(tabHost,"tab_test2","TAB 2",tab_one_res,textView2.getId()); 
        addTab(tabHost,"tab_test3","TAB 3",tab_one_res,textView3.getId()); 
        tabHost.setCurrentTab(0);
        
        TabHost.OnTabChangeListener tabChangeListener = logTabChanged;
        
        tabHost.setOnTabChangedListener(tabChangeListener);
        
    }

	private void addTab(TabHost tabHost, String tabSpec, String indicator, Drawable res, int content) {
		tabHost.addTab(tabHost
        		.newTabSpec(tabSpec)
        		.setIndicator(indicator, res)
        		.setContent(content));
	}
}</pre>



A whole lot cleaner then before, me thinks.


<h2>Conclusion</h2>



Using RoboGuice as your Ioc/Di container for your Android application is fairly easy. Documentation is however hard to find for RoboGuice all though it is not a new project and fairly well developed.
</p>

 [1]: /index.php/DesktopDev/SunTech/Java/android-testing-if-an-activity
 [2]: http://code.google.com/p/roboguice/
 [3]: http://code.google.com/p/roboguice/wiki/Installation