---
title: Detecting Property Changes in an Observable Collection
author: ThatRickGuy
type: post
date: 2010-04-07T19:16:01+00:00
ID: 753
excerpt: A quick and simple demo on how to set up a custom ObservableCollection to raise PropertyChanged events from the items it contains.
url: /index.php/webdev/webdesigngraphicsstyling/detecting-property-changes-in-an-observa/
views:
  - 39573
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - binding
  - silverlight

---
### Work, Work, Work

I was working on a new interface this week for a rather complex item selection system. It deals with hierarchies and mandatory selections, multi-selects and single-selects. In order to make it easier for users to see what items were already selected, there was also a list of just the selected items.

### Oh ObservableCollection, I See You!

I toyed around with a few different options, and debated some of them out with Adam, and finally decided to run with binding and an Observable Collection.

### Everything is better with LINQ

One of the great things about observable collections is that you can pass them around like candy, make a change to it from any number of locations, and it will update all of the controls bound to it. Mix in a little LINQ and you&#8217;ve got some awesome functionality with very little code.

### What Do You Mean CollectionChanged Doesn&#8217;t Fire?

One of the things I needed to do though, was to detect when the user selected an item from the collection. When the IsChecked flag was flipped, I needed to go to the server to get any child items. But the Observable Collection does not expose property changed events, which started me down this path. 

### The User Interface

For this demo app, the UI is pretty simple. I just have two list boxes. Each list box has a DataTemplate with a CheckBox in it. The CheckBox has its Content bound to the &#8220;Text&#8221; member and IsChecked is bound to a like named member. The IsChecked property is also set to TwoWay mode. This means that when the user changes its checked state, it will update the underlying collection.

```XAML
<ListBox x:Name="lst1" Grid.Column="0" >
            <ListBox.ItemTemplate >
                <DataTemplate>
                    <CheckBox Content="{Binding Text}" IsChecked="{Binding IsChecked, Mode=TwoWay}" />
                </DataTemplate>
            </ListBox.ItemTemplate>
        </ListBox>
```

### On to the Items

For the most part the Item class just contains our properties, but we have to beef it up just a bit. First, it needs <code class="codespan">Implements System.ComponentModel.INotifyPropertyChanged</code>. INotifyPropertyChanged will create a public event called &#8220;PropertyChanged&#8221;. In order to save ourselves a bit of repeating code, we can make a method called _onPropertyChanged_ that raises that event for us:

```VB.Net
Private Sub onPropertyChanged(ByVal PropertyName As String)
            RaiseEvent PropertyChanged(Me, New System.ComponentModel.PropertyChangedEventArgs(PropertyName))
        End Sub
```

Also in the item class we need to tweak the properties a bit. For instance:

```VB.Net
Private _IsChecked As Boolean
        Public Property IsChecked() As Boolean
            Get
                Return _IsChecked
            End Get
            Set(ByVal value As Boolean)
                If value <> _IsChecked Then
                    _IsChecked = value
                    onPropertyChanged("IsChecked")
                End If
            End Set
        End Property
```

Note that we are only calling the onPropertyChanged event when the value has indeed changed. Also, ensure that the underlying value has changed BEFORE you call the onPropertyChanged, otherwise you&#8217;ll wind up with some really interesting behavior.

### The Collection

The Observable collection doesn&#8217;t expose the PropertyChanged event by default, so we have to bubble it up ourselves. That means we need to make a new class that inherits from ObservableCollection. In order to get a handler on the Items PropertyChanged events, we need to override the InsertItem and RemoveItem methods. Inside them we Add and Remove an event handler for the Property Changed event. And in the procedure that handles that event, all we need to do is to raise our new public event.

### Back to the MainPage

In the code of the main page, we need an instance of the collection, using the WithEvents keyword will make it exceptionally easy to attach to our events. In the constructor, we can set the ItemSource of both of our list boxes to that collection. And if you check the objects/events drop downs, you should see that our collection now exposes both a CollectionChanged and ItemPropertyChanged event. Put a message box or break point in each and fire up the app.

### Running

When you run it, you should see a number of identical items in each list box. When you click on the check boxes of either list, you should see the ItemPropertyChanged event fire, after which both list boxes should reflect the new value in the collection.

### Show me the Demo



### Show Me the Code

Working VS 2008 solution can be downloaded from [here][1].

 [1]: http://ringdev.com.web10.reliabledomainspace.com/code/bindingdemo/trainingdemo2.zip