---
title: Getting chocolatey or other powershell scripts to work when behind a proxy
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-27T07:30:00+00:00
ID: 1278
excerpt: |
  Introduction
  
  So I played with chocolatey this weekend and found it intriguing. It is an easy way to install things and update things. Things like nodejs that are in a constant flux for the moment. And chocolatey worked great at home. Alas, at work it&hellip;
url: /index.php/desktopdev/mstech/getting-chocolatey-to-work-when/
views:
  - 12652
categories:
  - Microsoft Technologies
tags:
  - chocolatey
  - nuget
  - powershell
  - proxy

---
## Introduction

So I [played with chocolatey this weekend][1] and found it intriguing. It is an easy way to install things and update things. Things like nodejs that are in a constant flux for the moment. And chocolatey worked great at home. Alas, at work it did not work. Because we are behind a giant PITA of a proxy. Ours is a squid proxy and thus not NTLM. So I set about changing chocolatey to make it work. Here are the changes I had to do.
  
Many kudos to [Rob Reynolds][2] BTW 

## The installer

First I tackled the powershell installer script Rob wrote. 

You can see the [original version on the wiki][3].

And here is my version.

```powershell
# variables
$url = "http://packages.nuget.org/v1/Package/Download/Chocolatey/0.9.8.4"
$chocTempDir = Join-Path $env:TEMP "chocolatey"
$tempDir = Join-Path $chocTempDir "chocInstall"
if (![System.IO.Directory]::Exists($tempDir)) {[System.IO.Directory]::CreateDirectory($tempDir)}
$file = Join-Path $tempDir "chocolatey.zip"

# download the package
Write-Host "Downloading $url to $file"
$downloader = new-object System.Net.WebClient
if (!$downloader.Proxy.IsBypassed($url))
{
    $cred = get-credential
    $webclient = new-object System.Net.WebClient
    $proxyaddress = $webclient.Proxy.GetProxy($url).Authority
    Write-host "Using this proxyserver: " $proxyaddress
    $proxy = New-Object System.Net.WebProxy($proxyaddress)
    $proxy.credentials = $cred.GetNetworkCredential();
    $downloader.proxy = $proxy
}
$downloader.DownloadFile($url, $file)

# unzip the package
Write-Host "Extracting $file to $destination..."
$shellApplication = new-object -com shell.application
$zipPackage = $shellApplication.NameSpace($file)
$destinationFolder = $shellApplication.NameSpace($tempDir)
$destinationFolder.CopyHere($zipPackage.Items(),0x10)

# call chocolatey install
Write-Host "Installing chocolatey on this machine"
$toolsFolder = Join-Path $tempDir "tools"
$chocInstallPS1 = Join-Path $toolsFolder "chocolateyInstall.ps1"

& $chocInstallPS1

# update chocolatey to the latest version
Write-Host "Updating chocolatey to the latest version"
cup chocolatey
Write-Host "All done"```
You will notice the proxy addition to that script at the download the package level.

<span class="MT_red">But I noticed that it stopped working on the line cup chocolatey</span>. It just froze. I had no idea why until I noticed that the nuget session was open at that time. When I ran cup chocolatey from the command prompt I got the same behavior. Then in a moment of insanity I just type my proxy username and password and kablam I got in. Apparently the script just stood there waiting for me to do that. That is because Rob had redirected the standardoutput to a logfile. 

like this.

```powershell
Start-Process $nugetExe -ArgumentList $packageArgs -NoNewWindow -Wait -RedirectStandardOutput $logfile -RedirectStandardError $errorLogFile
```
You can find this in the chocolatey install folder in the chocolatey.ps1 file.

I removed the -RedirectStandardOutput where I could find it. and that made the cup chocolatey work. I now see this instead of a simple blinking cursor.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey3.png?mtime=1311758120"><img alt="" src="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey3.png?mtime=1311758120" width="677" height="392" /></a>
</div>

So that is the installer solved.

## Installing chocolatey packages

However trying to install a package like nodejs would still fail. Like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey4.png?mtime=1311758468"><img alt="" src="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey4.png?mtime=1311758468" width="677" height="392" /></a>
</div>

But we can soon fix this by going to the chocolateyInstaller.psm1 in the chocolateyInstall.helpers folder.

I just needed to add the proxy logic to the Get-WebFile function. Like this.

```powershell
$req = [System.Net.HttpWebRequest]::Create($url);
   #to check if a proxy is required
   $webclient = new-object System.Net.WebClient
    if (!$webclient.Proxy.IsBypassed($url))
    {
        $cred = get-credential
        $proxyaddress = $webclient.Proxy.GetProxy($url).Authority
        Write-host "Using this proxyserver: " $proxyaddress
        $proxy = New-Object System.Net.WebProxy($proxyaddress)
        $proxy.credentials = $cred.GetNetworkCredential();
        $req.proxy = $proxy
    }```
And now all is good and the chocolatey gods are happy again. As you can see in the following screenshots.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey5.png?mtime=1311758827"><img alt="" src="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey5.png?mtime=1311758827" width="727" height="443" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey6.png?mtime=1311758836"><img alt="" src="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey6.png?mtime=1311758836" width="677" height="392" /></a>
</div>

## Conclusion

With a few simple tricks you can make chocolatey work from behind a proxy. I might even be able to convince Rob to even add this to the code.

 [1]: /index.php/DesktopDev/MSTech/chocolatey-apt-get-for-windows
 [2]: https://github.com/ferventcoder
 [3]: https://github.com/ferventcoder/chocolatey/wiki/Installation