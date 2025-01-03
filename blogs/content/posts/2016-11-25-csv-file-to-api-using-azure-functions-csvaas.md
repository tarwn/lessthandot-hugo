---
title: CSV file to API using Azure Functions (CSVaaS)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-11-25T14:18:56+00:00
ID: 4839
url: /index.php/enterprisedev/cloud/azure/csv-file-to-api-using-azure-functions-csvaas/
featured_image: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/API.png
views:
  - 8857
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - Azure APIM
  - Azure Functions

---
We're living in the future. During a conversational aside the other day, the CEO recounted a story of someone he met that was willing to throw money at a product to make it easy to save an excel file and have it surface as an API. A few years ago that was server provisioning and a couple days to a couple weeks of work, depending on the level of analytics, authentication, identity management, documentation, data entry system, and so on you wanted. With the explosion of tools and services we're seeing in the cloud, now we can do this in an a few hours or less, with 200 lines of code and no servers.

## To Launch a managed API in less than 200 lines of code

When I went down this path, I decided it had to be a realistic. The API side had to have authentication, analytics, rate limiting, documentation, subscriptions, and endpoints that reflect the latest dataset ("/latest"), the list of all files received ("/all"), and access to a specific dataset/CSV ("/archive/{name}"). The CSV side of things had to be incredibly simple, no new interfaces, upload tools, or anything. I want to save a file in a local folder and have the data available via an API on the internet.

<div id="attachment_4840" style="width: 810px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS.png"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS.png" alt="Transformation Pipeline - Saving a CSV in a folder to Managed API" width="960" height="235" class="size-full wp-image-4840" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS.png 960w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS-300x73.png 300w" sizes="(max-width: 960px) 100vw, 960px" /></a>
  
  <p class="wp-caption-text">
    Transformation Pipeline – Saving a CSV in a folder to Managed API
  </p>
</div>

I drop a file in my Dropbox folder, it's picked up and processed into a similarly named JSON blob, a "latest" JSON blob, and an entry in the "list" of archived entries, all in Azure Storage from the original CSV file within 15 seconds or less. API Management then serves as a gateway to give me rate-limited, documented, etc, etc access to those pre-generated JSON responses.

### Tools and Services

Here's the tools I used:

  * Azure API Management – provides an API gateway with built-in authentication mechanisms, analytics, customizable policies, caching, and documentation with "Try It" interfaces
  * Azure Storage – provides infinite file storage where I can drop translated JSON files to serve as the back-end behind the API gateway
  * Azure Functions 
      * #1: monitors dropbox and scoops up changes to convert them from CSV into JSON, saving to an "archive" container in Azure Storage
      * #2: monitors the "archive" container and publishes new entries by copying them to "public/latest" and adding them to a list in "listing/all"
  * Microsoft Account – identity provider for API subscription
  * Dropbox – my end-user "UI"

The only one of these that requires coding is the Azure Functions, and I did those in C#.

### $50/month for fancy features, $0.03/month without

1GB of data / month of blob storage is about $0.03, 10GB would be around $0.20 or less. Processing 30 files/month via Azure Functions is free (at 1 second runs, 250K files would be $1.60/month), so less than $0.40/month if you could manage to copy 140 CSV files/hour for a month into dropbox (lots of coffee?). API Management is the expensive part (for this project), at $50/month for Developer level (but using it for a single API call is massive overkill, I would expect it to be serving up a lot more than that).

API Management offers a lot of extra capabilities, but if I didn't want them I could just as easily publish the functions and blob endpoints as the API, at roughly $0.03/month.

## The File Transform Process

So this CSV file: <https://www.dropbox.com/s/3wqj4jpv4vru2a7/file1?dl=0>

```text
MyString,MyNumber,MyBool,MyAlmostNumber,MyAlmostBool
"ABC",123,true,456,false
"DEF",124,false,555,true
"GHI",125,True,whatever,more stuff
```
Turns into this JSON file: <https://csvaas.blob.core.windows.net/archive/file1>

