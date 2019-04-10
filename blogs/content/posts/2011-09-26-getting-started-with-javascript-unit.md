---
title: Getting Started with JavaScript Unit Testing
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-09-26T09:58:00+00:00
ID: 1321
excerpt: "Recently I decided to start doing JavaScript code katas. I've been using JavaScript for around ten years, but there there are still a lot of aspects I don't know well or that I could use more practice in. Case in point, I had never used a unit testing framework with javascript."
url: /index.php/webdev/uidevelopment/javascript/getting-started-with-javascript-unit/
views:
  - 28799
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
tags:
  - javascript
  - jstestdriver
  - qunit
  - unit testing

---
Recently I decided to start doing JavaScript [code katas.][1] I've been using JavaScript for around ten years, but there there are still a lot of aspects I don't know well or that I could use more practice in. Case in point, I had never used a unit testing framework with javascript. Having never unit tested JavaScript before, I used a scientific tool to carefully select from amongst the numerous unit testing packages available.

I typed “javascript unit testing” into Google and started reading.

## jsTestDriver

[jsTestDriver][2] was listed on a wide number of sites as one of the top JavaScript unit testing tools and fit an initial requirement I set myself of running outside of a browser. jsTestDriver runs as a client-server pair, the client sending tests to the server, which then runs them on a captured browser. The advantage of this method is it can easily be integrated with a code editor or as part of an automated build.

The jsTestDriver site includes plugins for Eclipse, Maven, and IntelliJ. I also found an article on [using it with Visual Studio][3] and it was fairly easy setting it up as a user tool in EditPlus.

<div style="text-align: center; margin: 1em; color: #666666; font-size: 80%">
  <img src="http://tiernok.com/LTDBlog/jsunittest/editplus.png" alt="Screenshot of EditPlus" /><br /> Screenshot of EditPlus Settings
</div>

Though I'm not using the feature, jsTestDriver provides a flag to specify an output file for the results, enabling us to use it as part of a continuous build.

### The Setup

Setting up jsTestDriver on my system was fairly straightforward.

  1. Download Java – Visit [java.com][4] and download the appropriate installer, run the installer, remember to go into the control panel and fiddle with Java's update settings
  2. Download the jar file – Visit the [project on Google][5] and download a copy of the jar file (I used the self-contained version)
  3. Create folders – Create a top level folder and two sub folders (for instance, src and src-test). Put the jar in the top level folder
  4. Create a conf file – I used the one in the [Getting Started Guide][6] as a starting point

At this point we should be able to fire up the server for the first time and verify everything is ready to go. I created a .cmd file on my system for the server so I could easily start it:

```text
"C:Program Files (x86)Javajre6binjava" -jar JsTestDriver-1.3.2.jar --port 4224 --browser "C:Documents and SettingsTarwnLocal SettingsApplication DataGoogleChromeApplicationchrome.exe"
```
What this does is starts the jsTestDriver jar on port 4224 and also automatically starts up an instance of chrome that will be captured by the server to run tests. I was initially using Firefox but jsTestDriver can't intercept the console log the way it can with Chrome, so I wasn't getting very good output for failed or errored tests.

Next I created a .cmd file to run all the tests in my folders:

```text
"C:Program Files (x86)Javajre6binjava" -jar JsTestDriver-1.3.2.jar --tests all
pause
```
This tells jsTestDriver to run all available tests (based on the settings in the conf) using a jsTestDriver server on port 4224. I ended up not using this cmd file very frequently, as it was much handier to be able to run them from a key command inside my editor.

### Writing Tests

Once we have gotten this far, we can start writing some simple tests.

In each directory (src and src-test), create a file named “mystuff.js”.

**src/mystuff.js**

```javascript
myAwesomeApp = {};

myAwesomeApp.MyAwesomeClass = function(){};

myAwesomeApp.MyAwesomeClass.prototype.add = function(num0, num1){
	return num0 + num1;
};
```
**src-test/mystuff.js**

