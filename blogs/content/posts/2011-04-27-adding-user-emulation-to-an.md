---
title: Adding User Emulation to an Application
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-04-27T12:57:00+00:00
excerpt: "One of the tricks I picked up from my last job (and our forum software, now that I think of it) is the idea of user emulation. I could log into the application, search for a user, and, at the push of a button, temporarily become that user. The only differences between emulating them and actually logging in as them were a black bar that indicated who I am (with a link to stop emulating), all audit records continued to reflect my own user id, and I didn't need to keep track of 30 different sample accounts and passwords."
url: /index.php/architect/designingsoftware/adding-user-emulation-to-an/
views:
  - 13449
rp4wp_auto_linked:
  - 1
categories:
  - Designing Software

---
One of the tricks I picked up from my last job (and our forum software, now that I think of it) is the idea of user emulation. I could log into the application, search for a user, and, at the push of a button, temporarily become that user. The only differences between emulating them and actually logging in as them were a black bar that indicated who I am (with a link to stop emulating), all audit records continued to reflect my own user id, and I didn&#8217;t need to keep track of 30 different sample accounts and passwords.

As I said, it&#8217;s a neat trick. 

## Advantages

Implemented consistently, this stops being just a trick and becomes a very powerful tool. Developers, QA, even customer service and support see benefits from being able to quickly emulate end users.

### Debugging

As developers the largest benefit for us is the ability to debug our systems from a variety of viewpoints. Rather than going through the trouble of creating and managing sample users for every impacted role, we can use existing user records with real data behind them. This not only reduces the time to start debugging and remove the time involved in ongoing maintenance of test accounts, but may actually force out a few extra bugs that we wouldn&#8217;t catch with a new, vanilla user account.

### Support

Duplicating a bug or answering a question becomes a lot easier when we can emulate the person on the other end of the bug report or phone. We can emulate the person in our production environment and in our development environment and verify that both environments break in the same way (or that development doesn&#8217;t break after we fix it). We also get firsthand clues, which can knock hours off the bug-hunting process.

### Customer Service

Just as developers and support benefit on the technical side, in some cases Customer Service representatives (or selected members of the business) can use emulation to provide business or first level user support. When an end user has a complex question about an order or report, the service representative no longer needs to imagine their way through the issue, but instead can emulate that user, walk through the process, and see exactly what their user is seeing. This can be even more critical in systems where users see only a subset of the functionality or data available to service representatives.

## Implementation

So emulation is a useful tool as well as a neat trick. Like many things, it is generally easier to bake this in from the beginning than to add it after the fact. If the user information is accessed in a consistent fashion in existing software, it is possible to squeeze in emulation logic and clean up the few places people cut corners and accessed users outside the normal context. If the user information is loaded and accessed at will throughout the application, adding emulation will be much harder (though there is some additional benefit in that it forces you to clean up your architecture a bit).

**Sample Session Context Class**

<pre>Public Class SessionContext

	Private Property EmulatedUser As User
	Private Property LoggedInUser As User

	Public ReadOnly Property User As User
		Get
			If Me.EmulatedUser IsNot Nothing Then
				Return Me.EmulatedUser
			Else
				Return Me.LoggedInUser
			End If
		End Get
	End Property

	Public ReadOnly Property IsEmulating As Boolean
		Get
			Return Me.EmulatedUser IsNot Nothing
		End Get
	End Property

	''' &lt;summary&gt;
	''' Property used for accessing current's users information for auditing
	''' &lt;/summary&gt;
	Public ReadOnly Property UserIdForAuditing As Integer
		Get
			If Me.LoggedInUser IsNot Nothing Then Return Me.LoggedInUser.UserID
			Return 0
		End Get
	End Property

	Public Sub LogInUser(ByVal newUser As User)
		Me.LoggedInUser = newUser
		Me.EmulatedUser = Nothing
		' plus other login logic stuff
	End Sub

	Public Sub LogOutUser()
		Me.LoggedInUser = Nothing
		Me.EmulatedUser = Nothing
	End Sub

	Public Sub StartEmulating(ByVal selectedUser As User)
		Me.EmulatedUser = selectedUser
	End Sub

	Public Sub StopEmulating()
		Me.EmulatedUser = Nothing
	End Sub
End Class</pre>

Altogether not that complex a code construct, although I&#8217;m sure it will grow more so over the lifetime of the application. 

As long as we consistently access user information through the exposed User property in the session and user the exposed UserIdForAuditing property for auditing purposes, then most of the work is done for us. The only other piece we need is a button on the UI to start emulating and some logic to handle the danger below.

## Dangers

There are two main dangers to watch for. The first danger is making sure emulation doesn&#8217;t grant users the ability to promote themselves. Either emulation needs to be reserved for administrative users, or logic needs to be added to make certain levels or roles unavailable for emulation (or assignment during emulation).

The other main danger is that you now have a much greater chance (probably guarantee) that you will have the same user &#8220;logged in&#8221; in two locations at once. Most applications handle this just fine, but there are also many that cannot. Examples of this behavior are enforcing single location sign-ons, coded security that assumes multiple location means an account has been compromised, and making the mistake of storing session data keyed only to a user id instead of a unique session.