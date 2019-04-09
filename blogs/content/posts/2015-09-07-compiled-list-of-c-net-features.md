---
title: 'Compiled list of C# + .Net Features'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2015-09-07T10:16:29+00:00
ID: 4112
url: /index.php/desktopdev/mstech/compiled-list-of-c-net-features/
views:
  - 4050
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - .net
  - 'c#'

---
We have talked about doing this at work for a while, so I finally sat down this weekend and tried to make a list of C# features that the team could share. We intend to use this to help gauge who the best people are to ask questions in different areas (C# isn't our only list) as well as a list of things to learn when you're bored (after crossing off all the ones you know). I added in some of the .Net framework features to round the list off.

Based on their relevance in our team, I left some grouped together (like unsafe code and WPF) and broke others into details (ASP.Net). Do you see any I missed? How much of the list have you worked with in production?

# C# Language Features

**Statements:**

  * [C# 1.0] Iteration: for, foreach, while, do
  * [C# 1.0] Jump: break, continue, goto, return
  * [C# 1.0] Empty statement
  * [C# 1.0] Labeled statements
  * [C# 1.0] Conditional: if elseif else switch case default
  * [C# 1.0] Catching exceptions: try catch finally
  * [C# 1.0] Checked/Unchecked statements
  * [C# 1.0] lock
  * [C# 1.0] using

**Operators:**

  * [C# 1.0] Arithmetic + &#8211; * / %
  * [C# 1.0] Logical Comparisons & | ^ ! ~ && || true false
  * [C# 1.0] String concatenation
  * [C# 1.0] Increment/Decrement (++x &#8211;x x++ x&#8211;)
  * [C# 1.0] Binary Shift (<< >>)
  * [C# 1.0] Comparison (== != < > <= >=)
  * [C# 1.0] Assignment (= += -= *= /= %= &= |= ^= <<= >>=)
  * [C# 1.0] Indexing []
  * [C# 1.0] Cast ()
  * [C# 1.0] Conditional/Ternary (condition)?(if-true):(if-false)
  * [C# 2.0] Null Coalescing: ??
  * [C# 6.0] Null Propagation: ?.
  * [C# 1.0] Type Information as is sizeof typeof

**Language Features:**

  * [C# 1.0] Arrays
  * [C# 1.0] Operator Overloading
  * [C# 1.0] Inheritance: extends base
  * [C# 1.0] Hiding: new
  * [C# 1.0] Access modifiers: public protected private internal “protected internal”
  * [C# 1.0] Modifiers &#8211; static instance const virtual overrides sealed extern
  * [C# 1.0] Property Declaration
  * [C# 1.0] Constructors
  * [C# 1.0] Static Constructors
  * [C# 1.0] Destructors
  * [C# 1.0] Nested Classes
  * [C# 1.0] Events, Declaration and Usage
  * [C# 1.0] Indexers
  * [C# 1.0] Properties
  * [C# 1.0] Interfaces
  * [C# 1.0] Structs
  * [C# 1.0] Enums
  * [C# 1.0] Delegates
  * [C# 1.0] Exceptions
  * [C# 1.0] Attributes
  * [C# 1.0] Unsafe Code: unsafe contexts, pointers, fixed/moveable variables, stack allocation
  * [C# 1.0] Boxing and Unboxing
  * [C# 1.0] Application Startup/Termination: Main, termination status code
  * [C# 1.0] Preprocessor Conditionals: #if #elif #else #endif
  * [C# 1.0] Preprocessor Declarations: #define #undef
  * [C# 1.0] Preprocessor Debug: #error
  * [C# 1.0] Preprocessor Region: #region #endregion
  * [C# 1.0] Preprocessor Region: #line
  * [C# 2.0] Preprocessor Region: #pragma warning
  * [C# 2.0] Generics &#8211; Usage
  * [C# 2.0] Generic Class Declaration
  * [C# 2.0] Generic Constraints: “where T”
  * [C# 2.0] Anonymous methods (inline delegates, closures)
  * [C# 2.0] Iterators: yield
  * [C# 2.0] Static Classes
  * [C# 2.0] Partial types: “public partial class XYZ”
  * [C# 2.0] Nullable types: “int? x = null”
  * [C# 2.0] namespace aliases: “using xyz = System.IO;”
  * [C# 2.0] Default value expression: default()
  * [C# 2.0] Conditional attribute
  * [C# 2.0] Fixed size buffers
  * [C# 2.0] Delegate Covariance/Contravariance
  * [C# 2.0] Friend Assemblies: aka “oops we made everything internal”
  * [C# 3.0] Implicitly Typed Local Variables: var
  * [C# 3.0] Extension Methods
  * [C# 3.0] Lambda expressions
  * [C# 3.0] Generic method Type Inference
  * [C# 3.0] Object and Collection Initializers
  * [C# 3.0] Anonymouse types
  * [C# 3.0] Implicitly typed arrays
  * [C# 3.0] Query Expressions: from into in join let orderby group select
  * [C# 3.0] Automatically implemented properties: “public int X { get; set; }”
  * [C# 3.0] Partial method declaration
  * [C# 4.0] Covariance, Contravariance
  * [C# 4.0] Dynamic Dispatch: dynamic
  * [C# 4.0] Named Arguments and Optional Parameters (for Methods)
  * [C# 5.0] Async modified: async await
  * [C# 5.0] Caller Information Attributes
  * [C# 6.0] Roslyn &#8211; Compiler-as-a-service
  * [C# 6.0] Initializers for Automatic Properties
  * [C# 6.0] Getter-only Automatic Properties
  * [C# 6.0] Lambda Expressions for Method Declaration
  * [C# 6.0] Lambda Expressions for Property Body Declaration
  * [C# 6.0] Using static
  * [C# 6.0] Null-conditional operators
  * [C# 6.0] String interpolation
  * [C# 6.0] nameof
  * [C# 6.0] Index Initializers syntax improvement
  * [C# 6.0] Exception filters
  * [C# 6.0] Exception filters

Where's LINQ???!? In the .Net Framework of course:

# .Net Framework Features

**.Net 1.0**

  * Collections: ArrayList, HashTable, Dictionary
  * Threading &#8211; ThreadPool, Thread, ThreadStart
  * Threading &#8211; Synchronized Regions: SynchronizationAttribute, Monitor (lock/SyncLock)
  * Threading &#8211; Manual Synchronization: Interlocked, WaitHandle/Mutex, ManualResetEvent, AutoResetEvent
  * ADO.Net &#8211; Database access using: Connection, Command, DataReader, DbParameter
  * ASP.Net &#8211; WebForms
  * ASP.Net &#8211; ASMX
  * ASP.Net &#8211; ASHX
  * ASP.Net &#8211; Page output caching
  * ASP.Net &#8211; Application Cache

**.Net 2.0**

  * Threading &#8211; BackgroundWorker
  * Threading &#8211; SynchronizationContext
  * Generics
  * Generic Collections: List<T>, Stack<T>, Queue<T>, Dictionary<TKey,TValue>, LinkedList<T>, SortedDictionary<TKey,TValue>, ReadOnlyCollection<T>, etc
  * Nullable types
  * Partial Classes
  * Anonymous methods
  * Iterators
  * Data Protection API
  * Globalization: Culture, CultureInfo
  * System.Diagnostics.EventLog
  * System.Net.Mail
  * ResGen.exe
  * Threading &#8211; System.Threading.Semaphore
  * ADO.Net &#8211; Asynchronous Processing
  * ADO.Net &#8211; SqlBulkCopy
  * ADO.Net &#8211; SQL Server User Defined Types
  * ADO.Net &#8211; SQL Server Max Data Types
  * ADO.Net &#8211; DataSet + DataAdapter Batch Processing
  * ADO.Net &#8211; Query Notifications
  * ADO.Net &#8211; Connection Pool Control
  * ADO.Net &#8211; System.Transactions
  * ADO.Net &#8211; DataTableReader
  * ADO.Net &#8211; Multiple Active Result Sets (MARS)
  * ASP.Net &#8211; Master/Content pages
  * ASP.Net &#8211; WebParts
  * ASP.Net &#8211; Skins/Themes
  * ASP.Net &#8211; Membership
  * ASP.Net &#8211; Profiles and Custom Profile Properties
  * ASP.Net &#8211; Cache Profiles
  * ASP.Net &#8211; Microsoft AJAX

**.Net 3.0**

  * WCF
  * WPF
  * Windows Workflow Foundation 1.0
  * Windows CardSpace

**.Net 3.5**

  * .Net Compact Framework
  * System.AddIn
  * Collections &#8211; HashSet<T>, SortedSet<T>
  * Pipes
  * LINQ
  * LINQ &#8211; Expression Trees
  * ADO.Net &#8211; LINQ to SQL
  * ADO.Net &#8211; LINQ to Dataset
  * ADO.Net &#8211; Entity Data Model / Entity Framework 1
  * ASP.Net &#8211; ScriptManager, AJAX Controls
  * ASP.net &#8211; Dynamic Data
  * Silverlight

**.Net 4.0**

  * AppDomain Monitoring
  * Code Contracts
  * Covariance and Contravariance in Generics
  * Memory Mapped Files
  * Portable Class Libraries
  * Threading &#8211; PLINQ (pop quiz: does AsParallel() go at the beginning or end of your LINQ statement? Why?)
  * Threading &#8211; TPL &#8211; System.Threading.Tasks
  * Threading &#8211; Barrier, SpinWait, SpinLock, CancellationTokens, BlockingCollection<T>
  * Threading &#8211; ConcurrentStack, ConcurrentQueue, ConcurrentDictionary, ConcurrentBag
  * Tuples (\*sigh\*)
  * IObservable, Reactive Extensions
  * ADO.Net &#8211; Entity Framework 4
  * MEF
  * ASP.Net &#8211; HTML5 form types
  * ASP.Net &#8211; Bundling and minification
  * ASP.Net &#8211; WebSocket Support
  * ASP.Net &#8211; Asynchronous Requests/Responses
  * ASP.Net &#8211; System.Net.Http
  * ASP.Net &#8211; MVC
  * ASP.Net &#8211; Web API 1
  * ASP.Net &#8211; Extensible Cache &#8211; OutputCacheProvider
  * ASP.Net &#8211; Extensible Request Validation &#8211; RequestValidator
  * ASP.Net &#8211; Resource Monitoring &#8211; appDomainResourceMonitoring

**.Net 4.5, 4.6**

  * AppContext Compatibility Switches
  * .Net Native
  * Threading &#8211; TPL Dataflow
  * System.Net.Http
  * System.Net.WebSockets
  * ADO.Net &#8211; SQLClient Streaming Support
  * ADO.Net &#8211; Async for Connection, DbCommand, DbDataReader, SqlCommand, SqlDataReader, SqlBulkCopy
  * ADO.Net &#8211; AlwaysOn support
  * ADO.Net &#8211; LocalDB
  * ADO.Net &#8211; Entity Framework 5
  * ADO.Net &#8211; Extended Protection
  * ASP.Net &#8211; Web API 2
  * ASP.Net &#8211; OData

Whew, and that doesn't even count all of the extra nuget packages that are out there now…It's hard to remember how we managed without some of this stuff back in the 1.0 and 1.1 days.