```javascript
{  
   "info":{  
      "sourceFile":"file1",
      "processedTime":"2016-11-24T14:20:19",
      "types":[  "String", "Number", "Boolean", "String", "String" ]
   },
   "rows":[  
      {  "MyString":"ABC", "MyNumber":123, "MyBool":true, "MyAlmostNumber":"456", "MyAlmostBool":"false" },
      {  "MyString":"DEF", "MyNumber":124, "MyBool":false, "MyAlmostNumber":"555", "MyAlmostBool":"true" },
      {  "MyString":"GHI", "MyNumber":125, "MyBool":true, "MyAlmostNumber":"whatever", "MyAlmostBool":"more stuff" }
   ]
}
```
Which is then published to the listing: <https://csvaas.blob.core.windows.net/listing/all>

```javascript
{
    "LatestUpdate":"2016-11-24T14:29:21.1069912Z",
    "Items":["file2","file1"]
}
```
And is surfaced as the latest record (which has probably changed by now): <https://csvaas.blob.core.windows.net/public/latest>

And these are the prepared responses for this API: [CSVaaS Documentation][1]

<div id="attachment_4848" style="width: 810px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_Doc_1.jpg"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_Doc_1.jpg" alt="Automatic Documentation, Code Samples, etc" width="800" height="391" class="size-full wp-image-4848" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_Doc_1.jpg 800w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_Doc_1-300x146.jpg 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Automatic Documentation, Code Samples, etc
  </p>
</div>

Including example code in 7 languages and cUrl.

And, with a subscription id, you can run these directly in the page: <https://csvaas.azure-api.net/latest>

<div id="attachment_4849" style="width: 810px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_Doc_2.jpg"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_Doc_2.jpg" alt="API Sample Run" width="800" height="564" class="size-full wp-image-4849" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_Doc_2.jpg 800w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_Doc_2-300x211.jpg 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    API Sample Run
  </p>
</div>

Behind the scenes, I can configure requirements around the subscriptions and approval process:

<div id="attachment_4875" style="width: 659px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS_Settings.png"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS_Settings.png" alt="Setting the service to require Subscriptions  and Administrator Approval" width="649" height="387" class="size-full wp-image-4875" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS_Settings.png 649w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS_Settings-300x178.png 300w" sizes="(max-width: 649px) 100vw, 649px" /></a>
  
  <p class="wp-caption-text">
    Setting the service to require Subscriptions and Administrator Approval
  </p>
</div>

I have access to high-level monitoring:

<div id="attachment_4873" style="width: 692px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS1.png"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS1.png" alt="Usage Analytics" width="682" height="214" class="size-full wp-image-4873" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS1.png 682w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS1-300x94.png 300w" sizes="(max-width: 682px) 100vw, 682px" /></a>
  
  <p class="wp-caption-text">
    Usage Analytics
  </p>
</div>

And dive into details like response times around the world for specific date ranges and operations:
  


<div id="attachment_4878" style="width: 810px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS_Analytics.jpg"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS_Analytics.jpg" alt="Response Analytics for Geography by Date Rage, Operation, ..." width="800" height="319" class="size-full wp-image-4878" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS_Analytics.jpg 800w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/CSVaaS_Analytics-300x119.jpg 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Response Analytics for Geography by Date Rage, Operation, ...
  </p>
</div>

So I have pretty documentation, examples in numerous languages, try it right in the interface buttons, subscription management, and so on with no effort beyond defining the operations (and I could customize all of this if I felt like spending the time).

## Building It – Implementation Details

Here's the details of what it took to build it.

### Step 1: Storage

Add a new Storage Account with a catchy name:[About Azure Storage Accounts][2]

Open the containers list and add an archive container to hold the JSON files and container(s) for the "latest" and "listing" blobs. We can use re-write rules in the API Management operations to flatten or translate these paths later.

### Step 2: API Management

Add an API service via the dashboard. Configure it with a name and Web Service URL:

<div id="attachment_4842" style="width: 810px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_1.jpg"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_1.jpg" alt="Defining an Azure API" width="800" height="489" class="size-full wp-image-4842" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_1.jpg 800w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_1-300x183.jpg 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Defining an Azure API
  </p>
</div>

To add Authentication, head to the Identities tab. I used a Microsoft Account for simplicity, but other options like Azure AD, Twitter, Facebook, etc are available out of the box (or you can choose to use certificates or BASIC auth at the API level).

Instructions to setup App Service, go through similar steps for the Identity tab: MS Auth: https://docs.microsoft.com/en-us/azure/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication

<div id="attachment_4843" style="width: 810px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_2.jpg"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_2.jpg" alt="Defining API Identity Providers" width="800" height="497" class="size-full wp-image-4843" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_2.jpg 800w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_2-300x186.jpg 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Defining API Identity Providers
  </p>
