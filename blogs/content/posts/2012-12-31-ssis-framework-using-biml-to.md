---
title: 'SSIS Framework: Dynamically Create Packages to Extract Flat Files Using BIML'
author: Sam Vanga
type: post
date: -001-11-30T00:00:00+00:00
ID: 1824
excerpt: |
   
  Solution: SSIS Framework for Developing Extract Packages
  What are the benefits of this framework?
  
  Faster development time. 
  Handle changes in source file format.
  
  What is this framework?
  
  Metadata driven.
  Dynamic package creation.
  
  Pushin&hellip;
draft: true
url: /?p=1824
categories:
  - Business Intelligence
  - SSIS

---
When importing flat files to SQL Server destination, it's a common pattern to extract them to staging tables. Subsequent phases of ETL, transform and load, move data from staging to destination. A typical data integration project needs to import data from [at least] hunderds of files. Manually creating extract packages for hundreds of files is like watching Jersey Shore and South Beach Tow at the same time – painful.

In this post, I provide a framework that automates the process of creating SSIS packages to extract data from flatfiles to staging tables; I call them extract packages. This framework is metadata driven and creates packages dynamically.

Follow along!

### Step0

  * Download BIDS Helper from Codeplex
  * Download sample files from my Skydrive.
  * Save the sample files to C:SSIS Frameworks.

### Step1: Create and Populate File Metadta

Use the following script to create a new database called FileMetada. The two tables SourceFile and SourceFileFormat store data I need for creating SSIS packages. Spend a minute and closely look at the columns these tables have. I'll take a leap and leave out why I store them for now, but we will get to it shortly.

<Script 01 goes here>.

### Step2: Create StgDB

Create another database called StgDB, and staging tables.

<Script 02 goes here>.

Wait a minute. If there are 500 tables, writing create statements for each of those tables isn't fun, is it? Moreover, we have information in the FileMetada database that we can use to dynamically generate create statements.

### Step3: Generate Create Statements Dynamically

<Script 03 here>.

This script returns create statements for each file in the FileMetadata database. We've already created the staging tables, so we do't need it now.