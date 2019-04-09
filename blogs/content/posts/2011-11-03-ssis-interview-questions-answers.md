---
title: SSIS Interview questions – Answers
author: Ted Krueger (onpnt)
type: post
date: 2011-11-03T12:09:00+00:00
ID: 1369
excerpt: |
  This post will contain the way I would answer the question that were posted in, "SSIS Test yourself with these interview questions".  
  
  What is an Error-Tolerant Index?This question in an interview process may stump you up until the point you hear, Fu&hellip;
url: /index.php/datamgmt/dbprogramming/ssis-interview-questions-answers/
views:
  - 16946
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - SSIS

---
<div>
  <div class="image_block">
    <a href="/media/blogs/DataMgmt/bobs.gif?mtime=1320326652"><img src="/wp-content/uploads/blogs/DataMgmt/bobs.gif?mtime=1320326652" alt="" width="350" height="263" /></a>
  </div>
  
  <p>
    This post will contain the way I would answer the questions that were posted in, “<a href="/index.php/DataMgmt/ssis/ssis-interview-questions">SSIS Test yourself with these interview questions</a>“. I will post a few answers a day. Hope they help someone out there have a good understanding of the questions and why they may be a great thing to ask in an interview or testing situation. </div>
  </p>
  
  <p>
    <div>
      <strong>What is an Error-Tolerant Index?</strong>
    </div>
  </p>
  
  <blockquote>
    <p>
      <div>
        This question in an interview process may stump you up until the point you hear, Fuzzy Lookup. Think of an Error-Tolerant Index in respect to full text search keywords and indexing. In FTS, keywords can be exposed by using things like the fts_index_keywords DMF. (I believe that is the correct DMF. If not, it is close) In the FTS keyword list, several keywords are indexed and the location retained. That is the basics of the architecture of the index itself at a very skyscraper view.
      </div>
    </p>
    
    <p>
      <div>
        If I have this in a document, “The dog went to the park with his owner on a leash”, the keywords here would perhaps be dog, park, owner, leash. Remember, skyscraper view. In the Error-Tolerant Index, this concept is roughly the same. The index takes words and chops them up, creating the index itself. So take the sentence again: “The dog went to the park with his owner on a leash”. The Error-Tolerant Index equates this to the words, the, dog, went, to, the, park, with, his, owner, on, a, leash. The index knows the location of them and exactly how to get back there based on each word, hence the meaning of an index. In the Error-Tolerant Index, it does go further into chopping even the words themselves up.
      </div>
    </p>
    
    <p>
      <div>
        If you have ever heard someone complaining loudly about the performance of a fuzzy lookup, this point may be the issue itself. By default, when using the fuzzy lookup, you will start building and rebuilding this indexing structure. The easiest way to increase this overall process is to store that index on the server. The reason I mention this at this point; if a candidate does get to this point and impresses the entire place by answering this question, the next step is not to say, “Wow, yeah, You nailed that one. Let's move on.” but more so to continue on the line of questioning. Let’s be honest, only weirdo’s read BOL and would know how the fuzzy lookup works without every actually using it and maybe, having problems with it? What starts to become impressive is the line of troubleshooting an everyday BI Developer, SQL Developer or DBA would take to become knowledgeably on the fact that the fuzzy lookup caused performance problems and they researched, learned and dissected the true overall processing of this area. Knowing this information, the person can now successfully start, execute and release a product from SSIS with much greater efficiency, both on time to developer and overall execution time.
      </div>
    </p>
  </blockquote>
  
  <p>
    <strong>How would I implement logical or conditional precedence in SSIS?</strong>
  </p>
  
  <blockquote>
    <p>
      I think the right way to ask this would be, “How would I implement logical…without finding myself in a field of weeds and red boxes?”<br /> The best method I’ve learned over the years for getting this job done is using the expression precedence constraint. If you say that, you’re golden. You can conditionally move to tasks and components based on an expression in the precedence constraint. This makes for some intelligence in how the resource you are consuming will make the package react or even, what other packages you will need to call in order to set the stage right for a stable ETL.
    </p>
  </blockquote>
  
  <p>
    <strong>Tell me some examples you’ve implemented error handling in SSIS and how you did</strong>
  </p>
  
  <blockquote>
    <p>
      In its most simplistic form, error handling in SSIS during runtime is accomplished with precedence constraints: On Success, On Failure and On Evaluation. The On Completion precedence can be considered a type of error handling mechanism but realistically, this setting in the flow between tasks and components is more bypassing the concept of handling errors. Essentially, based on a successful or failure of a task or component, the control flow can be manipulated to perform several different actions. Of course, these actions are anything in the SSIS arsenal as it is.<br /> Such situations that evolve due to actual data at runtime can also be error handled by setting transforms to handle these on each column. Enhanced error handling in this case can be performed with script components as transformations.<br /> Event Handlers are another choice that can further handle situations that occur on and within tasks and components. Although event handlers have a more granular ability of logging, the actions that can occur from within them allow the ability for execution of certain tasks. Event handlers also allow you to result an actual error with conditions that may allow the package to continue. For example, the OnError handler could potentially call a task to run a SQL statement while passing in values that may help decide certain external resource situations.
    </p>
  </blockquote>
  
  <p>
    <strong>What are some logging mechanisms in SSIS? </strong>
  </p>
  
  <blockquote>
    <p>
      Truly, there is two primary logging mechanisms in SSIS; Logging and Event Handlers. Logging is built directly into SSIS and can be configured to log as little or as much information that is required. Before going on, logging is as with anything, resource intensive so the more you log, the more time you take to do so. The best example that I can think of here in configuring the built in logging is, capturing the pipe line execution events and logging them. Other logging configurations that could be useful are logging on specific tasks that may be failing or simply require a high level of auditing to be performed. A SQL Server table and text files are traditionally the most common ways to configure the logging with SSIS but there are others such as windows logs logging and I believe, XML. The securities in things that go beyond a table or text file get in the way of a lot of things though. (Note: in an answer situation, that statement could potentially cause a drastic change in questioning and derail the interviewer to dig deep into why you stated it. Security mentioning always gets attention)<br /> Event Handlers fall in logging because we can perform custom calls to do things with the information that can be logged with them. Stored procedures can insert into custom tables that can relate to custom reports or email out logs. This type of information gathering and debugging has over the time of SSIS and the evolution of the ETL platform as been extremely useful.
    </p>
  </blockquote>
  
  <p>
    <strong>How can you achieve parallelism in SSIS? </strong>
  </p>
  
  <blockquote>
    <p>
      Adjust the option, MaxConcurrentExecutables
    </p>
  </blockquote>
  
  <p>
    <strong>What types of things can I pass between packages in SSIS? </strong>
  </p>
  
  <blockquote>
    <p>
      We can pass Variables primarily between packages. Within a variable we can pass them as any type that is available. So if you were to create an object variable, although memory consuming, we could potentially pass a table that is in memory. Granted, in SQL Server 2012 (Denali) this is much, much easier now with parameters. Actually, this was almost a relief in a way. Configuring packages to consume parent variables was a time consuming and in some cases, confusing situation when many variables were in the process.
    </p>
  </blockquote>
  
  <p>
    <strong>List and describe the SSIS architecture components. </strong>
  </p>
  
  <blockquote>
    <p>
      Runtime Engine, Data Flow Engine (if these two are not mentioned then there is a problem). Designer is a major component, the API and the SSIS Services. (There are one or two more but these are the major ones)
    </p>
  </blockquote>
  
  <p>
    <strong>Describe a Partially blocking transformations and what components would use this type of transform in a data flow task. </strong>
  </p>
  
  <blockquote>
    <p>
      In the real world we have multiple sources that we want to combine in some way. This in SSIS terms is a partially blocking transformation. When two or more sources are coming in and they are combined into one complete result, it is a partially blocking transformation. The most common one that I can think of is the Merge. Union comes next in line for a partially blocking transformation.
    </p>
  </blockquote>
  
  <p>
    <strong>Explain the execution tree in SSIS. How did it change in SQL Server 2008 from 2005? </strong>
  </p>
  
  <blockquote>
    <p>
      It *is* the data flow. Well, really it is the buffer and threads that handle everything. Remember that, “Execution Tree is the buffer and threads that make up a Data Flow”.<br /> In 2008 this changed a bit overall. I wrote about this, praised it and gave it a big, O.M.G. that is cool. Essentially, in 2005 multi-processing power could not be taken advantage of by running components in parallel. In 2008 that was changed. The pipeline can divide and concur by using multiple, sub paths to execute multiple components at the same time (in parallel).
    </p>
  </blockquote>
  
  <p>
    <strong>If you change the DefaultMaxBufferRows in a package, what else do you need to change/ consider/relate based on your</strong>
  </p>
  
  <blockquote>
    <p>
      This is by far the toughest question even for me to answer. Without looking it up, as I need to every time when I want to adjust this setting, I can say that the DefaultMaxBufferRows relates directly to the DefaultMaxBufferSize and the Min/MaxBufferSize. First, the MaxBufferSize is maxed out at 100MB, I believe. This overwrites anything in the DefaultMaxBifferSize if it is set higher than the MaxBufferSize. This is all important to the DefaultMaxBufferRows because, well, size matters. SSIS determines how large, or small, a set of rows are based on calculating the estimated row size (determined when SSIS evaluates the source) * the DefaultMaxBufferRows. If this is less than the minimum buffer setting (can’t be changed and based on the server itself) rows are increased in the buffer, memory is optimized, or it is attempted to be optimized. On the other hand, if you exceed the MaxBfferSize with the multiplication of the estimated and default row size, you lose by SSIS reducing the row count in the buffer. This could result in a buffer not full. More time thinking, more time processing.<br /> The end result is, adjust these when you want to maximum the buffer size and fit as many rows in it as possible to prevent multiple buffers from being needed or poor results from over-sizing the buffer.
    </p>
    
    <p>
      I asked Josef Richberg (<a href="http://josef-richberg.squarespace.com/">Blog</a> | <a href="http://twitter.com/#!/sqlrunner">Twitter</a>) to help with answering this because I know my abilities to explain it were not the best. Granted I could and did above, the explanation after reading it and asking Josef to read it, becomes convoluted. Now Josef is one of the best writers I know. He may argue with me there but it's true. Everything he's written is clear, precise and just about always 100% accurate. He would be, one of a few, that I know could take this line of questioning and completely blow the person asking the questions out of the water with their answers.
    </p>
    
    <p>
      Josef responded with his, more clear, answer and since this list will potentially be used for others to interview, it should be used as reference here. Visit Josef's blog when you can. Really good technical articles on SSIS.
    </p>
    
    <blockquote>
      <p>
        To get the most out of the system you want to fill any &#8216;minimums'. That is to say, if I cannot reduce the min memory, I should optimize to use at least that much. If I have a minimum number of buffers, I should optimize to use at least that many. Here is how you do that.
      </p>
      
      <p>
        First calculate the avg size, in bytes, of a given row you want to process, which you can call RowSize. This calculation (10485760/RowSize) will give you the number of rows you can fit in 10MB. If the number <10,000 then increase your buffer size until the number is at or slightly higher than 10,000 (I would go slightly over to use all of the 10,000 buffers). This enables you to use all of the buffers since it cannot be lowered. If the number you calculate is >10,000, then increase DefaultBufferMaxRows to equal the calculated number. That will allow you to use all of the 10MB, which you again, cannot lower.
      </p>
    </blockquote>
  </blockquote>