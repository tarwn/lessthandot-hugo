---
title: Using Azure Functions to add a Contact Form to a Static Site
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-01-27T15:28:45+00:00
url: /index.php/enterprisedev/cloud/azure/using-azure-functions-to-add-a-contact-form-to-a-static-site/
views:
  - 13766
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - Azure Functions

---
My personal website is a static site: 100% HTML, JS, and CSS files with no server-side processing. I have custom code that pulls data from a variety of sources and builds updated versions of the files from templates, which are then deployed to the host. I do this to move the CPU latency of building the pages to my time, instead of charging it to visitors on each page hit. While I have a host, a strategy like this means I could also choose to host for free via github or similar services.

So there&#8217;s a great benefit to the reader and our wallet, but no server-side execution makes things like contact forms trickier. Luckily, Azure Functions or AWS Lambda can be used as a webhook to receive the form post and process it, costing nothing near nothing to use (AWS and Azure both offer a free tier for 1M requests/month and 400,000 GB-seconds of compute time).

So we swap out a hosted server at $x/month for free static page hosting and free contact form (and similar services), it just takes a little different type of work then building the standard PHP, ASP.Net, etc site.

## Creating the Azure Function

First up, let&#8217;s build out an Azure Function to accept a form post and convert it to an email.

### Setting up the function

From the Azure Dashboard, create a new &#8220;Azure Function App&#8221;.

<div id="attachment_4981" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_0.png"><img src="/wp-content/uploads/2017/01/Azure_0.png" alt="Azure Function App - Getting Started" width="800" height="467" class="size-full wp-image-4981" srcset="/wp-content/uploads/2017/01/Azure_0.png 800w, /wp-content/uploads/2017/01/Azure_0-300x175.png 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Azure Function App &#8211; Getting Started
  </p>
</div>

The Azure Portal offers us a few options to get started quickly. Pick the one on the right &#8220;Webhook + API&#8221; to get a function set up with the trigger and output we need out of the box.

The trigger for the Azure Function is the Webhook endpoint:

<div id="attachment_4998" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_1.png"><img src="/wp-content/uploads/2017/01/Azure_1.png" alt="Azure Function - Webhook Trigger" width="800" height="184" class="size-full wp-image-4998" srcset="/wp-content/uploads/2017/01/Azure_1.png 800w, /wp-content/uploads/2017/01/Azure_1-300x69.png 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Azure Function &#8211; Webhook Trigger
  </p>
</div>

We can customize this to listen only to the /contact route and POST messages:

<div id="attachment_4983" style="width: 751px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_2.png"><img src="/wp-content/uploads/2017/01/Azure_2.png" alt="Azure Function - Webhook Trigger Details" width="741" height="407" class="size-full wp-image-4983" srcset="/wp-content/uploads/2017/01/Azure_2.png 741w, /wp-content/uploads/2017/01/Azure_2-300x164.png 300w" sizes="(max-width: 741px) 100vw, 741px" /></a>
  
  <p class="wp-caption-text">
    Azure Function &#8211; Webhook Trigger Details
  </p>
</div>

There are other details we could configure, so as you do this you might start getting all kinds of other ideas (remember, 1M requests/month and 400,000 GB-seconds of compute time for FREE!).

We don&#8217;t have an input for this Azure Function, and the return value is simply the HTTP Response we&#8217;ll return form the trigger, so no further configuration to do:

<div id="attachment_4984" style="width: 401px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_3.png"><img src="/wp-content/uploads/2017/01/Azure_3.png" alt="Azure Function - Return Value" width="391" height="278" class="size-full wp-image-4984" srcset="/wp-content/uploads/2017/01/Azure_3.png 391w, /wp-content/uploads/2017/01/Azure_3-300x213.png 300w" sizes="(max-width: 391px) 100vw, 391px" /></a>
  
  <p class="wp-caption-text">
    Azure Function &#8211; Return Value
  </p>
</div>

We now have the &#8220;hello world&#8221; version of a webhook, let&#8217;s add more code.

### Coding the Contact Email and Response