</div>

There is a Starter Product and an Unlimited Product already predefined. Use the Starter Product for now, since it has rate limits and such already defined, and associate the API with the Product.

<div id="attachment_4844" style="width: 810px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_3.jpg"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_3.jpg" alt="Defining API Product" width="800" height="253" class="size-full wp-image-4844" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_3.jpg 800w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_3-300x94.jpg 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Defining API Product
  </p>
</div>

Now we need an API endpoint. Add an Operation, I chose to name my "latest". It will concatenate this value automatically on the base URL provided in the first setup screen (or you can choose to rewrite to a different URL). I'm going with the simple option of matching my Blob name to the path.

<div id="attachment_4845" style="width: 810px" class="wp-caption alignnone">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_4.jpg"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_4.jpg" alt="Defining an API Operation" width="800" height="419" class="size-full wp-image-4845" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_4.jpg 800w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2016/11/APIM_4-300x157.jpg 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
  
  <p class="wp-caption-text">
    Defining an API Operation
  </p>
</div>

And we have an API.

### Step 3: Azure Function

Provision an Azure Functions item from the dashboard, set it to the same resource group as the prior items and the storage account from #1, for simplicity. 

#### Step 3A: CSV to JSON

Create a new function with a trigger of type "External File Trigger". I chose Dropbox as my input and blob storage as my output, using the same "{name}" token in both names so that my incoming CSV file will generate a matching named JSON file in my output.

Lastly, write some code to convert the CSV to JSON. I used a nuget package, so I added a project.json to specify my dependencies:

```json
```
And then a simple Run function to convert the incoming input file into the projected JSON for blob storage:

**[CSVaaSDropboxProcessing/run.csx][3]**

```csharp
#r "Microsoft.WindowsAzure.Storage"

using System;
using System.Text;
using System.Text.RegularExpressions;
using CsvHelper;
using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(Stream input, string name, CloudBlockBlob jsonFile, TraceWriter log)
{
    log.Info($"C# External trigger function processed file: " + name);

    var boolRegex = new Regex(@"^([Tt]rue|[Ff]alse)$");
    var numericRegex = new Regex(@"^[\d\.,]*\d[\d\.,]*$");

    var reader = new StreamReader(input);
    var csv = new CsvReader(reader);
    csv.Configuration.HasHeaderRecord = false;

    var headers = new List<string>();
    var rows = new List<List<string>>();
    InspectedType[] types = null;
    int rowCount = 0;
    string stringValue;
    while(csv.Read())
    {
        if(rowCount == 0)
        {
            for(int i=0; csv.TryGetField<string>(i, out stringValue); i++) 
            {
                headers.Add(stringValue);
            }
            types = new InspectedType[headers.Count()];
            log.Info($"Headers: " + String.Join(",", headers));            
        }
        else
        {
            var row = new List<string>();
            for(int i=0; csv.TryGetField<string>(i, out stringValue); i++) 
            {
                row.Add(stringValue);
                if(rowCount == 1)
                {
                    if(boolRegex.IsMatch(stringValue))
                    {
                        types[i] = InspectedType.Boolean;
                    }
                    else if(numericRegex.IsMatch(stringValue))
                    {
                        types[i] = InspectedType.Number;
                    }
                    else
                    {
                        types[i] = InspectedType.String;
                    }
                }
                else
                {
                    if(types[i] == InspectedType.Boolean && !boolRegex.IsMatch(stringValue))
                    {
                        types[i] = InspectedType.String;
                    }
                    else if(types[i] == InspectedType.Number && !numericRegex.IsMatch(stringValue))
                    {
                        types[i] = InspectedType.String;
                    }
                }
            }
            rows.Add(row);
            log.Info($"Row $rowCount: " + String.Join(",", row));
        }
        rowCount++;
    }

    log.Info("Inspected Row Types: " + String.Join(",", types));

    var time = DateTime.UtcNow.ToString("s");
    var typesDescription = "\"" + String.Join("\",\"", types) + "\"";
    var sb = new StringBuilder();
    sb.AppendLine("{");
    sb.AppendFormat("\"info\": {{ \"sourceFile\": \"{0}\", \"processedTime\": \"{1}\", \"types\": [{2}] }},",
                    name, time, typesDescription);

    sb.AppendLine("\"rows\": [");
    bool isFirstRow = true;
    int columnCount = headers.Count();
    foreach(var row in rows)
    {
        if(!isFirstRow)
        {
            sb.AppendLine(",");
        }
        isFirstRow = false;

        for(var i = 0; i < columnCount; i++)
        {
            var value = row[i];
            if(types[i] == InspectedType.Boolean)
            {
                value = row[i].ToLower();
            }
            else if(types[i] == InspectedType.Number)
            {
                value = row[i].Replace(",", "");
            }
            else
            {
                value = "\"" + row[i].Replace("\"","\\\"") + "\"";
            }

            sb.AppendFormat("{0}\"{1}\": {2}{3}",
                            i == 0 ? "{" : ", ", 
                            headers[i], 
                            value,
                            i == columnCount - 1 ? "}" : "");
        }
    }
    sb.AppendLine("]");
    sb.AppendLine("}");

    jsonFile.UploadText(sb.ToString());
    jsonFile.Properties.ContentType = "application/json";
    jsonFile.SetProperties();
}

public enum InspectedType
{
    String = 0,
    Number = 1,
    Boolean = 2    
}
```
Nearly all of the code is converting the CSV to JSON, almost no code is required to handle the storage, dropbox, etc interactions.

