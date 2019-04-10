---
title: Using CodeDOM to Automate Technical Screening Evaluation
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2015-05-27T11:20:17+00:00
ID: 3353
url: /index.php/itprofessionals/using-codedom-to-automate-technical-screening-evaluation/
views:
  - 3594
rp4wp_auto_linked:
  - 1
categories:
  - IT Professionals
tags:
  - CodeDom
  - reflection

---
CodeDOM provides dynamic compilation of .Net code and is commonly used in places like template generation or compilation of emitted code. I recently needed a way to evaluate code submissions against a known set of test cases, so this post will walk through building a small program to do take source code as files, compile it, and execute it against a series of known inputs and outputs to evaluate it.

Sample Code: [tarwn/Blog_CodeDom on github][1]

To download and run the code yourself, you will need git and VisualSudion 2013 or greater. Then execute:
  
`git clone git@github.com:tarwn/Blog_CodeDom.git`

The sample submissions for this post are intended to answer the following tech screening question:

> Write a function that accepts an integer and outputs a string with: “Fizz” if it is divisible by 3, “Buzz” if it is divisible by 5, “FizzBuzz” if it is divisible by both, or the number if it is divisible by neither.

(this is not a real question from our tech screening)

By the end of this post, we will take a random submission like this:

```text
public static string FizzBuzz(int number){
	if(number % 3 == 0 && number % 5 == 0){
		return "FizzBuzz";
	}
	else if (number % 3 == 0){
		return "Fizz";
	}
	else if(number % 5 == 0){
		return "Buzz";
	}
	else{
		return number.ToString();
	}
}
```
and automatically produce results like this:

```text
Evaluated File: SamplePassSubmission.txt
Evaluation Time: 5/9/2015 12:54:41 PM
Final Result: 7/7 tests passed.

Individual Results:
	Pass	- Standard number is returned as string
	Pass	- 3 is returned as 'Fizz'
	Pass	- 5 is returned as 'Buzz'
	Pass	- 15 is returned as 'FizzBuzz'
	Pass	- 9 is returned as 'Fizz'
	Pass	- 20 is returned as 'Buzz'
	Pass	- 30 is returned as 'FizzBuzz'

...
```
## Background

Lately we been doing a lot of screening and interviewing as we grow the development team at [PrecisionLender][2]. Part of our interview process requires candidates to answer a technical screening questionaire with several C# and SQL exercises. Which means that another part of our process is for someone to evaluate those answers. I have a set of unit tests for all of the C# problems, but that doesn't solve the part where we have to copy/paste into the tests, modify method names, tweak static/non-static, correct name collisions, etc. 

CodeDOM has been on my list to play with for a while and seemed like the perfect answer.

The easy part turns out to be the compilation, which we can do like so:

```csharp
// assuming a variable with code in it named "code" and inputs as an object[] named inputParameters

// 1
var parameters = new CompilerParameters();
parameters.ReferencedAssemblies.Add("System.dll");
parameters.ReferencedAssemblies.Add("System.Core.dll");
parameters.ReferencedAssemblies.Add("System.Data.dll");
parameters.GenerateInMemory = true;

// 2
var provider = new CSharpCodeProvider();
var results = provider.CompileAssemblyFromSource(parameters, code);
if (results.Errors.HasErrors)
{
	// do something meaningful + return
}

// 3
var assembly = results.CompiledAssembly;
var type = assembly.GetType("NamespaceName.ClassName");
var method = type.GetMethods()[0];
var testObject = type.GetConstructor(new Type[] { }).Invoke(null);
var output = method.Invoke(testObject, inputParameters);
```
In more detail:

  1. Define the [compilation parameters][3], including the referenced assemblies that are needed and that we want to compile the code in memory rather than generating an assembly.
  2. Compile the code and verify it built without errors
  3. Use reflection to get the object and method, instantiate the object, and invoke the method with the test inputs

When we ask people to submit code exercise answers, there is no guarantee that it will or won't have using statements, a namespace, a class name, or static keywords where I do or don't expect them. Even if they are present, there is no guarantee they will have the naming I expect, such as the “namespaceName” and “ClassName” values I hardcoded above. 

Also, ignore the fact that I grab the first method and assume it's the one I want. This works often enough that I don't mind rearranging a bit in the one or two exceptional cases.

## Normalizing the Code

To test consistently and automatically, we need to solve the following problems:

  * Remove the “static” keyword from the method we intend to execute
  * Add or rename the class name to an expected value
  * Add or rename the namespace name to an expected value
  * Add using statements if they are not present

