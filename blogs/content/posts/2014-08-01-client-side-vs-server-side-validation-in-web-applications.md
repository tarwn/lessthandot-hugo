---
title: Client-side vs Server-side Validation in Web Applications
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-08-01T10:59:04+00:00
ID: 2847
url: /index.php/webdev/client-side-vs-server-side-validation-in-web-applications/
views:
  - 47310
rp4wp_auto_linked:
  - 1
categories:
  - Web Developer

---
Validation exists to &#8220;ensure that the application is robust against all forms of input data&#8221; ([owasp.org][1]). Invalid data can cause unexpected execution errors, break assumptions elsewhere in your application, introduce errors into reporting, and even let someone hijack your service to attack others ([script injection][2], sending spam, etc). 

When we&#8217;re talking about web form submission, the majority of invalid data tends to be users that missed a required field or mis-entered a strict value, like a phone number. So we have a high focus on helping the user understand and correct their input. The common question when we&#8217;re building new sites is whether we build our validation on the server-side, client-side, or both. If the deadline is tight enough, it may only be a question of client-side or server-side. 

So which do we choose?

## Client-Side

When we start out adding validation, we want to make it as easy as possible for the user to fix the problem with the least additional load on our servers. This leans towards doing the client-size validation first, then server-side later if we have time. Client-side validation is faster, typically looks prettier, often has better association between messages and inputs, and just generally looks like the better choice from the product owner or customer perspective.

Here&#8217;s a sample form using ASP.Net and Razor:

```html
```
We want to make the text input required, so with a little jQuery Validation, we now have:

```html
```
That was an easy sample to write and more realistic forms aren&#8217;t much more challenging. It stops the user from submitting invalid data and helps them correct it.

But when we look at how well it achieves the purpose, we find it has a lot of gaps:

  * Yes &#8211; It prevents bad values for users with good intent
  * Yes &#8211; It helps the good intent user correct their value without the overhead of a server round-trip
  * No &#8211; It prevents bad values when a script fails to load (like jQuery)
  * No &#8211; It prevents bad values as a result of malicious editing of the web form (developer tools)
  * No &#8211; It prevents bad values submitted directly to the endpoint (ex: [Cross-Site Request Forgery][3])
  * No &#8211; It prevents bad values when accessed in [frames][4]
  * No &#8211; It prevents bad values when data is altered via a [Man-in-the-middle attack][5]

When we&#8217;re working in authenticated areas, the risk for some of these is reduced, but reduced is not the same as robust.

## Server-Side

On the server, we can perform the same checks we did on the client to ensure the values are valid and we can add in additional checks for things like CSRF:

Client:

```html
```
Server:

```C#
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult SaveRecord(int recordId, string recordValue)
{
    var errors = new List<string>();
    if (!_backendLogic.IsRecordIdValid(recordId))
        errors.Add("The specified record id is not valid");
    if (string.IsNullOrWhiteSpace(recordValue))
        errors.Add("The record value is required");

    if (errors.Count == 0)
    {
        _backendLogic.Save(new RecordDTO(recordId, recordValue));
        return View("Index");
    }
    else
    {
        // return input values and errors so we can re-display everything
        var model = new SaveErrorViewModel(recordId, recordValue, errors);
        return View(model);
    }
}
```
So how does this stack up against the client-side method?

  * Yes &#8211; It prevents bad values for users with good intent
  * No &#8211; It helps the good intent user correct their value without the overhead of a server round-trip
  * Yes &#8211; It prevents bad values when a script fails to load (like jQuery)
  * Yes &#8211; It prevents bad values as a result of malicious editing of the web form (developer tools)
  * Yes &#8211; It prevents bad values submitted directly to the endpoint (ex: [Cross-Site Request Forgery][3])
  * Yes &#8211; It prevents bad values when accessed in [frames][4]
  * Yes &#8211; It prevents bad values when data is altered via a [Man-in-the-middle attack][5]

Note: Keep in mind some of these also require other corrective or protective actions (like framebusting to combat Cross Frame Scripting), I&#8217;m just focusing on the validation aspects.

## The Answer

The best answer is to use both. Server-side validation treats all incoming data as untrusted, it&#8217;s the gateway into the rest of the system. Client-side validation helps make the experience smooth for an end user and attempt to reduce some load from the server. With both in place, you hit the whole punch-list of options above.

If you have to sacrifice one, however, client-side validation should be the one to go. Client-side offers a better user experience and slightly less server load, but it&#8217;s at the expense of all of the security concerns that server-side validation addresses. looked at another way, Server-side validation prevents the type of issues that could put you out of business, client-side validation improves the experience.

## My Single Page Application Makes This Easier

I recently saw someone comment that building their site as a Single Page Application makes validation easier. All the necessary javascript files are available and running, the structure makes it harder to even figure out where your forms are (security through obscurity), and so on.

I disagree, I think validation for a SPA is actually a larger undertaking than the traditional client/server form submission model. 

A SPA trades individual pages and form posts for a much higher number raw API requests. This not only creates a much larger surface area for someone to mine or attempt to inject bad data through, it also provides a lower level of obscurity for systems that follow common patterns for their API design. In addition, authorization actually adds even more challenges, as you have to decide whether to give every user the whole application (and provide a lot of information to un-authorized users that can view source) or build more extensive server-side logic to validate which individual content and script files the user actually needs for their level of access. In addition to the increased server-side complexity and size, not doing extensive client-side validation is even harder to sell in a SPA. 

That adds up to higher server-side requirements, more complex authorization requirements, and a harder requirement to implement client-side validation. 

In either case, traditional server/client pages or a SPA, the best answer is both and the absolute minimum is server-side with client-side added primarily for usability.

 [1]: https://www.owasp.org/index.php/Data_Validation "Data Validation at owasp.org"
 [2]: https://www.owasp.org/index.php/Cross-site_Scripting_%28XSS%29
 [3]: https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29
 [4]: https://www.owasp.org/index.php/Cross_Frame_Scripting
 [5]: https://www.owasp.org/index.php/Man-in-the-middle_attack