
_This is why you write a readme on projects, so you know how to work them a couple years later when your CDN goes bankrupt._

# Running Locally

1. open a console, `cd blogs`
2. `hugo server` to launch the site locally

# All the media

All of the old wordpress media is now linked from Blob Storage: https://lessthandot.z19.web.core.windows.net/

This also has an older generated copy of the static site, as the first home of the archive.

## Asset files

All the uploads for the original wordpress site are stored outside the git repo because it's a lot of media content.

1. Blob Storage: lessthandot storage account in LessThanDot Archive subscription: https://lessthandot.z19.web.core.windows.net/
2. Eli's harddrive and NAS backup
3. Original LTD Wordpress FTP backups, if they exist still


# TODO

Main Site:
1. ✔ Update google analytics to newer version
2. ✔ Update hugo to newer version
3. ✔ Fix errors and layout changes due to extreme version upgrade of hugo
4. ✔ Reroute all wp-content to go to azure storage $web directly so we don't have to re-host it
    * replace `/wp-content` to `https://lessthandot.z19.web.core.windows.net/wp-content`
    * assets in storage are doubled, both in original case and lowercase
5. Build process to use github action and deploy to an Azure static website
6. Fix issues
    * Custom 404 page
7. Change DNS to point to new site

SQLCop:
1. Update download links to pull directly from storage
2. Remove DNS entry

Cleanup:
1. Set the edgio feature flag to not convert to frontdoor
2. cleanup edgio CDNs somehow?
