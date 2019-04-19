---
title: 'SUM (T-SQL Tuesday #016: Aggregate Functions)'
author: Jes Borland
type: post
date: 2011-03-10T11:48:00+00:00
ID: 1076
excerpt: "Thank you to everyone that participated in T-SQL Tuesday #016. I'd consider this a smashing success with 42 posts submitted! Here is the SUM() of all the posts."
url: /index.php/datamgmt/dbprogramming/sum-t-sql-tuesday-016/
views:
  - 10361
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server

---
Thank you to everyone that participated in T-SQL Tuesday #016: Aggregate Functions. I'd consider this a smashing success with 42 posts submitted! 

I'd like to give a shout out to my LessThanDot.com posse for letting me host, and writing excellent posts. I've been on the site for four months now, and I'm loving it here. 

A special thank you goes to the 13 Microsoft MVPs for sharing your expertise with us, especially since many of you were at Summit and had a busy week. MVPs are the cream of the crop – they are experts in the field, and they put their time and energy into the community, as well. Thanks for helping! 

With that...drumroll please...

**Forty-Two Blogs on Aggregation** 

Right out of the gate, Rob Farley, MVP ([B][1] | [T][2]) gave us [The Blocking Nature of Aggregates][3]. He describes the differences between Stream Aggregate and Hash Match with an easy-to-understand example of a card deck. Then, he demonstrates how the Aggregate transformation in SSIS works. 

Mark Broadbent ([B][4] | [T][5]) was a firm believer that you don't need a line of T-SQL to participate! He has aggregated a great list of training media in [T-SQL Tuesday #016 – SELECT MediaType, SUM(Content) FROM #Sources GROUP BY MediaType][6].

Michael J Swart ([B][7] | [T][8]) In [The Aggregate Function PRODUCT()][9], Michael shows us how to build a PRODUCT function, and how he uses it to calculate the value of the dollar after inflation.

