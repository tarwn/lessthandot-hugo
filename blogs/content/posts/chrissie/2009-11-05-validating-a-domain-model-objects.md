---
title: Validating a domain model/objects
author: Christiaan Baes (chrissie1)
type: post
date: 2009-11-05T08:05:31+00:00
ID: 587
url: /index.php/desktopdev/mstech/validating-a-domain-model-objects/
views:
  - 4370
categories:
  - Microsoft Technologies

---
One of the biggest problems you will encounter in your career as a developer is pesky users. They always seem to enter the wrong data. So we need to validate their input into your brilliant system. 

There are several ways of doing this when using a domain model.

First, you could make sure that all your properties and constructors are designed in such a way that your object can never be in an invalid state. [I wrote about this before][1]. The biggest problem with this, is that it can get problematic really fast. I would therefore only advise such an approach for simple objects. Really simple objects. 

The better thing to do is to create a validating framework. You can read [an excellent article by JP Boodhoo][2]. He describes how to do this in a C# kind of manner. He shows how to make a validation framework and adds a Validate method to the object. This is one method to do it. But his framework works just as well for an external framework where you pass in the object to be validated. And that is what my code in VB.Net does. I also don&#8217;t use predicates but the newer Func(of T).

First the interface for the BusinessRule:

```vbnet
Namespace Model.Validation
    Public Interface IBusinessRule(Of T)
        ReadOnly Property Name() As String
        ReadOnly Property Description() As String
        Function IsBrokenBy(ByVal Item As T) As Boolean
    End Interface
End Namespace```
Then an implementation for this Interface:

```vbnet
Namespace Model.Validation
    Public Class BusinessRule(Of T)
        Implements IBusinessRule(Of T)

        Private _name As String
        Private _description As String
        Private _action As Func(Of T, Boolean)

        Public Sub New(ByVal Name As String, ByVal Description As String, ByVal Condition As Func(Of T, Boolean))
            _action = Condition
            _name = Name
            _Description = Description
        End Sub

        Public Function IsBrokenBy(ByVal Item As T) As Boolean Implements IBusinessRule(Of T).IsBrokenBy
            Return Not _action(Item)
        End Function

        Public ReadOnly Property Description() As String Implements IBusinessRule(Of T).Description
            Get
                Return _description
            End Get
        End Property

        Public ReadOnly Property Name() As String Implements IBusinessRule(Of T).Name
            Get
                Return _name
            End Get
        End Property
    End Class
End Namespace```
Of course you will always have more than one rule per object so we need an IBusinessRuleSet:

```vbnet
Namespace Model.Validation
    Public Interface IBusinessRuleSet(Of T)
        Function Messages(ByVal Item As T) As IList(Of String)
        Function IsBroken(ByVal Item As T) As Boolean
        Function BrokenBy(ByVal Item As T) As IList(Of IBusinessRule(Of T))
        ReadOnly Property RulesCount() As Integer
        ReadOnly Property BrokenRulesCount() As Integer
    End Interface
End Namespace```
And an implementation:

