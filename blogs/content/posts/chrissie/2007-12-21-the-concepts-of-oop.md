---
title: The concepts of OOP
author: Christiaan Baes (chrissie1)
type: post
date: 2007-12-21T14:24:36+00:00
ID: 20
url: /index.php/webdev/serverprogramming/aspnet/the-concepts-of-oop/
views:
  - 2946
categories:
  - ASP.NET

---
# My explanation



Well there seems to be some confusing opinions on the net over what OOP seems to be. I&#8217;ve been reading up on the concepts of OOP after a little discussion with our teacher yesterday where he did most of the talking.



He followed the APIE principal



  * A = Abstraction
  * P = Polymorphism
  * I = Inheritance
  * E = Encapsulation



## 1. Abstraction



Not many sites talk about this concept as part of OOP but apparantly this means that abstract real world things to your code, for example Car, Vehicule, Sportscar, &#8230; become objects. So you try to mimic Real world objects into an OO model.



## 2. Polymorphism



According to the teacher (who I hope will read this and orrect me), polymorphism is the fact that a certain object can be different at designtime then at runtime.



For example: A car is a vehicle and a sporstcar is a car. A vehicle has a max-speed of 80 and a car a max-speed of a 100 and sportscar a max-speed of 120. They all share the same method getspeed. At designtime you do this.



More or less pseudocode.



Vehicle vehicle = new Vehicle();
  
print vehicle.getspeed(); // This will give you 80



Vehicle vehicle = new Car();
  
print vehicle.getspeed(); // This will give you 100



Vehicle vehicle = new SportsCar();
  
print vehicle.getspeed(); // This will give you 120



So the vehicle is always of type Vehicle but depending on the implementation it will return a different speed.



## 3. Inheritance



  * The subclass inherits the methods of its superclass.
  * The subclass can add methods other then the once of the superclass.
  * The subclass can implement a method of the superclass differently via overriding.



## 4. Encapsulation



The hiding of your private attributes via public getters and setters. 



# Other explanations

I can live with the above, but I also like the opinion of others and they seem to differ somewhat.



This site seems to be in agreement somewhat. http://www.startvbdotnet.com/oop/default.aspx



This site seems to have a different concept of polymorphism. http://www.answers.com/topic/polymorphism-in-object-oriented-programming
  
According to them polymorphism is nothing more then overloading and overriding 



This professor of an Austin, TE college seems to forget about abstraction.
  
Here he talks about Encapsulation. http://www.developer.com/java/article.php/935351
  
Here about Polymorphism. http://www.developer.com/tech/article.php/966001
  
Here about Inheritance. http://www.developer.com/java/article.php/957381



And this one tries to be to simple. Starting here. http://homepages.north.londonmet.ac.uk/~chalkp/proj/ootutor/inheritance.html



So if anyone can come up with a better site please do.