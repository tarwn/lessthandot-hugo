---
title: Merge Replication for SQL Server Auditing
author: Ted Krueger (onpnt)
type: post
date: 2014-07-24T19:53:03+00:00
url: /index.php/uncategorized/merge-replication-for-auditing/
views:
  - 12889
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized

---
I recently had the pleasure of presenting for the <a href="http://adelaide.sqlpass.org/" title="Adelaide SQL Server User Group" target="_blank">Adelaide SQL Server User Group</a>. I truly enjoyed the presentation and the group attending. The best part of this presentation is, it was on Merge Replication and all the great things we can accomplish with it. 

While setting up demonstrations for that presentation, I began to think about how effective we can be with SQL Server features. While some features may be specifically written for tasks, they are not always feasible in our specific environments. I’d like to share one of those features and methods to effectively take advantage of what we can do with SQL Server, in this article. 

If I had to come up with a percentage of clients that have asked me to implement a way of monitoring or reviewing when and how data was changed, I’d have to say that percentage is hovering in the 80% range. In today’s data age, it is simply a much needed aspect to evolving data; knowledge of data change and point of origins. Change Data Capture (CDC) is a great feature and one that directly goes to answering this requirement. However, in some cases, CDC isn’t a viable solution. This could be due to implementation requirements, instance impact of log readers and so on. In that case, we have to start thinking outside the box of native, wizard-based installations, and start looking to how we can utilize other features. 

For this case, Merge Replication may be a good option and one I’ve effectively implemented in the past simply for change tracking and reporting. Merge Replication with a specific publication configuration, can work much like CDC or Transactional Replication but remove the log reader and other internal tables that would require reporting on, directly in the same database. We can enhance the designs of Merge Replication Publishers to also make effective use of conflicts with customer coding in business logic handlers. We then have the ability to begin altering the way the subscribers of the data begin to receive data. 

For example, take a scenario where we have the following requirements.

**Problem**: You have a primary single instance that has a database, low transaction rates, and there is a need to monitor changes in a reporting manner. The primary single instance cannot allow a log reader or CDC implementation due to Standard Edition of the instance.
  
**Solution**: Implement a merge replication publication on the tables that require change monitoring. Make the articles in the publication download-only and set a conflict resolver to a business logic handler that bypasses the actual updates to the subscriber and inserts the new data into a secondary table.

The solution would appear as follows

[<img src="/wp-content/uploads/2014/07/merge_1.png" alt="merge_1" width="454" height="154" class="alignnone size-full wp-image-2871" srcset="/wp-content/uploads/2014/07/merge_1.png 454w, /wp-content/uploads/2014/07/merge_1-300x101.png 300w" sizes="(max-width: 454px) 100vw, 454px" />][1]

How we can accomplish this is to begin with our publication design. We will use AdventureWorks2012 as our demonstration database and create a publication that sends the Person.Person table. This table is a perfect example of an audit need, due to the nature of data that may change on a human resources basis.
  
There are a few key aspects to this article that are required to be set. 

First, the table will require column-level tracking 

[<img src="/wp-content/uploads/2014/07/merge_2.png" alt="merge_2" width="442" height="162" class="alignnone size-full wp-image-2870" srcset="/wp-content/uploads/2014/07/merge_2.png 442w, /wp-content/uploads/2014/07/merge_2-300x109.png 300w" sizes="(max-width: 442px) 100vw, 442px" />][2]

Second, the resolver should be set to a business logic handler that we will create.

[<img src="/wp-content/uploads/2014/07/merge_3.png" alt="merge_3" width="518" height="359" class="alignnone size-full wp-image-2869" srcset="/wp-content/uploads/2014/07/merge_3.png 518w, /wp-content/uploads/2014/07/merge_3-300x207.png 300w" sizes="(max-width: 518px) 100vw, 518px" />][3]

To create the PersonAudit resolver, we will need to do coding in Visual Studio and compile a class file that we place in the SQL Server system folders. The class file (assembly file) can be placed in another folder, but assurance this file will not be removed is best. The SQL Server system directories are, hopefully, protected in a critical nature.
  
