---
title: Access Git Commits during a TeamCity Build using Powershell
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-10-29T19:13:12+00:00
ID: 3026
url: /index.php/uncategorized/access-git-commits-during-a-teamcity-build-using-powershell/
views:
  - 12283
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized
tags:
  - build automation
  - git
  - powershell
  - teamcity

---
Recently I needed access to the list of commits that were included with each of my TeamCity builds. TeamCity provides a pretty big list of [Predefined Build Parameters][1], but it doesn't provide access to details of the commits it is currently building. Having Powershell and Git on my server, though, I can write some scripts to extract not just information about the latest commit, but about any series of commits that have occurred.

# Extracting Usable Commit Details

In this script, I am extracting just the list of authors, dates, and commit messages. I formatted the git log output so I could easily feed it into Powershell's [ConvertFrom-StringData][2] method to get an array of objects.

```powershell
function Get-CommitsFromGitLog([string] $StartCommit, [string] $EndCommit){
    $Cmd = "git log --pretty=format:""CommitHash=%H :: AuthorEmail=%ae :: AuthorDate=%ad :: Subject=%s"" $StartCommit...$EndCommit"

    $Result = Invoke-Expression $Cmd
    $ParsedResult = $Result | %{ ConvertFrom-StringData($_ -replace " :: ", "`n")  }

    return $ParsedResult
}
```
TeamCity defines a Build Parameter named [build.vcs.number][3], so we could use this script to get details about that specific commit like so:

```powershell
Get-CommitsFromGitLog -StartCommit "%build.vcs.number%^" -EndCommit "%build.vcs.number%"
```
The net effect is that I'm asking for all changes starting one commit before the identified one (the ^ at the end) through that identified one. 

Unfortunately, this is only the latest commit. Retrieving the details for a group of commits requires some additional work.

# Finding the Previous Commit Hash

This is the tricky part. If TeamCity provided the build.vcs.number from the previous build, we would probably be using that. Though, realistically, if our build is successful and the prior one wasn't, we would only be listing some of the changes being deployed. Really what we need is the build.vcs.number from the _last successful build_, which definitely isn't built in.

However, TeamCity does have a [REST API][4] that exposes details about prior builds. There is also a built in service account we can use to access that API, and the credentials and URL are all available in the REST API. So we can implement some calls without the extra pain of accidentally breaking the build every time Joe the developer changes his password and forgets he had it in the script too.

The API exposes methods to access prior [Build Requests][5]. For what we are doing, we want to get a build for a specific Build Type (the running build configuration) and in my case I want the last successful build, not just the last build. using the request for build by build type id, we would be looking for something like:

`$($TeamCityUrl)/app/rest/buildTypes/id:$($TeamCityBuildTypeId)/builds/status:SUCCESS`

Using [Invoke-WebRequest][6], we can write a script that accesses that build information:

```powershell
function Get-TeamCityLastSuccessfulRun([string] $TeamCityUrl, [string] $TeamCityBuildTypeId, 
                                       [string] $TeamCityUsername, [string] $TeamCityPassword){

    $Credentials = "$($TeamCityUsername):$($TeamCityPassword)"
    $AuthString = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("$Credentials")

    $Url = "$($TeamCityUrl)/app/rest/buildTypes/id:$($TeamCityBuildTypeId)/builds/status:SUCCESS" 

    $Content = Invoke-WebRequest "$Url" -Headers @{"Authorization" = "Basic $AuthString")}

    return $Content
}
```
Now I can combine some parameters from TeamCity, [Select-Xml][7], and the first script to get a list of commit information since the last successful TeamCity run.

```powershell
$Run = Get-TeamCityLastSuccessfulRun -TeamCityUrl "%teamcity.serverUrl%" 
                                     -TeamCityBuildTypeId "%system.teamcity.buildType.id%" 
                                     -TeamCityUsername "%system.teamcity.auth.userId%"
                                     -TeamCityPassword "%system.teamcity.auth.password%"

$LatestCommitFromRun = (Select-Xml -Content "$Run" -Xpath "/build/revisions/revision/@version").Node.Value

$CommitsSinceLastSuccess = Get-CommitsFromGitLog -StartCommit "$LatestCommitFromRun" 
                                                 -EndCommit "%build.vcs.number%"
```
And there we have it, a cumulative list of authors and commits since the last successful build. You could use similar logic to pass in parameters to get the whole history since the last pinned build, a specific tag in the git repository, etc.

# What Can You Do With This?

With those two functions, you now have the raw data about all commits that are being built/deployed with this build. You could use this data to automatically produce release notes, adding a basic HTML page to the product or website you are building. You could automatically append a list of contributors to a LATEST file with specifics about the build. If you have issue numbers or ticket numbers in the build, you could easily parse them out of the commit messages and output links in those documents. A richer contributors page is possible by outputting gravatar image tags using the authors emails addresses. Need auditability? Call a logging API with details about the commits, authors, and the URL back to the build in TeamCity.

The development team at [PrecisionLender][8] uses similar scripts as part of our automated deployment processes, specifically to help add audit information to our tickets so that every ticket reflects when it successfully passed through the automated tests and was later deployed to production. When an auditor asks for proof that N tickets went through our process correctly, we simply open up the ticket and point at the activity log attached to it, including those custom entries from TeamCity that link back to the relevant successful build results.

 [1]: https://confluence.jetbrains.com/display/TCD8/Predefined+Build+Parameters "TeamCity 8.x - Predefined Build Parameters"
 [2]: http://technet.microsoft.com/en-us/library/hh849900.aspx "MSDN - ConvertFrom-StringData"
 [3]: https://confluence.jetbrains.com/display/TCD8/Predefined+Build+Parameters#PredefinedBuildParameters-ServerBuildProperties
 [4]: https://confluence.jetbrains.com/display/TCD8/REST+API "TeamCity REST API"
 [5]: https://confluence.jetbrains.com/display/TCD8/REST+API#RESTAPI-BuildRequests "TeamCity 8.x - REST API - Build Requests"
 [6]: http://technet.microsoft.com/en-us/library/hh849901.aspx "Powershell - Invoke-WebRequest"
 [7]: https://technet.microsoft.com/en-us/library/hh849968.aspx "Powershell - Select-Xml"
 [8]: https://precisionlender.com