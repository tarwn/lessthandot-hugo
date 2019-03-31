---
title: Java Training Day 3
author: SQLDenis
type: post
date: 2012-11-28T23:42:00+00:00
excerpt: |
  Today was day three of our Java training, we did mostly OOP stuff today. I also noticed that whenever you do a week of training by the team that Wednesday PM comes along, you are pretty much mush.
  
  We covered Object Oriented design, why you would use&hellip;
url: /index.php/enterprisedev/application-lifecycle-management/java-training-day-3/
views:
  - 14722
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management

---
Today was day three of our Java training, we did mostly OOP stuff today. I also noticed that whenever you do a week of training by the team that Wednesday PM comes along, you are pretty much mush.

We covered Object Oriented design, why you would use it, we covered Encapsulation, Inheritance and Polymorphism. You encapsulate functionality in classes, you hide the implementation so that the classes can be treated as black boxes, the person using your classes does not need to know how stuff is implemented, all the person needs to know is what the class does. Inheritance enables you to basically make a copy of a class.
  
Polymorphism enables you to change the behavior from the class that you are inheriting from. For example if in your animal class you would have a soundsLike method, if you now have a dog and a cat class, your soundsLike method would then return woof in the dog class and meow in the cat class, you would do this by overiding the method from the super class

We also covered Access Control Modifiers, there is public, protected, private and default (defaults to the package)

Private
  
Only accessible from withing the class

Protected
  
Only accessible from withing the class and from sublasses

Public
  
Accessible from code in any package

We covered constructors, what its function is and that the compiler will create an empty constructor if you don&#8217;t create your own, the constructor has the same name as the class. Java doesn&#8217;t have a destructor since it is garbage collected but you can implement a finalize method

We also looked at nested classes, basically a class that is declared inside another class. POJOs (Plain Old Java Objects) were also explained, a good example would be a JavaBean that you would use in JSP, JavaBeans have public get and set methods and also a public no-argument constructor.

Next up we looked at the javadoc utility, this utility is used to create documentation in a standard format.

Even though Java doesn&#8217;t have multiple inheritance like c++, you can &#8216;inherit&#8217; from more than one interface. An interface is basically a code contract, when you inherit (implement) an interface you have to implement all the methods defined in the interface. An interface does not have an implementation, it has methods with empty bodies, it is up to the class that implements the interface to create the method code

We looked at upcasting (casting to a superclass type) and downcasting (casting to a subclass, this will only work if the object is a compatible type)
  
You can use the instanceof operator to check the class of an object

Finally we looked at Annotations and Enumerated Types

Here is an example of an enum from the java docs http://docs.oracle.com/javase/tutorial/java/javaOO/enum.html

<pre>public enum Day {
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY 
}</pre>

Here is an example of a switch statement using the enum

<pre>public class EnumTest {
    Day day;
    
    public EnumTest(Day day) {
        this.day = day;
    }
    
    public void tellItLikeItIs() {
        switch (day) {
            case MONDAY:
                System.out.println("Mondays are bad.");
                break;
                    
            case FRIDAY:
                System.out.println("Fridays are better.");
                break;
                         
            case SATURDAY: case SUNDAY:
                System.out.println("Weekends are best.");
                break;
                        
            default:
                System.out.println("Midweek days are so-so.");
                break;
        }
    }
    
    public static void main(String[] args) {
        EnumTest firstDay = new EnumTest(Day.MONDAY);
        firstDay.tellItLikeItIs();
        EnumTest thirdDay = new EnumTest(Day.WEDNESDAY);
        thirdDay.tellItLikeItIs();
        EnumTest fifthDay = new EnumTest(Day.FRIDAY);
        fifthDay.tellItLikeItIs();
        EnumTest sixthDay = new EnumTest(Day.SATURDAY);
        sixthDay.tellItLikeItIs();
        EnumTest seventhDay = new EnumTest(Day.SUNDAY);
        seventhDay.tellItLikeItIs();
    }
}</pre>

The output is

Mondays are bad.
  
Midweek days are so-so.
  
Fridays are better.
  
Weekends are best.
  
Weekends are best.

Who wrote that&#8230;Monday is my favorite day 🙂

That is it for today&#8230;tomorrow we will start on generics which of course in java is just syntactic sugar unlike in c#, java uses a technique called erasure&#8230;..the generic type is erased at runtime

From the Java docs

[Type Erasure][1]

> Generics were introduced to the Java language to provide tighter type checks at compile time and to support generic programming. To implement generics, the Java compiler applies type erasure to:
> 
> Replace all type parameters in generic types with their bounds or Object if the type parameters are unbounded. The produced bytecode, therefore, contains only ordinary classes, interfaces, and methods.
  
> Insert type casts if necessary to preserve type safety.
  
> Generate bridge methods to preserve polymorphism in extended generic types.
  
> Type erasure ensures that no new classes are created for parameterized types; consequently, generics incur no runtime overhead. 

The reason they did this was for backwards compatibility, they did not need to odify the JVM. Here is also a nice post: [Generics in C#, Java, and C++][2], Anders Hejlsberg with Bill Venners and Bruce Eckel talking about the differences

 [1]: http://docs.oracle.com/javase/tutorial/java/generics/erasure.html
 [2]: http://www.artima.com/intv/generics2.html