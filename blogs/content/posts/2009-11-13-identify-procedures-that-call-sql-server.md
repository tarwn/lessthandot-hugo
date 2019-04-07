---
title: Identify procedures that call SQL Server undocumented procedures
author: George Mastros (gmmastros)
type: post
date: 2009-11-13T11:30:39+00:00
ID: 628
url: /index.php/datamgmt/datadesign/identify-procedures-that-call-sql-server/
views:
  - 46952
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
When you use an undocumented stored procedure, you run the risk of not being able to upgrade your database to a new version. What&#8217;s worse&#8230; you could have broken functionality and not even know it. With undocumented stored procedures, Microsoft may not document when they decide to deprecate it, so you may not know about your broken functionality until it&#8217;s too late.

Presented below is a hard coded list of undocumented stored procedures. By their very nature, it is hard to find documentation on undocumented procedures. Therefore, the procedures in the list below is likely to be incomplete.

**How to detect this problem:**

sql
Declare @Temp Table(ProcedureName VarChar(50))

Insert Into @Temp Values('sp_MStablespace')
Insert Into @Temp Values('sp_who2')
Insert Into @Temp Values('sp_tempdbspace')
Insert Into @Temp Values('sp_MSkilldb')
Insert Into @Temp Values('sp_MSindexspace')
Insert Into @Temp Values('sp_MShelptype')
Insert Into @Temp Values('sp_MShelpindex')
Insert Into @Temp Values('sp_MShelpcolumns')
Insert Into @Temp Values('sp_MSforeachtable')
Insert Into @Temp Values('sp_MSforeachdb')
Insert Into @Temp Values('sp_fixindex')
Insert Into @Temp Values('sp_columns_rowset')
Insert Into @Temp Values('sp_MScheck_uid_owns_anything')
Insert Into @Temp Values('sp_MSgettools_path')
Insert Into @Temp Values('sp_gettypestring')
Insert Into @Temp Values('sp_MSdrop_object')
Insert Into @Temp Values('sp_MSget_qualified_name')
Insert Into @Temp Values('sp_MSgetversion')
Insert Into @Temp Values('xp_dirtree')
Insert Into @Temp Values('xp_subdirs')
Insert Into @Temp Values('xp_enum_oledb_providers')
Insert Into @Temp Values('xp_enumcodepages')
Insert Into @Temp Values('xp_enumdsn')
Insert Into @Temp Values('xp_enumerrorlogs')
Insert Into @Temp Values('xp_enumgroups')
Insert Into @Temp Values('xp_fileexist')
Insert Into @Temp Values('xp_fixeddrives')
Insert Into @Temp Values('xp_getnetname')
Insert Into @Temp Values('xp_readerrorlog')
Insert Into @Temp Values('sp_msdependencies')
Insert Into @Temp Values('xp_qv')
Insert Into @Temp Values('xp_delete_file')
Insert Into @Temp Values('sp_checknames')
Insert Into @Temp Values('sp_enumoledbdatasources')
Insert Into @Temp Values('sp_MS_marksystemobject')
Insert Into @Temp Values('sp_MSaddguidcolumn')
Insert Into @Temp Values('sp_MSaddguidindex')
Insert Into @Temp Values('sp_MSaddlogin_implicit_ntlogin')
Insert Into @Temp Values('sp_MSadduser_implicit_ntlogin')
Insert Into @Temp Values('sp_MSdbuseraccess')
Insert Into @Temp Values('sp_MSdbuserpriv')
Insert Into @Temp Values('sp_MSloginmappings')
Insert Into @Temp Values('sp_MStablekeys')
Insert Into @Temp Values('sp_MStablerefs')
Insert Into @Temp Values('sp_MSuniquetempname')
Insert Into @Temp Values('sp_MSuniqueobjectname')
Insert Into @Temp Values('sp_MSuniquecolname')
Insert Into @Temp Values('sp_MSuniquename')
Insert Into @Temp Values('sp_MSunc_to_drive')
Insert Into @Temp Values('sp_MSis_pk_col')
Insert Into @Temp Values('xp_get_MAPI_default_profile')
Insert Into @Temp Values('xp_get_MAPI_profiles')
Insert Into @Temp Values('xp_regdeletekey')
Insert Into @Temp Values('xp_regdeletevalue')
Insert Into @Temp Values('xp_regread')
Insert Into @Temp Values('xp_regenumvalues')
Insert Into @Temp Values('xp_regaddmultistring')
Insert Into @Temp Values('xp_regremovemultistring')
Insert Into @Temp Values('xp_regwrite')
Insert Into @Temp Values('xp_varbintohexstr')
Insert Into @Temp Values('sp_MSguidtostr')

Select Distinct Name 
From   sysobjects 
       Inner Join @Temp T
         On Object_Definition(id) Like '%' + T.ProcedureName + '%'		
Where  XType = 'P'
       And ObjectProperty(ID, N'IsMSShipped') = 0
Order By Name
```
**How to correct it:** Rewrite your functionality so that is does not rely upon undocumented procedures.

**Level of severity:** moderate to high

**Level of difficulty:** moderate to high. Undocumented stored procedures are often used because it&#8217;s easy. Replacing it is usually NOT easy.