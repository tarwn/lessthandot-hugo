---
title: SQL Azure Compatibility Assessment service released
author: SQLDenis
type: post
date: 2012-01-04T08:42:00+00:00
ID: 1477
excerpt: 'If you are considering moving your SQL Server databases to SQL Azure, you can use the SQL Azure Compatibility Assessment to check if your database schema is compatible with SQL Azure. This service is very easy to use and does not require an Azure accoun&hellip;'
url: /index.php/datamgmt/datadesign/sql-azure-compatibility-assessment-service/
views:
  - 7372
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - azure
  - cloud computing
  - sql azure

---
If you are considering moving your SQL Server databases to SQL Azure, you can use the SQL Azure Compatibility Assessment to check if your database schema is compatible with SQL Azure. This service is very easy to use and does not require an Azure account.

To use this service, you need a Live ID and a .dacpac extracted from your database, you do not need to have an Azure account or have Azure knowledge

In order to use the SQL Azure Compatibility Assessment you need to prepare a .dacpac of your database and go to the service portal: https://assess.sql.azure.com/ to use the service.

There is a 2 minute and 52 seconds video available here: http://www.microsoft.com/en-us/showcase/details.aspx?uuid=6302dccd-3a20-483e-8ec7-5ff5ad72c2c2

The SQL Azure Compatibility Assessment will give you a report of things that are not supported (like full text indexes) or things you need to fix (like adding a clustered index if you want to do inserts)

More information about SQL Azure Compatibility Assessment can be found in this TechNet wiki article: http://social.technet.microsoft.com/wiki/contents/articles/6246.aspx

Happy cloud computing 🙂