#### Step 3B: Listing and Latest JSON

Create a second Azure Function that watches the "Archive" blob, with a blob output with the "latest" name hardcoded, and input for the "listing" blob, and an "inout" blob for the listing. We reference the input twice because it lets us bind the input as a string for easy null checks, but a CloudBlockBlob for actual output so we can set the output blob properties to make it "application/json".

**[CSVaaSPublish/run.csx][4]**

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;
using Newtonsoft.Json;

public static void Run(Stream triggerBlob, string name, string listBlobIn, CloudBlockBlob listBlobOut, Stream outputBlob, TraceWriter log)
{
    // publish "latest"
    triggerBlob.CopyTo(outputBlob);

    // add original item to archive "all" list
    CSVList list;
    if(!String.IsNullOrEmpty(listBlobIn))
    {
        list = JsonConvert.DeserializeObject<CSVList>(listBlobIn);
    }
    else{
        list = new CSVList();
    }

    list.LatestUpdate = DateTime.UtcNow;
    list.Items.Add(name);

    listBlobOut.UploadText(JsonConvert.SerializeObject(list));
    listBlobOut.Properties.ContentType = "application/json";
    listBlobOut.SetProperties();
}

public class CSVList
{
    public CSVList() 
    {
        Items = new List<string>();
    }

    public DateTime LatestUpdate { get;set; }
    public List<string> Items { get;set; }    
}
```
And there we have it, a CSV-powered API with full documentation, authentication via Microsoft as a single-sign on source, analytics, rate limits, and an interface that requires me to do nothing more than save my file in a folder and pay an extremely low consumption bill based on usage.

## Things I Figured Out

Along the way I ran into a couple undocumented Functions issues, here's the details in case someone else does to.

**CloudBlockBlob: Cannot bind blob to CloudBlockBlob using access Write**
  
The Issue:
  
Part of the list of [available bindings]() will complain if you set them as direction "in" or "out" and fail to work.

The Fix:
  
Edit the function.json file directly, changing the "direction" property to "inout", an undocumented value that indicates that it is a bi-directional binding type.

**CloudBlockBlob: Exception binding parameter 'output'**
  
The Issue:
  
This happened on my Dropbox->Blob Function. When you create an ExternalFileTrigger, it also asks you to define an output. This is not a "blob" output, but a special ApiHub thing that doesn't have the same capabilities as a Blob output and can't handle the same range of bindings that the "blob" type can.

To Fix it:
  
Edit the function.json file, changing the type to "blob" and pasting in a connection property that has been set up for a "blob" type (the ApiHub storage connection string won't work here either). Once you update both of these properties, everything is happy.

 [1]: https://csvaas.portal.azure-api.net/docs/services/5833743025491306b41695ff/operations/5833780425491306b4169603
 [2]: https://docs.microsoft.com/en-us/azure/storage/storage-create-storage-account
 [3]: https://github.com/tarwn/csvaas/blob/master/CSVaaSDropboxProcessing/run.csx
 [4]: https://github.com/tarwn/csvaas/blob/master/CSVaaSPublish/run.csx