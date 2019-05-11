---
title: Java Training Day 2
author: SQLDenis
type: post
date: 2012-11-28T00:05:00+00:00
ID: 1804
excerpt: |
  Control statements
  
  if
  
  has to have parentheses and it has to be a boolean expressions  if (boolean-expression) {statement};
  We also covered the while and do while loop followed by the for loop
  
  Enhanced for loop aka foreach loop
  The for loop lo&hellip;
url: /index.php/enterprisedev/appserver/java-training-day-2/
views:
  - 5086
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
  - Application Server
  - Java EE
  - JBoss
  - Websphere

---
This is day two of the Java training that our group is taking. Today nothing really complicated was covered, nobody looked like a zombie yet, I am sure by Friday, that will be different. Here is a quick rundown of what was covered

**Control statements**

**if**

The if has to have parentheses and it has to be a boolean expressions if (boolean-expression) {statement};
  
We also covered the while and do while loop followed by the for loop

Enhanced for loop aka foreach loop
  
The for loop looks like this for(1=0;i<10;I++)
  
The enhanced for loop looks like this: `for(Number n : Numbers.getList())`
  
In C# the foreach loop looks like this: `foreach (Number n in Numbers.getList())`

Java doesn't have a goto statement but you can accomplish the same thing by using a label...see you can still write spaghetti code 🙂

**Switch statement**
  
The switch statement works with byte,short, int, char and enum in Java before version 7, Java version 7 added the ability to use strings as well

The switch statement with the string data type is case sensitive
  
This will print hello

```java
String test ="hello";
		switch(test){
		case "Hello":
			System.out.println("Hello");
			break;
		case "hello":
			System.out.println("hello");
			break;
		default:
			System.out.println("default");
			break;
		}

```
**Integer data types**
  
No null values like in .net where you would use int?
  
you can use _ in constants for readability reasons so for example 2_000 and 2000 are the same
  
There are no underflow or overflow error, results will wrap around

```java
byte x =127;
System.out.println(x);
x+= 1;
System.out.println(x);
```

That will print
  
127
  
-128

Java doesn't have the checked keyword like in c#, you need to roll your own if you want to prevent this kind of stuff

If you try assigning 128 to a byte, you will get an error, it won't become -128

```java
byte x =128;
System.out.println(x);
```

Exception in thread "main" java.lang.Error: Unresolved compilation problem:
  
Type mismatch: cannot convert from int to byte
  
at com.denis.MainClass.main(MainClass.java:12)

You also can't convert straight from an int to a string

```java
String s = "123";
int z = s;
System.out.println(z);
```

Exception in thread "main" java.lang.Error: Unresolved compilation problem:
  
Type mismatch: cannot convert from String to int

Here is one way to do the conversion by using the parseInt method of the Integer wrapper type

```java
String s = "123";
int z = Integer.parseInt(s);
System.out.println(z);
```

Looks like Java doesn't have TryParse like c#

To test if strings are the same use equals or equalsIgnoreCase
  
Here is what is the output of the following tests

```java
String a1 = "A";
String a2 = "a";
String a3 = "a";
System.out.println(a1 == a2); 			//false
System.out.println(a1 == a3); 			//false
System.out.println(a1.equals(a2));  		//false
System.out.println(a1.equalsIgnoreCase(a2)); 	//true
```

This one is similar to SELECT 5/2 in SQL Server, it does integer math, however since answer is a double, you get back 2.0

```java
double answer;
answer = 5/2;
System.out.println(answer); // 2.0
```

You need to change either of the two integers in order to get back a result that is not an integer

The stack holds all local variables and temporary variables, there is a separate stack for each thread, variables on the stack must be initialized explicitly
  
The heap holds memory that is dynamically allocated with new, heap memory is shared between threads, variables on the heap are initialized automatically to default values
  
Objects are always on the heap

Primitive arguments are always passed by value
  
Objects and arrays are passed by reference

We covered StringBuffer and StringBuilder, StringBuilder is not thread safe but is faster than StringBuffer if accesed by a single thread

**varargs**
  
varargs is a variable-length argument list

public static void main(String[] args)
  
public static void main(String... args)

public static void main(String... args){
      
for (String s: args ) System.out.println(s);
    
}

Arrays and Objects instantiation, constructors, default constructors were also covered. We covered much more than I wrote down, I was busy with listening and doing the labs so didn't have the opportunity to write it all down