Robert Davis, MCM ([B][10] | [T][11]) gives us examples of using aggregates to get information from an XML query plan in [T-SQL Tuesday #16: Using Aggregate Functions in XML][12] .

Nick Haslam ([B][13] | [T][14]) brings us a solid example of how he used PIVOT and aggregation to solve a complex data warehouse load in [T-SQL Tuesday #016 – Taking it to the MAX][15]. 

Jacob Sebastian, MVP ([B][16] | [T][17]) A T-SQL Tuesday first-timer (yay!) brings us [T-SQL Tuesday #016 – Summarizing data using GROUPING SETS()][18], a run-through of GROUPING SETS, with fantastic examples. 

Bradley Ball ([B][19] | [T][20]) provides two scripts, one to get database size statistics, and another for table size statistics, in [T-SQL Tuesday 16: Aggregation][21]. 

Jim McLeod ([B][22] | [T][23]) The burning question on Jim's mind is “Can we reproduce columnstore indexes in SQL Server 2008?” Find out by reading [Can We Reproduce Columnstore Aggregations in SQL 2008?][24] 

Luke Hayler ([B][25] | [T][26]) is a man after my own heart with a Reporting Services 2005 discussion about row groups, column groups and their scope in [T-SQL Tuesday #016 – Aggregations][27]. Excellent examples, Luke! 

Robert Matthew Cook ([B][28] | [T][29]) learns something new when he researches CLR Aggregates for [T-SQL Tuesday #016 Aggregate Functions #tsql2sday][30] . 

Noel McKinney ([B][31] | [T][32]) uses CHECKSUM_AGG to determine changes in checksums in a group, for loading a DW. [T-SQL Tuesday #016 – Aggregation Functions][33] 

Jason Strate, MVP ([B][34] | [T][35]) is improving query performance – significantly! – by aggregating in a correlated subquery. He shows us how in [Aggregating With Correlated Sub-Queries #tsql2sday][36]. 

Ted Krueger, MVP ([B][37] | [T][38]) is deleting data (oh noes!) in [T-SQL Tuesday #016 – COUNT and DELETE duplicates][39]. Thankfully, it's just duplicate data, and never should have been there in the first place. 

Matt Velic ([B][40] | [T][41]) gets a gold star for reinforcing the basics of aggregation with a look back at one of the American film greats, John Hughes, in [T-SQL Tuesday 16: Aggregate the 80s][42] . Also, my _favoritest_ favorite John Hughes movie has to be Uncle Buck. Or maybe The Great Outdoors. Or maybe Weird Science. He was so full awesome, it's hard to pick. 

Denis Gobo, MVP ([B][43] | [T][44]) gives thorough examples of how to chart intraday and end-of-day stock market data. Interesting stuff! [T-SQL Tuesday #016 Grouping Market Data With T-SQL][45] 

Bob Pusateri ([B][46] | [T][47]) has a solution for people that don't have time or money for SSAS in [A Poor Man's Data Warehouse][48]. Also: bacon gets an honorable mention here. 

Pinal Dave ([B][49] | [T][50]) A student's question prompted this quick-but-informative post: [SQL SERVER – Difference between COUNT(DISTINCT) vs COUNT(ALL)][51].

Jen McCown, MVP ([B][52] | [T][53]) serves up some of my favorite T-SQL tricks, like OVER (PARTITION BY) and CTEs in [T-SQL Tuesday #016: Aggregates and Windowing Functions and Ranking! Yum!][54]. 

Andy Lohn ([B][55] | [T][56]) uses CHECKSUM_AGG to verify the data in static tables across different environments in [T-SQL Tuesday #016 – The Power of the CHECKSUM_AGG][57]. 

Gill Rowley ([B][58] | [T][58]) gets mathy and creates a factorial function! [T-SQL Tuesday #16 – Aggregate Functions & Factorials(!)][59] 

Kelly Martinez ([B][60] | [T][61]) shows us how to use PIVOT like a pro! [T-SQL Tuesday #16: PIVOT][62] 

Oscar Zamora ([B][63] | [T][64]) has outstanding examples of OVER, PARTITION BY and RANK for [Windowed Functions empowering analytics [#TSQL2sday]][65]. 

Dev Nambi ([B][66] | [T][67]) walks us through finding MEDIAN, PERCENTILE and COVARIANCE. [T-SQL Tuesday #16: Statistics using CLR Aggregates][68] 

Doug Lane ([B][69] | [T][69]) Another man after my heart with a Reporting Services post, Doug asks if RS datasets are indexed in [T-SQL Tuesday #016: Aggregations in Reporting Services][70]. 

Sean McCown, MVP ([B][71] | [T][53]) I've never heard of PowerShell scripts referred to as “cute”, until Sean wrote [T-SQL Tuesday #016: Get DB Sums With Powershell][72]. 

Claire Willett ([B][73] | [T][74]) [T-SQL Tuesday 016 – When is yesterday, exactly?][75] Claire tells us, “Despite only taking up 20% of the week, Mondays are kind of important.” She then shows us how she used aggregates with dates to pull data for the current business day and previous day. 

George Mastros, MVP ([B][76] | [T][77]) walks us through the **how** of [Understanding SQL Server 2000 Pivot with Aggregates][78] . 

Ricardo Leka ([B][79] | [T][80]) A quick but effective script to show blocked processes: [T-SQL Tuesday #016 – Blocking Processes – #tsql2sday][81]. 

Jason Brimhall ([B][82] | [T][83]) [T-SQL Tuesday #016: Aggregates and Statistics][84]. First, I learned something new: what a “quartile” is. And he used OVER – a favorite! Jason shows us how to use MAX and OVER to properly size production databases. 

Andy Leonard, MVP ([B][85] | [T][86]) proves there are multiple ways to do the same thing with [T-SQL Tuesday: Aggregations in SSIS][87]!

Allen White, MVP ([B][88] | [T][89]) More PowerShell! Allen implements security best practices, POSH and aggregates in one fell swoop: [T-SQL Tuesday #016:Check Your Service Accounts with PowerShell][90]. 

Amit Banerjee ([B][91] | [T][92]) gives us examples of using aggregates for performance monitoring: [T-SQL Tuesday #016 : Aggregate Functions][93] . 

Brad Schulz, MVP ([B][94]) [T-SQL Tuesday #016: AWF-Inspiring Queries][95] Brad, who appears to missing a letter in his last name (ha! Had to, Brad!), walks an order through an identity crisis with the help of window functions. 

Tom Powell ([B][96] | T) Tom shows us the fine points of both functions in [Aggregation – Getting Totals And Subtotals With ROLLUP And CUBE][97]. 

Thomas Rushton ([B][98] | [T][99]) shows us how to check backup sizes. Then he shows us a better way. In the same post! [T-SQL Tuesday #16 – Aggregates and Average Weekly Backup Sizes][100] 

Aaron Bertrand, MVP ([B][101] | [T][102]) String concatenation, six ways: which IS the best? Find out in [T-SQL Tuesday # 16 : This is not the aggregate you're looking for][103]. 

Stuart Ainsworth ([B][104] | [T][105]) gives us a simple, effective method to find the first of an item in [#TSQL2sday: Emulating a FIRST aggregation][106]. 

Ken Johnson ([B][107] | [T][108]) tackles aggregates from a more philosophical perspective with [The Absolute Best][109]. 

Stacia Misner ([B][110] | [T][111]) shows us how to use AGGREGATE in [Retrieving Aggregate Data from Analysis Services for Reports][112]. 

Naomi Nosonovsky ([B][113]) talks about how to perform a PERCENT() function in [T-SQL Tuesday #016 – TOP N Percent for the group][114]. 

Josh Feierman ([B][115] | [T][116]) Tips! Tricks! Performance Monitoring! MDX! Yes, all of this in one place at [Fun With Aggregation – TSQL2SDay #16][117]. 

Steve Jones, MVP ([B][118] | [T][119]) Last, but certainly not least, Steve shares a TRUE STORY of how aggregation is ALMOST equal to aggravation – but he worked around it in the end! [T-SQL Tuesday #016 – Aggregation][120]

**Whew!** 

We made it! Thank you, Adam Machanic, for letting me host. I learned a ton this month, and it was great to get the opportunity to work with so many great bloggers. 

High fives all around!

 [1]: http://sqlblog.com/blogs/rob_farley/default.aspx
 [2]: http://twitter.com/#!/rob_farley
 [3]: http://sqlblog.com/blogs/rob_farley/archive/2011/03/07/the-blocking-nature-of-aggregates.aspx
 [4]: http://tenbulls.co.uk/
 [5]: http://twitter.com/#!/retracement
 [6]: http://tenbulls.co.uk/2011/03/08/t-sql_tuesday_16/
 [7]: http://michaeljswart.com/
 [8]: http://twitter.com/#!/MJSwart
 [9]: http://michaeljswart.com/2011/03/the-aggregate-function-product/
 [10]: http://www.sqlsoldier.com/wp/
 [11]: http://twitter.com/#!/SQLSoldier
 [12]: http://www.sqlsoldier.com/wp/sqlserver/tsqltuesday16usingaggregatefunctionsinxml
 [13]: http://blog.nhaslam.com/
 [14]: http://twitter.com/#!/nhaslam
 [15]: http://blog.nhaslam.com/2011/03/08/t-sql-tuesday-016-taking-it-to-the-max-tsql2sday/
 [16]: http://beyondrelational.com/blogs/jacob/default.aspx
 [17]: http://twitter.com/#!/jacobsebastian
 [18]: http://beyondrelational.com/blogs/jacob/archive/2011/03/08/t-sql-tuesday-016-summarizing-data-using-grouping-sets.aspx
 [19]: http://www.sqlballs.com/
 [20]: http://twitter.com/#!/sqlballs
 [21]: http://www.sqlballs.com/2011/03/t-sql-tuesday-16-aggregation.html
 [22]: http://www.jimmcleod.net/blog/
 [23]: http://twitter.com/#!/Jim_McLeod
 [24]: http://www.jimmcleod.net/blog/index.php/2011/03/08/can-we-reproduce-columnstore-aggregations-in-sql-2008-t-sql-tuesday-016/
 [25]: http://www.lukehayler.com/
 [26]: http://twitter.com/#!/lukehayler
 [27]: http://www.lukehayler.com/2011/03/t-sql-tuesday-016-aggregations/
 [28]: http://www.sqlmashup.com/
 [29]: http://twitter.com/sqlmashup
 [30]: http://www.sqlmashup.com/t-sql-tuesday-016-aggregate-functions
 [31]: http://noelmckinney.com/
 [32]: http://twitter.com/NoelMcKinney
 [33]: http://noelmckinney.com/2011/03/t-sql-tuesday-016-aggregation-functions/
 [34]: http://www.jasonstrate.com/
 [35]: http://www.twitter.com/StrateSQL
 [36]: http://www.jasonstrate.com/2011/03/aggregating-with-correlated-sub-queries-tsql2sday/?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+StrateSql+%28Strate+SQL%29
 [37]: /index.php/All/?disp=authdir&author=68
 [38]: http://twitter.com/onpnt
 [39]: /index.php/DataMgmt/DBAdmin/t-sql-tuesday-016-count-and-delete-duplicates
 [40]: http://mattvelic.com/
 [41]: http://www.twitter.com/mvelic
 [42]: http://mattvelic.com/aggregate-the-80s/
 [43]: /index.php/All/?disp=authdir&author=4
 [44]: http://twitter.com/DenisGobo
 [45]: /index.php/DataMgmt/DataDesign/t-sql-tuesday-016-grouping
 [46]: http://www.bobpusateri.com/
 [47]: http://www.twitter.com/sqlbob
 [48]: http://www.bobpusateri.com/archive/2011/03/a-poor-mans-data-warehouse/
 [49]: http://blog.sqlauthority.com/
 [50]: http://twitter.com/#!/pinaldave
 [51]: http://blog.sqlauthority.com/2011/03/08/sql-server-difference-between-countdistinct-vs-countall/
 [52]: http://www.midnightdba.com/Jen/
 [53]: http://twitter.com/#!/MidnightDBA
 [54]: http://www.midnightdba.com/Jen/2011/03/t-sql-tuesday-016-aggregates-and-windowing-functions-and-ranking-yum/
 [55]: http://www.sqlfeatherandquill.com/
 [56]: http://twitter.com/#!/SQLQuill
 [57]: http://www.sqlfeatherandquill.com/2011/03/08/t-sql-tuesday-016-the-power-of-the-checksum_agg/?utm_source=feedburner&utm_medium=twitter&utm_campaign=Feed%3A+SqlFeatherAndQuill+%28SQL+Feather+and+Quill%29
 [58]: http://gillrowley.wordpress.com/
 [59]: http://gillrowley.wordpress.com/2011/03/08/t-sql-tuesday-16-aggregate-functions-factorials/
 [60]: http://www.zero1design.com/
 [61]: http://twitter.com/#!/greeleygeek
 [62]: http://www.zero1design.com/2011/03/08/t-sql-tuesday-16-pivot/
 [63]: http://ozamora.com/
 [64]: http://twitter.com/#!/ZamoraO
 [65]: http://ozamora.com/2011/03/windowed-functions-empowering-analytics-tsql2sday/?utm_source=feedburner&utm_medium=twitter&utm_campaign=Feed%3A+OscarZamora+%28Oscar+Zamora%29
 [66]: http://devnambi.com/
 [67]: http://twitter.com/#!/DevNambi
 [68]: http://devnambi.com/archive/2011/03/t-sql-tuesday-aggregates/
 [69]: http://www.douglane.net/
 [70]: http://www.douglane.net/2011/03/t-sql-tuesday-016-aggregations-in-reporting-services/
 [71]: http://www.midnightdba.com/DBARant/
 [72]: http://www.midnightdba.com/DBARant/?p=542
 [73]: http://wiki.softartisans.com/display/~clairew
 [74]: http://twitter.com/#!/softartisans
 [75]: http://wiki.softartisans.com/pages/viewpage.action?pageId=14712884
 [76]: /index.php/All/?disp=authdir&author=10
 [77]: http://www.twitter.com/gmmastros
 [78]: /index.php/DataMgmt/DataDesign/understanding-sql-server-2000-pivot
 [79]: http://leka.com.br/
 [80]: http://twitter.com/#!/BigLeka
 [81]: http://leka.com.br/2011/03/08/t-sql-tuesday-016-blocking-processes-tsql2sday/
 [82]: http://jasonbrimhall.info/
 [83]: http://twitter.com/sqlrnnr
 [84]: http://jasonbrimhall.info/2011/03/08/t-sql-tuesday-016-aggregates-and-statistics/
 [85]: http://sqlblog.com/blogs/andy_leonard/default.aspx
 [86]: http://twitter.com/AndyLeonard
 [87]: http://sqlblog.com/blogs/andy_leonard/archive/2011/03/08/t-sql-tuesday-aggregations-in-ssis.aspx
 [88]: http://sqlblog.com/blogs/allen_white/default.aspx
 [89]: http://www.twitter.com/SQLRunr
 [90]: http://sqlblog.com/blogs/allen_white/archive/2011/03/08/t-sql-tuesday-016-check-your-service-accounts-with-powershell.aspx
 [91]: http://troubleshootingsql.com/author/troubleshootingsql/
 [92]: http://twitter.com/banerjeeamit
 [93]: http://troubleshootingsql.com/2011/03/09/t-sql-tuesday-016-aggregate-functions/
 [94]: http://bradsruminations.blogspot.com/
 [95]: http://bradsruminations.blogspot.com/2011/03/t-sql-tuesday-016-awf-inspiring-queries.html
 [96]: http://philergia.wordpress.com/
 [97]: http://philergia.wordpress.com/2011/03/08/aggregation-getting-totals-and-subtotals-with-rollup-and-cube/
 [98]: http://thelonedba.wordpress.com/
 [99]: http://twitter.com/#!/ThomasRushton
 [100]: http://thelonedba.wordpress.com/2011/03/08/t-sql-tuesday-16-aggregates-and-average-weekly-backup-sizes/
 [101]: http://sqlblog.com/blogs/aaron_bertrand/default.aspx
 [102]: http://twitter.com/#!/AaronBertrand
 [103]: http://sqlblog.com/blogs/aaron_bertrand/archive/2011/03/08/t-sql-tuesday-16-this-is-not-the-aggregate-you-re-looking-for.aspx
 [104]: http://codegumbo.com/
 [105]: http://twitter.com/#!/stuarta
 [106]: http://codegumbo.com/index.php/2011/03/08/tsql2sday-emulating-a-first-aggregation/
 [107]: http://kenj.blogspot.com
 [108]: http://twitter.com/#!/datamongrel
 [109]: http://kenj.blogspot.com/2011/03/absolute-best.html
 [110]: http://blog.datainspirations.com/
 [111]: http://twitter.com/#!/StaciaMisner
 [112]: http://blog.datainspirations.com/2011/03/08/retrieving-aggregate-data/
 [113]: /index.php/All/?disp=authdir&author=218
 [114]: /index.php/DataMgmt/DataDesign/t-sql-tuesday-016-top#item_1149
 [115]: http://awanderingmind.com/
 [116]: http://twitter.com/#!/awanderingmind
 [117]: http://awanderingmind.com/2011/03/08/fun-with-aggregation-tsql2sday-16/
 [118]: http://voiceofthedba.wordpress.com/
 [119]: http://www.twitter.com/way0utwest
 [120]: http://voiceofthedba.wordpress.com/2011/03/09/t-sql-tuesday-016-aggregation/