```vbnet
Namespace Model.Validation
    Public Class BusinessRuleSet(Of T)
        Implements IBusinessRuleSet(Of T)

        Private _rules As IList(Of IBusinessRule(Of T))
        Private _brokenRules As IList(Of IBusinessRule(Of T))

        Public Sub New(ByVal Rules As IList(Of IBusinessRule(Of T)))
            _rules = Rules
        End Sub

        Public Function BrokenBy(ByVal Item As T) As System.Collections.Generic.IList(Of IBusinessRule(Of T)) Implements IBusinessRuleSet(Of T).BrokenBy
            _brokenRules = New List(Of IBusinessRule(Of T))
            For Each _rule In _rules
                If _rule.IsBrokenBy(Item) Then
                    _brokenRules.Add(_rule)
                End If
            Next
            Return _brokenRules
        End Function

        Public ReadOnly Property BrokenRulesCount() As Integer Implements IBusinessRuleSet(Of T).BrokenRulesCount
            Get
                If _brokenRules Is Nothing Then
                    Return 0
                End If
                Return _brokenRules.Count
            End Get
        End Property

        Public ReadOnly Property RulesCount() As Integer Implements IBusinessRuleSet(Of T).RulesCount
            Get
                Return _rules.Count
            End Get
        End Property

        Public Function IsBroken(ByVal Item As T) As Boolean Implements IBusinessRuleSet(Of T).IsBroken
            If BrokenBy(Item).Count &gt; 0 Then Return True
            Return False
        End Function

        Public Function Messages(ByVal Item As T) As System.Collections.Generic.IList(Of String) Implements IBusinessRuleSet(Of T).Messages
            Dim _l As New List(Of String)
            For Each _rule In BrokenBy(Item)
                _l.Add(_rule.Description)
            Next
            Return _l
        End Function
    End Class
End Namespace```
And now for the Validator class that you will be using all over the place if needed 😉 :

```text
Namespace Model.Validation
    Public Interface IValidator(Of T)
        Function IsValid(ByVal Item As T) As Boolean
        Function Validate(ByVal Item As T) As IList(Of IBusinessRule(Of T))
        Function Messages(ByVal Item As T) As IList(Of String)
    End Interface
End Namespace```
And the implementation:

```vbnet
Imports TDB2009.Dal.Model.Common.Interfaces

Namespace Model.Validation
    Public MustInherit Class Validator(Of T As IDomainEntity)
        Implements IValidator(Of T)

        Private _rules As IBusinessRuleSet(Of T)

        Public Sub New(ByVal Rules As IBusinessRuleSet(Of T))
            _rules = Rules
        End Sub

        Public Function IsValid(ByVal Item As T) As Boolean Implements IValidator(Of T).IsValid
            Return Not _rules.IsBroken(Item)
        End Function

        Public Function Validate(ByVal Item As T) As IList(Of IBusinessRule(Of T)) Implements IValidator(Of T).Validate
            Return _rules.BrokenBy(Item)
        End Function

        Public Function Messages(ByVal Item As T) As IList(Of String) Implements IValidator(Of T).Messages
            Return _rules.Messages(Item)
        End Function
    End Class
End Namespace```
And this is an example of an implemented BusinessRuleset:

```vbnet
Imports TDB2009.Dal.Model.Validation

Namespace Model.AnalysisManagement.Validators.Rulesets
    Public Class FiberTypeRulesSet
        Inherits BusinessRuleSet(Of FiberType)
        Implements Interfaces.IFiberTypeRulesSet

        Public Sub New()
            MyBase.New(MakeRules)
        End Sub

        Private Shared Function MakeRules() As IList(Of IBusinessRule(Of FiberType))
            Dim _Rules As New List(Of IBusinessRule(Of FiberType))
            _Rules.Add(New BusinessRule(Of FiberType)("NotEmptyDescriptionEn", "Description En can not be empty", Function(x) Not String.IsNullOrEmpty(x.Description.En)))
            _Rules.Add(New BusinessRule(Of FiberType)("NotEmptyDescriptionFr", "Description Fr can not be empty", Function(x) Not String.IsNullOrEmpty(x.Description.Fr)))
            _Rules.Add(New BusinessRule(Of FiberType)("NotEmptyDescriptionNl", "Description Nl can not be empty", Function(x) Not String.IsNullOrEmpty(x.Description.Nl)))
            _Rules.Add(New BusinessRule(Of FiberType)("NotEmptyKey", "Key can not be empty", Function(x) Not String.IsNullOrEmpty(x.FiberType)))
            Return _Rules
        End Function
    End Class
End Namespace```

 [1]: /index.php/All/?p=321
 [2]: http://blog.jpboodhoo.com/ValidationInTheDomainLayerTakeOne.aspx