Business Logic Handler coding is very standard. You will want to capture Update, Delete and Insert events. To write this code, you do not need to be an expert in C# development but just have a solid understanding of coding practices, reading the code and how the execution will occur.

MSDN has a completed code area for the base structure to use for your own business logic handler. Using this base code, we can alter the code to take advantage of the Action handler. In this case, we would see, the final action each event takes is, “ActionOnDataChange.AcceptData”.
  
For the UPDATE handler

<pre>public override ActionOnDataChange UpdateHandler(SourceIdentifier updateSource,
          DataSet updatedDataSet, ref DataSet customDataSet, ref int historyLogLevel,
          ref string LogMessage)
            {
                updatedDataSet.AcceptChanges();
            }</pre>

This event handler will basically do nothing but accept all changes that are being replicated down to the subscriber. In our case, we need a very simple change to bypass this and instead, insert data into a custom table that we create to match the schema of the articles table.

Using a bulk loading method and the RejectData action, we can accomplish this

<pre>public void BulkUpload(DataTable dt)
        {
            dt.TableName = "PersonAudit";
            string constr = "Server=ONPNT\\SQL2008R2;Database=AW_SalesOrder_Report;Trusted_Connection=True;";
            
      
            using (SqlConnection connection = new SqlConnection(constr))
            {
                connection.Open();
                
                SqlTransaction trans = connection.BeginTransaction();
                using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection,
                SqlBulkCopyOptions.TableLock |
                SqlBulkCopyOptions.FireTriggers,trans))
                {
                    bulkCopy.BulkCopyTimeout = 0;
                    bulkCopy.DestinationTableName = dt.TableName;
                    bulkCopy.WriteToServer(dt);
                    trans.Commit();
                }
            }
        }</pre>

Now the update handler would appear as

<pre>public override ActionOnDataChange UpdateHandler(SourceIdentifier updateSource,
          DataSet updatedDataSet, ref DataSet customDataSet, ref int historyLogLevel,
          ref string LogMessage)
            {
                updatedDataSet.AcceptChanges();
                    BulkUpload(updatedDataSet.Tables[0]);
                    return ActionOnDataChange.RejectData;
            }</pre>

All we need to do now is compile (build) this project and utilize the .DLL file that is generated in the debug or release folders for our project. 

[<img src="/wp-content/uploads/2014/07/merge_4.png" alt="merge_4" width="294" height="136" class="alignnone size-full wp-image-2868" />][4]

Copy the PersonAudit.dll to your COM directory within the SQL Server system directories.
  
Once the .DLL is copied, the resolver must be registered. This is done by using the sp_registercustomresolver procedure.

<pre>Use Distribution
GO
sp_unregistercustomresolver @article_resolver='PersonAudit'
GO
sp_registercustomresolver @article_resolver='PersonAudit',
@is_dotnet_assembly = 'true',
@dotnet_assembly_name = 'C:\Program Files\Microsoft SQL Server\100\COM\PersonAudit.dll',
@dotnet_class_name = 'PersonBusinessLogicHandler.PersonAudit'
GO
sp_enumcustomresolvers</pre>

[<img src="/wp-content/uploads/2014/07/merge_5.png" alt="merge_5" width="624" height="157" class="alignnone size-full wp-image-2867" srcset="/wp-content/uploads/2014/07/merge_5.png 624w, /wp-content/uploads/2014/07/merge_5-300x75.png 300w" sizes="(max-width: 624px) 100vw, 624px" />][5]

Notice the @dotnet\_class\_name parameter. This parameter is critical for the proper name and is a common problem area. If you revisit the solution in Visual Studio, this two part name will be equivalent to the namespace and class in your code.

[<img src="/wp-content/uploads/2014/07/merge_6.png" alt="merge_6" width="728" height="182" class="alignnone size-full wp-image-2866" srcset="/wp-content/uploads/2014/07/merge_6.png 728w, /wp-content/uploads/2014/07/merge_6-300x75.png 300w" sizes="(max-width: 728px) 100vw, 728px" />][6]

