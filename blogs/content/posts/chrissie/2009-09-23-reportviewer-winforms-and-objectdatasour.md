---
title: Reportviewer, winforms and objectdatasources
author: Christiaan Baes (chrissie1)
type: post
date: 2009-09-23T05:30:37+00:00
ID: 564
url: /index.php/desktopdev/mstech/reportviewer-winforms-and-objectdatasour/
views:
  - 5438
categories:
  - Microsoft Technologies
tags:
  - objectdatasource
  - rdlc
  - vb.net

---
I kinda like the rdlc/reportviewer reports in VS2008. It is not what you can call high tech reporting but I think it will do everything I need it to do. 

But the question is do we do reporting on the domainmodel or a separate viewmodel/reportingmodel? Or do we just use datasets and custom sql?

Well, I am a guy that tends to use objects, so I like to go the object source path. And the Microsoft reports have some support for this. However the support is not as great as I have seen in other products. Now I see no reason why you would not make your reports use the database directly, after all domainmodels are all about [OLTP][1] and not [OLAP][2]. 

I have not yet found a way to easily bind against list/collections. You always have to make a subreport for that, which seems a bit over the top. Perhaps we can have this in 2010. 

But I would always recommend making a viewmodel for the reports you need to make, and for the following reasons:

  * It will make live easier when you have to switch reporting engines (which I had to do)
  * reporting data is all about strings: do the formatting in the viewmodel and give the report strings (sometimes images)
  * the formatting is easier in the viewmodel than it is in most reporting engines

Let&#8217;s give you an example with rdlc as to why it is easier to use a viewmodel instead of reporting against the domainmodel.

The domainmodel looks something like this:

```vbnet
Public Class Invoice
    Private _total As Decimal
    Private _customer As Customer
    Private _invoiceDate As Date
End Class

Public Class Customer
    Private _lastname As String
    Private _firstname As String
End Class
Public ```
You will just have to imagine all the properties and constructors that go with that model.

Now I have to make a report that gives me an invoice with a field that joins lastname and firstname of the customer. And for some bizarre reason, customer is not a required field. This means that customer can be nothing.

If customer is nothing and you have
  


> =First(Fields!Customer.Value.LastName) & &#8221; &#8221; & First(Fields!Customer.Value.FirstName)</p>
in your value of the textbox, then it will show you
  


> #Error</p>
which is not a desired effect we would like it empty or say &#8220;No customer needed&#8221;.

You could try this
  


> =iif(First(Fields!Customer.Vaulue is nothing,&#8221;no customer needed&#8221;,First(Fields!Customer.Value.LastName) & &#8221; &#8221; & First(Fields!Customer.Value.FirstName)</p>
but that will still give you #Error, since in the iif all the members are evaluated. So you have to take more drastic measures.
  
You will have to make a custom code like [this solution][3] shows.

Something like:

```vbnet
Public Function ConvertValue(ByVal value As Object) As String
    If IsNothing(value) Then
        Return Nothing
    Else
        Return Format(CDec(value),"(###) ###-####") 
   End If
End Function```
Or you could change the domainmodel and add a property Lastnameandfirstname. Anyway, I think the better solution is to do something like this:

```vbnet
Public Class ReportInvoice
    Private _total As String
    Private _customerNameandfirstname As String
    Private _invoicedate As String

    Public Sub New(ByVal Invoice As Invoice)
        _total = Invoice.Total.ToString("0.00 €")
        If Invoice.Customer IsNot Nothing Then
            _customerNameandfirstname = Invoice.Customer.Lastname & " " & Invoice.Customer.Firstname
        Else
            _customerNameandfirstname = "customer not known"
        End If
        _invoicedate = Invoice.InvoiceDate.ToString("dd/MM/yyyy")
    End Sub
End Class```
This will make it very easy to design the report since all the fields are there and when you switch reporting engines, making the new reports will be a lot faster since you don&#8217;t have to rewrite all the custom code.

Does that make sense?

 [1]: http://en.wikipedia.org/wiki/OLTP
 [2]: http://en.wikipedia.org/wiki/Online_analytical_processing
 [3]: http://social.msdn.microsoft.com/Forums/en-US/vsreportcontrols/thread/cbed4e88-16d4-4d4b-821a-7a7e728c7e34