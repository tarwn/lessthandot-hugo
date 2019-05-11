---
title: Combine Azure Storage Analytics Logs with Powershell
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-03-14T15:17:33+00:00
ID: 2515
url: /index.php/enterprisedev/cloud/azure/combine-azure-storage-analytics-logs-with-powershell/
views:
  - 10782
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - azure
  - powershell

---
When you have Storage Analytics transaction logging turned on, it produces transaction log files for each service call you make to blob, table, or queue service. Unfortunately it captures these in multiple files per hour, stored in a folder hierarchy by service (blob, queue, table), year, month, day, and hour. Trying to dig through these files or combine them into a single excel file can be time consuming and, unfortunately, is one of the first things you will be asked if you submit a storage-related support ticket. 

<div id="attachment_2518" style="width: 710px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/03/AzureManagementStudio_RawLogs.png"><img src="/wp-content/uploads/2014/03/AzureManagementStudio_RawLogs.png" alt="1 Hour of Raw Diagnostics Logs (in Azure Management Studio)" width="700" height="423" class="size-full wp-image-2518" srcset="/wp-content/uploads/2014/03/AzureManagementStudio_RawLogs.png 700w, /wp-content/uploads/2014/03/AzureManagementStudio_RawLogs-300x181.png 300w" sizes="(max-width: 700px) 100vw, 700px" /></a>
  
  <p class="wp-caption-text">
    1 Hour of Raw Diagnostics Logs (in Azure Management Studio)
  </p>
</div>

At some point, I created a powershell script to do the heavy lifting for me. The goal was to be able to start anywhere in the folder hierarchy and combine the files, converting them from semi-colon to comma-delimited as it went. This way I could download one or more folders from any level to a single folder, run the powershell script, and spend my time filtering and querying in the resulting CSV instead of digging through multiple files.

```powershell
param (
    [string]
    [Alias("i")]
    $InputFolder = ".",
    [string]
    [Alias("o")]
    $OutputFile = "CombinedFile.csv"
 )

$Headers = "Log Version"," Transaction Start Time"," REST Operation Type"," Request Status"," HTTP Status Code"," E2E Latency"," Server Latency"," Authentication type"," Requestor Account Name"," Owner Account Name"," Service Type"," Request URL"," Object Key"," Request ID"," Operation Number"," Client IP"," Request Version"," Request Header Size"," Request Packet Size"," Response Header Size"," Response Packet Size"," Request Content Length"," Request MD5"," Server MD5"," ETag"," Last Modified Time"," ConditionsUsed"," User Agent"," Referrer"," Client Request ID"

($Headers -Join ",") | Set-Content $OutputFile

dir -recurse $InputFolder -Include "*.log" | %{ Import-Csv $_.FullName -Delimiter ";" -Header $Headers | ConvertTo-Csv -Delimiter "," -NoTypeInformation | select -skip 1 | Add-Content $OutputFile }
```
Running it from a powershell prompt is easy. I downloaded a different hour's worth of blob transactions to a folder named "blob\_2014\_03_11" and then ran:

`.\CombineLogFiles.ps1  "blob_2014_03_11\1200" combinedfiles.csv` 

Quickly combining a sample download of 7000 transactions from multiple files into a single 3.4MB CSV almost instantaneously.