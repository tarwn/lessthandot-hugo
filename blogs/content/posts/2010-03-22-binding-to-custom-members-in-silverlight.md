---
title: Binding to Custom Properties in Silverlight
author: ThatRickGuy
type: post
date: 2010-03-22T12:52:17+00:00
ID: 645
excerpt: 'The Binding system is Silverlight is pretty cool. It lets us easily bind property values of UI Elements to all sorts of good stuff. But, if you want to create your own custom bind-able properties, it takes a bit more effort than the usual Property="{Binding=Field}" in XAML.'
url: /index.php/webdev/webdesigngraphicsstyling/binding-to-custom-members-in-silverlight/
views:
  - 6079
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - silverlight binding

---
The Binding system is Silverlight is pretty cool. It lets us easily bind property values of UI Elements to all sorts of good stuff. But, if you want to create your own custom bind-able properties, it takes a bit more effort than the usual Property="{Binding=Field}" in XAML.

For example, I have a datagrid. One of the columns in that data grid holds a control that displays and icon and a tool tip based on the content of some data.

So you would start with a control like this:

```VB.Net
Imports System
Imports System.Windows
Imports System.Windows.Controls
Imports System.Windows.Media
Imports System.Windows.Media.Animation
Imports System.Windows.Shapes

Partial Public Class CommentIndicator 
	Inherits UserControl

	Public Sub New()
		' Required to initialize variables
		InitializeComponent()
	End Sub

    Public Overrides Function ToString() As String
        Return MyToolTip
    End Function

    Public Property MyToolTip() As String
        Get
            Dim o As Object = Me.GetValue(ToolTipService.ToolTipProperty)
            Dim sReturn As String = String.Empty
            If Not o Is Nothing Then
                sReturn = o.ToString
            End If
            Return sReturn
        End Get
        Set(ByVal value As String)
            Me.SetValue(ToolTipService.ToolTipProperty, value)
        End Set
    End Property
End Class
```
Then, in the XAML of the data grid:

```
<data:DataGrid.Columns>
	<data:DataGridTextColumn x:Name="dgcOrderNumber" CanUserReorder="False" CanUserSort="True" Header="Order Number" SortMemberPath="OrderNumber" Binding="{Binding OrderNumber}" Width="118"/>
	<data:DataGridTextColumn CanUserReorder="False" CanUserSort="True" Header="Request Date" SortMemberPath="RequestDate" Binding="{Binding RequestDate, Converter={StaticResource DateConverter}}" Width="110"/>
	<data:DataGridTextColumn CanUserReorder="False" CanUserSort="True" Header="Status" SortMemberPath="Status" Binding="{Binding Status}" Width="100"/>
	<data:DataGridTemplateColumn CanUserSort="True" CanUserReorder="False" IsReadOnly="True" SortMemberPath="Comment" Width="26" >
		<data:DataGridTemplateColumn.CellTemplate> 
			<DataTemplate> 
                        	<Local:CommentIndicator MyToolTip="{Binding Comment}"  Width="15" Height="17"  />
			</DataTemplate> 
		</data:DataGridTemplateColumn.CellTemplate> 
	</data:DataGridTemplateColumn>               
</data:DataGrid.Columns>
```
Everything looks good, but running the app will pop one of Silverlight completely unenlightening errors.

What we need to do is to set up MyToolTip as a dependency property. We just have to add a bit of code to the custom control:

```VB.Net
Public Shared ReadOnly _MyToolTipProperty As DependencyProperty = DependencyProperty.Register("MyToolTip", GetType(String), GetType(CommentIndicator), New PropertyMetadata(New PropertyChangedCallback(AddressOf MyToolTipCallback)))
    Private Shared Sub MyToolTipCallback(ByVal d As DependencyObject, ByVal e As DependencyPropertyChangedEventArgs)
        d.SetValue(ToolTipService.ToolTipProperty, e.NewValue)
    End Sub
```
And that fixes that. Why this wasn't just handled behind the scenes I don't know. I've heard that they have done a lot of work on the Binding system for SL4, so maybe this will not be a requirement once the next update rolls out. 

-Rick