The function starts with some generated code that matches the variable names in the Trigger and Output (I picked C#, JavaScript is also an option), attempts to pull a value out of the querystring, and returns a &#8220;Hello&#8221; response:

<pre>using System.Net;

public static async Task&lt;HttpResponseMessage&gt; Run(HttpRequestMessage req, TraceWriter log)
{
    // ... sample code we don't need that pulls name from querystring ...

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}</pre>

Before we start in on the email code, let&#8217;s start by not putting the secrets right in the code. Functions have a built in method to manage secrets, but they&#8217;re not terribly easy to find. Click the &#8220;Function app settings&#8221; menu option at the bottom left to get to this screen:
  


<div id="attachment_4985" style="width: 420px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_4.png"><img src="/wp-content/uploads/2017/01/Azure_4.png" alt="Finding the App Settings, Step 1" width="410" height="249" class="size-full wp-image-4985" srcset="/wp-content/uploads/2017/01/Azure_4.png 410w, /wp-content/uploads/2017/01/Azure_4-300x182.png 300w" sizes="(max-width: 410px) 100vw, 410px" /></a>
  
  <p class="wp-caption-text">
    Finding the App Settings, Step 1
  </p>
</div>

Then click the &#8220;App Settings&#8221; button:

<div id="attachment_4986" style="width: 420px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_5.png"><img src="/wp-content/uploads/2017/01/Azure_5.png" alt="Finding the App Settings, Step 2" width="410" height="249" class="size-full wp-image-4986" srcset="/wp-content/uploads/2017/01/Azure_5.png 410w, /wp-content/uploads/2017/01/Azure_5-300x182.png 300w" sizes="(max-width: 410px) 100vw, 410px" /></a>
  
  <p class="wp-caption-text">
    Finding the App Settings, Step 2
  </p>
</div>

This will open another blade to the right. One of the sections is the &#8220;App settings&#8221; section. You can enter AppSettings key/value configurations here that will be available to your function code. In my case, I&#8217;m going to add in all of my SMTP settings so I don&#8217;t have them stored in the code when I later hook this to a git repository.

<div id="attachment_4987" style="width: 378px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_6.png"><img src="/wp-content/uploads/2017/01/Azure_6.png" alt="Adding SMTP AppSettings" width="368" height="459" class="size-full wp-image-4987" srcset="/wp-content/uploads/2017/01/Azure_6.png 368w, /wp-content/uploads/2017/01/Azure_6-240x300.png 240w" sizes="(max-width: 368px) 100vw, 368px" /></a>
  
  <p class="wp-caption-text">
    Adding SMTP AppSettings
  </p>
</div>

Now we can add some basic validation and some fairly standard &#8220;send an email&#8221; code. I&#8217;m accessing the stored secrets above via ConfigurationManager.AppSettings, as I would if you were writing a .Net application with an app.config. 

<pre>using System.Configuration;
using System.Net;
using System.Net.Mail;
using System.Threading.Tasks;

public static async Task&lt;HttpResponseMessage&gt; Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // 1: Get request body + validate required content is available
    var postData = await req.Content.ReadAsFormDataAsync();
    var missingFields = new List&lt;string&gt;();
    if(postData["fromEmail"] == null)
        missingFields.Add("fromEmail");
    if(postData["message"] == null)
        missingFields.Add("message");
    
    if(missingFields.Any())
    {
        var missingFieldsSummary = String.Join(", ", missingFields);
        return req.CreateResponse(HttpStatusCode.BadRequest, $"Missing field(s): {missingFieldsSummary}");
    }

    // 2: Site settings
    var smtpHost = ConfigurationManager.AppSettings["smtpHost"];
    var smtpPort = Convert.ToInt32(ConfigurationManager.AppSettings["smtpPort"]);
    var smtpEnableSsl = Boolean.Parse(ConfigurationManager.AppSettings["smtpEnableSsl"]);
    var smtpUser = ConfigurationManager.AppSettings["smtpUser"];
    var smtpPass = ConfigurationManager.AppSettings["smtpPass"];
    var toEmail = ConfigurationManager.AppSettings["toEmail"];

    // 3: Build + Send the email
    MailMessage mailObj = new MailMessage(postData["fromEmail"], toEmail, "Site Contact Form", postData["message"]);
    SmtpClient client = new SmtpClient();
    client.Host = smtpHost;
    client.Port = smtpPort;
    client.EnableSsl = smtpEnableSsl;
    client.DeliveryMethod = SmtpDeliveryMethod.Network;
    client.UseDefaultCredentials = false;
    client.Credentials = new System.Net.NetworkCredential(smtpUser, smtpPass);

    try
    {
        client.Send(mailObj);
        return req.CreateResponse(HttpStatusCode.OK, "Thanks!");
    }
    catch (Exception ex)
    {
        return req.CreateResponse(HttpStatusCode.InternalServerError, new {
            status = false,
            message = $"Email has not been sent: {ex.GetType()}"            
        });
    }
}</pre>