This expanded out into the following test cases (naming pattern: FunctionUnderTest\_Scenario\_ExpectedResult):
  
**[EvaluatorTests.cs][4]**

  * NormalizeCode\_MethodWithNoNamespaceOrClass\_IsWrappedInBoth
  * NormalizeCode\_StaticClassWithNoNamespace\_IsWrappedInNamespace
  * NormalizeCode\_StaticClassWithNoNamespace\_ClassIsRenamed
  * NormalizeCode\_ClassWithNoNamespace\_IsWrappedInNamespace
  * NormalizeCode\_ClassWithNoNamespace\_ClassIsRenamed
  * NormalizeCode\_NamespacedCode\_AddsUsingStatements
  * NormalizeCode\_NamespacedCode\_RenamesNamespace
  * NormalizeCode\_RunOnCodeLine\_ReplacesOriginalClassAndNamespace
  * NormalizeCode\_TwoClasses\_OnlyFirstClassIsRenamed
  * NormalizeCode\_StaticMethod\_IsConvertedToInstance
  * NormalizeCode\_MultipleStaticMethods\_AllAreConvertedToInstance

Breaking this down into pieces:
  
(full function at [EvaluateFizzBuzz/Evaluation/Evaluator.cs – NormalizeCode()][5])

We need to ensure the first method is an instance method rather than static:

```csharp
// instance-ize functions
var methodnameRegex = new Regex("(public|private) static (?!class)([^{]+)+{");
if (methodnameRegex.IsMatch(normalizedSource))
{
	normalizedSource = methodnameRegex.Replace(normalizedSource, "$1 $2\n{");
}
```
Add or Normalize the class name to an expected value (IntendedClassName):

```csharp
// normalize class name
var classnameRegex = new Regex("(public|private) (static )?class [^{]+{");
if (classnameRegex.IsMatch(normalizedSource))
{
	normalizedSource = classnameRegex.Replace(normalizedSource, "public class " + IntendedClassName + "\r\n{", 1);
}
else
{
	normalizedSource = String.Format("public class {0}\r\n{{\r\n{1}\r\n\r\n}}", IntendedClassName, normalizedSource);
}
```
Add or Normalize the namespace name to an expected value (IntendedNamespace)

```csharp
// normalize namespace
var namespaceRegex = new Regex("namespace [^\\n{ ]+[^{]+{");
if (namespaceRegex.IsMatch(normalizedSource))
{
	normalizedSource = namespaceRegex.Replace(normalizedSource, "namespace " + IntendedNamespace + "\r\n{");
}
else
{
	normalizedSource = String.Format("{0}\r\n\nnamespace {1}\r\n{{\r\n{2}\r\n}}", USING_STATEMENTS, IntendedNamespace, normalizedSource);
}
```
And then add a standard set of using statements (USING_STATEMENTS constant) if they aren't present yet:

```csharp
// add using statements if not present
if (!normalizedSource.Contains("using System"))
{
normalizedSource = USING_STATEMENTS + "\r\n" + normalizedSource;
}
```
This will convert a file like the  [sample “Pass” submission file][6] like so:

Before:

```csharp
public static string FizzBuzz(int number){
	if(number % 3 == 0 && number % 5 == 0){
		return "FizzBuzz";
	}
	else if (number % 3 == 0){
		return "Fizz";
	}
	else if(number % 5 == 0){
		return "Buzz";
	}
	else{
		return number.ToString();
	}
}
```
After:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;


namespace FizzBuzzSample
{
public class FizzBuzzClass
{


public string FizzBuzz(int number)
{
	if(number % 3 == 0 && number % 5 == 0){
		return "FizzBuzz";
	}
	else if (number % 3 == 0){
		return "Fizz";
	}
	else if(number % 5 == 0){
		return "Buzz";
	}
	else{
		return number.ToString();
	}
}

}
}
```
So now we have consistent code, next up is evaluating the results.

## Compiling and Evaluating Output

Earlier in the post I showed example code to compile and run some code. Now that we have normalized code, we know exactly what namespace and class name to expect and can run it through that code and, finally, evaluate the output.

The Evaluator requires not just an IntendedNamespace and IntendedClassname, but also the list of TestDefinitions it will evaluate against:
  
**[EvaluateFizzBuzz/Evaluation/TestDefinition.cs][7]**

```csharp
public class TestDefinition
{
	...

        public TestDefinition(string name, object[] inputParameters, object expectedOutput)
        {
            Name = name;
            InputParameters = inputParameters;
            DescriptionOfExpectation = String.Format(expectedOutput.ToString());
            EvaluateResult = (o) => new LocalEvaluationResult(expectedOutput.Equals(o), o != null ? o.ToString() : "");
        }