```JavaScript
TestCase("Sample Test Case",{

	"test Number plus Zero Equals Number": function(){
		var adder = new myAwesomeApp.MyAwesomeClass();
		assertEquals(5, adder.add(5,0));
	},
	"test Number plus Number Equals Sum": function(){
		var adder = new myAwesomeApp.MyAwesomeClass();
		assertEquals(8, adder.add(5,3));
	},
	"test Zero plus Number Equals Number": function(){
		var adder = new myAwesomeApp.MyAwesomeClass();
		assertEquals(5, adder.add(0,5));
	},
	"test Number plus Negative of Number Equals Zero": function(){
		var adder = new myAwesomeApp.MyAwesomeClass();
		assertEquals(0, adder.add(5,-5));
	},
	"test Fails miserably": function(){
		fail("miserably");
	}
});
```
JavaScript provides a number of different methods to define objects with functions, in the source class I used the prototype method and in the tests file I used an object literal. For jsTestDriver, the important part is that the tests in the object we pass to TestCase begin with the word test, and the object literal method seemed like a friendlier layout for a test file.

### Running Tests

Once we have the two files in place, start the server by issuing the following command (or creating the cmd file like me):

```text
"C:Program Files (x86)Javajre6binjava" -jar ../JsTestDriver-1.3.2.jar --port 4224 --browser "C:Documents and SettingsTarwnLocal SettingsApplication DataGoogleChromeApplicationchrome.exe"
```
You will need to update the browser and java paths to reflect your own.

Once the browser has started and it has been captured by the server for testing, it will look like this:

<div style="text-align: center; margin: 1em; color: #666666; font-size: 80%">
  <img src="http://tiernok.com/LTDBlog/jsunittest/jsTestDriver.png" alt="Chrome captured by jsTestDriver Server" /><br /> Chrome captured by jsTestDriver Server
</div>

Now we can run our tests by issuing the following command:

```text
"C:Program Files (x86)Javajre6binjava" -jar ../JsTestDriver-1.3.2.jar --tests all
```
Again, you will need to change the java path to reflect your own (or remove it if you have added it to your PATH variable).

The results should look something like this:

<pre style="margin: 1em; border: 1px solid #999999; padding: 1em;">....F
Total 5 tests (Passed: 4; Fails: 1; Errors: 0) (0.00 ms)
  Chrome 13.0.782.220 Windows: Run 5 tests (Passed: 4; Fails: 1; Errors 0) (0.00 ms)
    Object Literal Test Case.test Fails miserably failed (0.00 ms): AssertError: miserably
      AssertError: miserably
          at Object.test Fails miserably (http://localhost:4224/test/src-test/mystuff.js:22:3)

Tests failed: Tests failed. See log for details.
</pre>

The top reflects the tests that have run at a glance with .s for passing tests, Fs for failed, and E for errored. Afterwards we get a summary of the total counts and then a section for the one browser we ran with. jsTestDriver allows you to capture multiple browsers, so we could configure this to run our tests across chrome, firefox, and IE simultaneously.

jsTestDriver also supports “setup” and “teardown” functions to run before and after tests. 

## Qunit

Qunit is a browser-based solution that was built to unit test the jQuery framework. Qunit has fewer requirements to run, but because it runs directly in a browser it means we have to switch windows and refresh in order to get an updated test run.

### The Setup

Because Qunit will run in our browser, there are relatively few requirements and unlike jsTestDriver, none of them are installations.

  1. We already made our folders, so nothing to do here
  2. Download the necessary include files to the top level: [qunit.js][7] and [qunit.css][8] (I renamed mine without the -git)
  3. Create an empty html file in the top level

The empty file will be our test runner. Update the file to look like this:

```html
<DOCTYPE html>
<html>
<head>
	<script src="http://code.jquery.com/jquery-1.6.4.min.js" type="text/javascript"></script>
	<script src="qunit.js" type="text/javascript"></script>
	<link rel="stylesheet" media="all" href="qunit.css" />

	<script src="src/mystuff.js" type="text/javascript"></script>
	<script src="src-test/mystuff_qunit.js" type="text/javascript"></script>

</head>
<body>
	<h1 id="qunit-header">MyStuff</h1>
	<h2 id="qunit-banner"></h2>
	<h2 id="qunit-userAgent"></h2>
	<ol id="qunit-tests"></ol>
</body>
</html>
```
As you can see, we are referencing a CDNed version of jQuery, the local qunit files we downloaded, our source file, and a test js file we haven't created yet. The remainder of the HTML will be used by Qunit to display the results.

### Writing Tests

Writing test in Qunit is pretty straightforward. Since we already have the src/mystuff.js file from above, we can jump right in and create a qunit version of our test cases.

**src-test/mystuff_qunit.js**

```JavaScript
module("Sample Test Case");

test("Number plus Zero Equals Number", function(){
	var adder = new myAwesomeApp.MyAwesomeClass();
	equals( adder.add(5,0),5);
});

test("Number plus Number Equals Sum", function(){
	var adder = new myAwesomeApp.MyAwesomeClass();
	equals(adder.add(5,3),8);
});

test("Zero plus Number Equals Number", function(){
	var adder = new myAwesomeApp.MyAwesomeClass();
	equals(adder.add(0,5),5);
});

test("Number plus Negative of Number Equals Zero", function(){
	var adder = new myAwesomeApp.MyAwesomeClass();
	equals(adder.add(5,-5),0);
});

test("Fails miserably", function(){
	ok(false,"miserably");
});
```
Qunit's _equals_ method has it's actual and expected arguments reversed from jsTestDriver, instead expecting them in this order: _Qunit.equals(actual, expected)_. I didn't originally notice this and had to update both the jsTestDriver test mapping script and the sample above (here and in bitbucket).

### Running Tests

Opening the testrunner html file, we should now see it display a block for each test that we have defined above.

<div style="text-align: center; margin: 1em; color: #666666; font-size: 80%">
  <img src="http://tiernok.com/LTDBlog/jsunittest/qunit.png" alt="QUnit Results" /><br /> QUnit Results
</div>

Failed tests automatically display details. Any test can be toggled open/closed by clicking it's name, and a handy “rerun” button lets us re-run a single test.

## Combining Them

By writing a small amount of glue script, I was able to re-use my jsTestDriver tests in Qunit. Since I am currently only using a small subset of assertions and using the object literal method, the glue script is limited to only exactly what I needed.

Add this file to the top level folder:
  
**jsTestDriverInQunit.js**

```JavaScript
/* bare minimum to run jsTestDriver tests as Qunit tests */
function TestCase(name, tests){
        if(tests != null)
                module(name);
        for(var key in tests){
                if(tests[key] instanceof Function && key.indexOf("test") == 0){
                        test(key,tests[key]);
                }
        }
        return function(){};
}

function assertEquals(arg0,arg1){
        equals(arg1,arg0);
}
function fail(msg){
        ok(false,msg);
}
```
And update the testrunner HTML file we created to look like this:

```html
<DOCTYPE html>
<html>
<head>
	<script src="http://code.jquery.com/jquery-1.6.4.min.js" type="text/javascript"></script>
	<script src="qunit.js" type="text/javascript"></script>
	<script src="jsTestDriverInQunit.js" type="text/javascript"></script>
	<link rel="stylesheet" media="all" href="qunit.css" />

	<script src="src/mystuff.js" type="text/javascript"></script>
	<script src="src-test/mystuff.js" type="text/javascript"></script>
</head>
<body>
	<h1 id="qunit-header">MyStuff</h1>
	<h2 id="qunit-banner"></h2>
	<h2 id="qunit-userAgent"></h2>
	<ol id="qunit-tests"></ol>
</body>
</html>
```
And now whether we run the jsTestDriver client/server or open the Qunit file, we will be running the same exact set of tests.

There is also a [project][9] that translates Qunit tests into tests that can be run with jsTestDriver.

All of the source code for this post (as well as the content of a couple programming katas) can be found in my [javascript repository][10] on BitBucket. The folder structure is slightly different to cut down on duplication of resources.

 [1]: /index.php/ITProfessionals/ProfessionalDevelopment/using-code-katas-to-improve "Read Post: Using Code Katas to Improve Programming Skills"
 [2]: http://code.google.com/p/js-test-driver/wiki/GettingStarted "Visit the jsTestDriver wiki"
 [3]: http://slmoloch.blogspot.com/2009/08/how-to-run-jstestdriver-with-visual_02.html "Read the Visual Studio post"
 [4]: http://java.com "Java website"
 [5]: http://code.google.com/p/js-test-driver/downloads/list "View the jsTestDriver downloads"
 [6]: http://code.google.com/p/js-test-driver/wiki/GettingStarted#Writing_configuration_file "Visit the Getting Started guide"
 [7]: http://code.jquery.com/qunit/qunit-git.js "Download qunit.js"
 [8]: http://code.jquery.com/qunit/qunit-git.css "Download qunit.css"
 [9]: http://code.google.com/p/js-test-driver/wiki/QUnitAdapter "Go to the QUnitAdapter project"
 [10]: https://bitbucket.org/tarwn/katas.javascript/src "Go to the source for the post"