With the code in place, we can use a tool like Postman to fire off some test POSTs and verify all the pieces are connected.

Don&#8217;t grab the URL above your code screen yet, it probably has an Administrative key coded into it. Open the &#8220;Keys&#8221; panel from the button (#1 below) in the top right and select the &#8220;default&#8221; key in the &#8220;Function Keys&#8221; list. When you do this, it will update the Function Url (#3) above the code panel to include this key instead of one of the Admin keys. As a final step, click the &#8220;Logs&#8221; (#2) button to open the log so you can see compile and run logs when it builds or is triggered. Now copy the Function URL (#3) so we can paste it into Postman to start testing.

<div id="attachment_4999" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_7.png"><img src="/wp-content/uploads/2017/01/Azure_7.png" alt="Azure Functions - Key, Logs, and URL" width="800" height="131" class="size-full wp-image-4999" srcset="/wp-content/uploads/2017/01/Azure_7.png 800w, /wp-content/uploads/2017/01/Azure_7-300x49.png 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Azure Functions &#8211; Key, Logs, and URL
  </p>
</div>

In a new Postman request, enter the URL at the top, select &#8220;Post&#8221; as the method, and add in key/value pairs for the fromEmail and the message. Of course we expect this to fail the first time:

<div id="attachment_5000" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_8.png"><img src="/wp-content/uploads/2017/01/Azure_8.png" alt="Azure Function - First Call Failed" width="800" height="385" class="size-full wp-image-5000" srcset="/wp-content/uploads/2017/01/Azure_8.png 800w, /wp-content/uploads/2017/01/Azure_8-300x144.png 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Azure Function &#8211; First Call Failed
  </p>
</div>

Fixing the code then nets us:

<div id="attachment_5001" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_9.png"><img src="/wp-content/uploads/2017/01/Azure_9.png" alt="Azure Function - Second Call Failed" width="800" height="326" class="size-full wp-image-5001" srcset="/wp-content/uploads/2017/01/Azure_9.png 800w, /wp-content/uploads/2017/01/Azure_9-300x122.png 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Azure Function &#8211; Second Call Failed
  </p>
</div>

Because my code is expecting urlencoded form data and wasn&#8217;t able to parse any from the body. Once we switch that, we get:

<div id="attachment_5002" style="width: 810px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_10.png"><img src="/wp-content/uploads/2017/01/Azure_10.png" alt="Azure Function - Success" width="800" height="290" class="size-full wp-image-5002" srcset="/wp-content/uploads/2017/01/Azure_10.png 800w, /wp-content/uploads/2017/01/Azure_10-300x108.png 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Azure Function &#8211; Success
  </p>
</div>

Success!

### Building the HTML form

Now we just need to switch from Postman to using an HTML form. Here&#8217;s a quick sample:

