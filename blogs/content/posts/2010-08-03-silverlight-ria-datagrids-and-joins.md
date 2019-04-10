---
title: Silverlight, RIA, Datagrids, and Joins
author: ThatRickGuy
type: post
date: 2010-08-03T13:16:43+00:00
ID: 860
url: /index.php/webdev/webdesigngraphicsstyling/silverlight-ria-datagrids-and-joins/
views:
  - 6760
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - binding
  - entity framework
  - ria
  - silverlight

---
Lately we've been playing around with RIA services for Silverlight. Using them we lose the use of our stack, but the speed of development with them is quite impressive. 

## Watch and Learn

If you are unfamiliar with RIA in Silverlight, I would recommend this video: <http://www.silverlight.net/learn/videos/all/net-ria-services-intro/>. It is a complete walk through of creating a demo from the 'Business Application' template in Visual Studio for SL 3. If you are working in SL4, be sure to check the comments in the video for the breaking changes.

## What's the Skinny?

It's pretty fancy stuff. You make an Entity Framework model, based on your database. Then when you compile, the template generates a whole bunch of code for you. First, on the server it generates a Service and a Metadata class to expose your data entities and basic CRUD functionality to the client. Then in the client, another class is generated that handles the serialization and exposing of those server classes client side. One of the most impressive things about this interaction though, is that the return objects from the server are IQueriable, not arrays. Which gives you the ability to perform LINQ and deferred execution from the client!

## So Why the Fuss?

Watch the video, play around with it. I'm not going to go through the whole thing here, but I will show you something I ran into.

I have a relational database, in this case I am looking at the _League_ and _Player_ tables. _League_ has an OwnerPlayer_ID field that links to the _Player_ table. The problem though, by using the RIA services and bindings out of the box, is that while the League class has a .Player property, it will never be populated. And showing people that a League is owned by Player #5237 isn't nearly as helpful as showing them the Player's name. We have to make 2 changes to get this to work correctly.

## Reprimanding that Lazy EF

On the server side, find your service class. It should be called something like 'MyApplicationDomainService1' and be in the 'Services' folder by default. In that class look for a method called 'GetMyObjects' where MyObjects is the collection you are binding to on the client side. It should contain something like 'Return (Me.ObjectContext.MyObjects)'. Change it to:
  
<code =VB>Return (Me.ObjectContext.MyObjects.Include("MyChildObject")</code>
  
In my case this was the 'GetLeagues' method and it contains: 'Return (Me.ObjectContext.Leagues.Include(“Player”))'

This will tell the Entity Framework model that it should Eager Load the .Player property. But before we rejoice and rebuild, wait, there's more!

## Getting RIA up off the Couch

Next up, right next to the DomainService1 file should be a file called MyApplicationDomainService1.metadata. In this file we need to find the class that represents what we are querying (The “MyObject” from the previous step). In that class you should see the property you want to load (The “MyChildObject” from above). On this property, you want to prepend the <Include()> attribute. This will tell the RIA services that it should include this property in the serialization and transmission of the query results. 

In my case of League and Owner Player, it looks like this:

```VB
Friend NotInheritable Class LeagueMetadata
             
        '...

        <Include()>
        <Display(AutoGenerateField:=False)>
        Public Property Player As Player
        '...
    End Class
```
## And the Grid?

Now that the EF is loading the data we want, and the RIA services are returning the data we want, all we have to do is bind it! And this is the easiest part. It is just like they show in the video, with one minor change. Instead of just binding on the primary object's property, we use dot notation to get at the child object's property. Shown here as the “Player.Name” binding

```XML
<sdk:DataGrid x:Name="grdLeagues" Margin="4,20,4,4" Grid.Row="1" ItemsSource="{Binding ElementName=LeagueDomainDataSource, Path=Data}" AutoGenerateColumns="False">
    <sdk:DataGrid.Columns>
        <sdk:DataGridTextColumn Binding="{Binding Path=Name}" Header="Name" />
        <sdk:DataGridTextColumn Binding="{Binding Path=Player.Name, Mode=OneWay}" Header="Organizer" />
        <sdk:DataGridTextColumn Binding="{Binding Path=Province}" Header="State" />
        <sdk:DataGridTextColumn Binding="{Binding Path=City}" Header="City" />
        <sdk:DataGridTextColumn Binding="{Binding Path=LGS}" Header="Game Store" />
    </sdk:DataGrid.Columns>
</sdk:DataGrid>
```

-Rick