The last piece required is the audit table creation on the subscriber. This table can be as elaborate or simplistic as needed. Keep in mind, the longer the bulk loading process takes, the more impact you can have on the system. It is always recommended to keep the tables maintained the same while adding a capture data time and possible login account, if applicable. For our test, the table dbo.PersonAudit is created on the subscriber and it identical to the Person.Person table.
  
Now that we have a publication containing the Person.Person table as the article, column-level tracking set, and the resolver set to our customer business logic handler, we can test the solution. 

**Testing Audits**
  
Again, our landscape consists of a publisher, a subscriber, and a resolver between them to manage the synchronization to the subscriber. The subscriber’s article is set for download only so we are assured no changes will be flowing back to the publisher.
  
On the publisher and the AdventureWorks2012 database, we can run a test by updating the Title column.

First, check the value of Title for BusinessEntityID of 1

<pre>SELECT * FROM Person.Person WHERE BusinessEntityID = 1</pre>

[<img src="/wp-content/uploads/2014/07/merge_7.png" alt="merge_7" width="624" height="66" class="alignnone size-full wp-image-2874" srcset="/wp-content/uploads/2014/07/merge_7.png 624w, /wp-content/uploads/2014/07/merge_7-300x31.png 300w" sizes="(max-width: 624px) 100vw, 624px" />][7]

Now execute a simple UPDATE statement (The Title initial value was Test2 for this publication initialization)

<pre>UPDATE Person.Person
SET Title = 'Test5'
WHERE BusinessEntityID = 1</pre>

On the subscriber server, we want to right click the subscriber in the Replication listing and open the View Synchronization Status. From here, we can force synchronization to occur. 

[<img src="/wp-content/uploads/2014/07/merge_8.png" alt="merge_8" width="624" height="352" class="alignnone size-full wp-image-2873" srcset="/wp-content/uploads/2014/07/merge_8.png 624w, /wp-content/uploads/2014/07/merge_8-300x169.png 300w" sizes="(max-width: 624px) 100vw, 624px" />][8]

Click Start and wait for the process to complete.
  
Once synchronization is completed, we can review the dbo.PersonAudit table and the Person.Person table

[<img src="/wp-content/uploads/2014/07/merge_9.png" alt="merge_9" width="624" height="216" class="alignnone size-full wp-image-2872" srcset="/wp-content/uploads/2014/07/merge_9.png 624w, /wp-content/uploads/2014/07/merge_9-300x103.png 300w" sizes="(max-width: 624px) 100vw, 624px" />][9]

We can see that many tests have occurred but the existing Title still remains in the Person.Person table while capturing each change. Again, for a completion solution, addition datetime columsn can be added for the reporting aspect and following changes as they may happen. Reporting then becomes a simplistic aspect to the processing flow and low impact on the total data services in the primary database. 

**Summary**
  
Taking advantage of the features natively available in SQL Server is an extremely effective way to accomplish tasks that may be nicely packaged in other features, simply not available to the edition or configurations. We’ve shown a great way of managing Merge Replication in a data audit solution just for this concept. Keep in mind, edition costs and other blocking events may cause you to not have access to features such as CDC but, there are many other features that provide much functionality far and beyond what they may seem to have on the surface.

 [1]: /wp-content/uploads/2014/07/merge_1.png
 [2]: /wp-content/uploads/2014/07/merge_2.png
 [3]: /wp-content/uploads/2014/07/merge_3.png
 [4]: /wp-content/uploads/2014/07/merge_4.png
 [5]: /wp-content/uploads/2014/07/merge_5.png
 [6]: /wp-content/uploads/2014/07/merge_6.png
 [7]: /wp-content/uploads/2014/07/merge_7.png
 [8]: /wp-content/uploads/2014/07/merge_8.png
 [9]: /wp-content/uploads/2014/07/merge_9.png