<pre>&lt;h1&gt;Contact Form&lt;/h1&gt;
Send me a message! (Congratulations for finding this, it's not an official part of the site!)
&lt;div id="contactForm"&gt;
    Your Email: &lt;input type="text" name="fromEmail" /&gt;&lt;br /&gt;
    Message: &lt;br /&gt;
    &lt;textarea cols="60" rows="4" name="message"&gt;&lt;/textarea&gt;&lt;br /&gt;
    &lt;input type="submit" value="Send!" /&gt;
&lt;/div&gt;

&lt;script src="https://code.jquery.com/jquery-3.1.1.min.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript"&gt;
    var url = "https://eli-contactform.azurewebsites.net/api/contact?code=1pabaq6cdy2tt3f43t4uuqsemi8429ygl2n4ca6m9utugoz2gldiw15i5t61ew3pzzb7n60mb1emi";
    $("form").on('submit', function (event) {
        event.preventDefault();

        // grab contact form data
        var data = $(this).serialize();

        // hide prior errors, disable inputs while we're submitting
        $("#contactFormError").hide();
        $("#contactForm input").prop('disabled', true);

        // back in my day, we had to AJAX uphill both ways, in the snow, through cross-iframe scripts
        $.ajax({
            type: "POST",
            url: url,
            data: data,
            dataType: "text",
            headers: {'Content-Type': 'application/x-www-form-urlencoded'},
            success: function (respData) {
                // Yay, success!!
                $("#contactForm").html("&lt;div style='padding: 5em 1em; text-align: center; color: #008800'&gt;" + respData + "&lt;/div&gt;");
            },
            error: function (jqXHR) {
                // Boo, error...
                $("#contactFormError").html("&lt;div style='padding: 1em; text-align: center; color: #660000'&gt;Sorry, an error occurred: " + jqXHR.responseText + "&lt;/div&gt;");
                $("#contactFormError").show();
                $("#contactForm input").prop('disabled', false);
            }
        });
    });
&lt;/script&gt;</pre>

I use jQuery to post the form content because the Azure Function isn&#8217;t going to return a pretty HTML page. This way I can capture the output and use jQuery to tel the user whether it was successful or not.

There was one more catch the first time I tried this. Because I&#8217;m posting from my a page from my personal page to a domain in Azure, the calls initially fails with a Cross-Origin error. To enable Cross-Origin calls from your domain, go back to the Azure Functions interface in the App Settings section and open the CORS page:

<div id="attachment_4992" style="width: 420px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_11.png"><img src="/wp-content/uploads/2017/01/Azure_11.png" alt="Azure Function - CORS Config" width="410" height="249" class="size-full wp-image-4992" srcset="/wp-content/uploads/2017/01/Azure_11.png 410w, /wp-content/uploads/2017/01/Azure_11-300x182.png 300w" sizes="(max-width: 410px) 100vw, 410px" /></a>
  
  <p class="wp-caption-text">
    Azure Function &#8211; CORS Config
  </p>
</div>

This will open the list of domains allowed to make Cross-Origin calls (which will result in the necessary Cross-Origin-Access header being sent back so your browser will trust the content). Add your domain, save, and your jQuery calls should now work just fine.

<div id="attachment_5003" style="width: 660px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_14.png"><img src="/wp-content/uploads/2017/01/Azure_14.png" alt="Contact Form" width="650" height="269" class="size-full wp-image-5003" srcset="/wp-content/uploads/2017/01/Azure_14.png 650w, /wp-content/uploads/2017/01/Azure_14-300x124.png 300w" sizes="(max-width: 650px) 100vw, 650px" /></a>
  
  <p class="wp-caption-text">
    Contact Form
  </p>
</div>

<div id="attachment_5004" style="width: 660px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2017/01/Azure_13.png"><img src="/wp-content/uploads/2017/01/Azure_13.png" alt="Contact Form Success!!" width="650" height="236" class="size-full wp-image-5004" srcset="/wp-content/uploads/2017/01/Azure_13.png 650w, /wp-content/uploads/2017/01/Azure_13-300x108.png 300w" sizes="(max-width: 650px) 100vw, 650px" /></a>
  
  <p class="wp-caption-text">
    Contact Form Success!!
  </p>
</div>

And there we go, successful contact emails on the static page.

## Final Steps

Besides making a nicer user experience than the one I hacked together there, one last step you should also take is to enter in a value for the maximum daily usage quota just to ensure no one finds your form and tries to DOS your credit card. 

So there we go, a contact form for a static website that should run absolutely free. This is easily extended to other features when you take into account that you could also be dropping messages in a queue, saving to blobs, etc and then using an AJAX GET call to a webhook like this to get that stored content (basically a free, trigger-based API service). There&#8217;s all kinds of options you can fit inside the free level of these services that frees you from having to pay for a full web host.