	...
}
```
Here are the TestDefinitions we will evaluate against for the sample problem:
  
**[EvaluateFizzBuzz/Program.cs][8]**

```csharp
new List<TestDefinition>(){
	new TestDefinition("Standard number is returned as string", new object[]{ 1 }, "1"),
	new TestDefinition("3 is returned as 'Fizz'", new object[]{ 3 }, "Fizz"),
	new TestDefinition("5 is returned as 'Buzz'", new object[]{ 5 }, "Buzz"),
	new TestDefinition("15 is returned as 'FizzBuzz'", new object[]{ 15 },"FizzBuzz"),
	new TestDefinition("9 is returned as 'Fizz'", new object[]{ 9 }, "Fizz"),
	new TestDefinition("20 is returned as 'Buzz'", new object[]{ 20 }, "Buzz"),
	new TestDefinition("30 is returned as 'FizzBuzz'", new object[]{ 30 }, "FizzBuzz")
}
```
Now we can expand the compilation code above to compile and then invoke and evaluate against each of these TestDefinitions:
  
**[EvaluateFizzBuzz/Evaluation/Evaluator.cs][9]**

```csharp
var parameters = new CompilerParameters();
// ... set up parameters ...

var provider = new CSharpCodeProvider();
var results = provider.CompileAssemblyFromSource(parameters, code);

if (results.Errors.HasErrors)
{
	// ... compile list of errors and return
}

var assembly = results.CompiledAssembly;
var type = assembly.GetType(IntendedNamespace + "." + IntendedClassName);
var method = type.GetMethods()[0];

var result = new EvaluationResult();
foreach (var test in Tests)
{
	var testObject = type.GetConstructor(new Type[] { }).Invoke(null);
	try
	{
		// ...

		var output = method.Invoke(testObject, test.InputParameters);
		var evalResult = test.EvaluateResult(output);

		if (evalResult.IsPass)
		{
			result.Tests.Add(/* ... pass information ... */);
		}
		else
		{
			result.Tests.Add(/* ... fail information ... */);
		}
	}
	catch (TargetInvocationException exc)
	{
		result.Tests.Add(/* ... error information ... */);
	}
	finally
	{
	    // ...
	}
}

result.Summary = String.Format("{0}/{1} tests passed.",
			    result.Tests.Where(t => t.IsPass).Count(),
			    result.Tests.Count);
// ...

return result;
```
And there we have it, we start with one randomly submitted code file and end with the results of automated evaluation.

## But What About…?

This is only one small part of our interview process and, before anyone brings it up, we do also evaluate for things the computer can't, such as readability of the code. While I have considered some options like incorporating NDepend evaluation, we'll likely never be able to completely automate this. That being said, having this portion automated does reduce the amount of manual evaluation we do, so overall we still save quite a bit of effort.

 [1]: https://github.com/tarwn/Blog_CodeDom "tarwn/Blog_CodeDom on github"
 [2]: http://precisionlender.com/about-us/?utm_source=nav&utm_medium=topnav&utm_campaign=website#team
 [3]: https://msdn.microsoft.com/en-us/library/system.codedom.compiler.compilerparameters%28v=vs.110%29.aspx "See CompilerParameters on MSDN"
 [4]: https://github.com/tarwn/Blog_CodeDom/blob/master/EvaluateFizzBuzzTests/EvaluatorTests.cs "EvaluateFizzBuzzTests/EvaluatorTests.cs on github"
 [5]: https://github.com/tarwn/Blog_CodeDom/blob/master/EvaluateFizzBuzz/Evaluation/Evaluator.cs "EvaluateFizzBuzz/Evaluation/Evaluator.cs, Line 34
on github"
 [6]: https://github.com/tarwn/Blog_CodeDom/blob/master/EvaluateFizzBuzz/SampleFile/SamplePassSubmission.txt "EvaluateFizzBuzz/SampleFile/SamplePassSubmission.txt"
 [7]:  "EvaluateFizzBuzz/Evaluation/TestDefinition.cs on github"
 [8]: https://github.com/tarwn/Blog_CodeDom/blob/master/EvaluateFizzBuzz/Program.cs "EvaluateFizzBuzz/Program.cs on github"
 [9]: https://github.com/tarwn/Blog_CodeDom/blob/master/EvaluateFizzBuzz/Evaluation/Evaluator.cs "EvaluateFizzBuzz/Evaluation/Evaluator.cs on github"