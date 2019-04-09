---
title: Observing – Optimize for Ad Hoc Workload server option
author: SQLology ~ Kim Tessereau
type: post
date: 2011-04-09T11:26:00+00:00
ID: 1109
excerpt: |
  Optimize For Ad Hoc Workloads – Observations......
   
  I’ve been watching this one particular server that has been throwing alerts for high physical memory usage from Red Gate’s SQL Monitoring tool.  I was discussing this with a friend of mine, Clayton&hellip;
url: /index.php/datamgmt/dbadmin/observing-optimize-for-ad-hoc/
views:
  - 7823
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <strong style="mso-bidi-font-weight: normal;"><span style="font-family: Calibri; font-size: 11pt;">Optimize For Ad Hoc Workloads – Observations……</span></strong>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <strong style="mso-bidi-font-weight: normal;"><span style="font-family: Calibri; font-size: 11pt;"> </span></strong>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <strong style="mso-bidi-font-weight: normal;"><span style="font-family: &amp;amp; font-size: 8pt;">I’ve been watching this one particular server that has been throwing alerts for high physical memory usage from Red Gate’s SQL Monitoring tool.<span style="mso-spacerun: yes;">  </span>I was discussing this with a friend of mine, Clayton Hoyt and he mentioned that I might want to look at the “Optimize for Ad Hoc Workloads” advanced server configuration options.<span style="mso-spacerun: yes;">  </span>I took him up on his advice and googled for the “Optimize For Ad Hoc Workloads” and “SQLSkills”.<span style="mso-spacerun: yes;">  </span>I found a wonderful article from Kimberly Tripp on just this topic.<span style="mso-spacerun: yes;">  </span>Here is the link.</span></strong>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <strong style="mso-bidi-font-weight: normal;"><span style="font-family: &amp;amp; font-size: 8pt;"> </span></strong>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: Calibri; font-size: 11pt;"> <a href="http://www.sqlskills.com/blogs/kimberly/post/procedure-cache-and-optimizing-for-adhoc-workloads.aspx">http://www.sqlskills.com/blogs/kimberly/post/procedure-cache-and-optimizing-for-adhoc-workloads.aspx</a></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: Calibri; font-size: 11pt;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">The article suggests running the below query to determine the number of plans in cache and the total space that they are taking up.<span style="mso-spacerun: yes;">  </span>So I ran the query on my memory hungry server and the following is what happened……</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">SELECT</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> objtype <span style="color: blue;">AS</span> [CacheType]</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">        </span><span style="color: gray;">,</span> <span style="color: fuchsia;">count_big</span><span style="color: gray;">(*)</span> <span style="color: blue;">AS</span> [Total Plans]</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">        </span><span style="color: gray;">,</span> <span style="color: fuchsia;">sum</span><span style="color: gray;">(</span><span style="color: fuchsia;">cast</span><span style="color: gray;">(</span>size_in_bytes <span style="color: blue;">as</span> <span style="color: blue;">decimal</span><span style="color: gray;">(</span>18<span style="color: gray;">,</span>2<span style="color: gray;">)))/</span>1024<span style="color: gray;">/</span>1024 <span style="color: blue;">AS</span> [Total MBs]</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">        </span><span style="color: gray;">,</span> <span style="color: fuchsia;">avg</span><span style="color: gray;">(</span>usecounts<span style="color: gray;">)</span> <span style="color: blue;">AS</span> [Avg Use Count]</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">        </span><span style="color: gray;">,</span> <span style="color: fuchsia;">sum</span><span style="color: gray;">(</span><span style="color: fuchsia;">cast</span><span style="color: gray;">((</span><span style="color: blue;">CASE</span> <span style="color: blue;">WHEN</span> usecounts <span style="color: gray;">=</span> 1 <span style="color: blue;">THEN</span> size_in_bytes <span style="color: blue;">ELSE</span> 0 <span style="color: blue;">END</span><span style="color: gray;">)</span> <span style="color: blue;">as</span> <span style="color: blue;">decimal</span><span style="color: gray;">(</span>18<span style="color: gray;">,</span>2<span style="color: gray;">)))/</span>1024<span style="color: gray;">/</span>1024 <span style="color: blue;">AS</span> [Total MBs &#8211; USE Count 1]</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"><span style="mso-spacerun: yes;">        </span><span style="color: gray;">,</span> <span style="color: fuchsia;">sum</span><span style="color: gray;">(</span><span style="color: blue;">CASE</span> <span style="color: blue;">WHEN</span> usecounts <span style="color: gray;">=</span> 1 <span style="color: blue;">THEN</span> 1 <span style="color: blue;">ELSE</span> 0 <span style="color: blue;">END</span><span style="color: gray;">)</span> <span style="color: blue;">AS</span> [Total Plans &#8211; USE Count 1]</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">FROM</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: green;">sys</span><span style="color: gray;">.</span><span style="color: green;">dm_exec_cached_plans</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">GROUP</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">BY</span> objtype</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">ORDER</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> <span style="color: blue;">BY</span> [Total MBs &#8211; USE Count 1] <span style="color: blue;">DESC</span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">go</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">Initial Query Results:<span style="mso-spacerun: yes;">  </span>This is what I started with.</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">CacheType   Total Plans   Total MBs     Avg Use Count Total MBs &#8211; USE Count 1  Total Plans </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">&#8212;&#8212;&#8212;&#8211; &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212; &#8212;&#8212;&#8212;&#8212;</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; background: yellow; font-size: 8pt; mso-highlight: yellow;">Adhoc       32750        <span style="mso-spacerun: yes;"> </span>2352.641181       927           947.289619                 9167</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">Proc        842           387.390625        169428        6.882812                   29</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">Prepared    576           69.460937         15176         6.234375                   64</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">Trigger     35            21.867187         6154          0.828125                   3</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">Check       31            0.812500          11453         0.023437                   1</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">View        327           36.875000         8057          0.000000                   0</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">UsrTab      4             0.468750          1473          0.000000                   0</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Then I turned on “Optimized for Ad Hoc Workload” and watched the total number of plans decrease along with the total amount of space depleat.<span style="mso-spacerun: yes;">  </span>I kept monitoring during this time as well using SQL Monitor and the only thing that I noticed was the Buffer Free Pages dropped from 1.7 million to .7 million during this 4 hours I spent watching this server.</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">4 hours had passed, Query Results:<span style="mso-spacerun: yes;">  </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">CacheType<span style="mso-spacerun: yes;">   </span>Total Plans<span style="mso-spacerun: yes;">  </span>Total MBs<span style="mso-spacerun: yes;">    </span>Avg Use Count Total MBs &#8211; USE Count 1<span style="mso-spacerun: yes;">   </span>Total Plans </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;"> </span><span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">&#8212;&#8212;&#8212;-<span style="mso-spacerun: yes;">  </span>&#8212;&#8212;&#8212;&#8211; <span style="mso-spacerun: yes;"> </span>&#8212;&#8212;&#8212;&#8212; &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; background: yellow; font-size: 8pt; mso-no-proof: yes; mso-highlight: yellow;">Adhoc<span style="mso-spacerun: yes;">       </span>42270<span style="mso-spacerun: yes;">     </span><span style="mso-spacerun: yes;">   </span>2264.423034<span style="mso-spacerun: yes;">        </span>733<span style="mso-spacerun: yes;">           </span>659.141784<span style="mso-spacerun: yes;">                </span>11827</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Proc<span style="mso-spacerun: yes;">        </span>833<span style="mso-spacerun: yes;">          </span>320.968750<span style="mso-spacerun: yes;">         </span>173396<span style="mso-spacerun: yes;">        </span>6.882812<span style="mso-spacerun: yes;">                  </span>29</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Prepared<span style="mso-spacerun: yes;">    </span>653<span style="mso-spacerun: yes;">          </span>84.187500<span style="mso-spacerun: yes;">          </span>13557<span style="mso-spacerun: yes;">         </span>2.773437<span style="mso-spacerun: yes;">                  </span>26</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Trigger<span style="mso-spacerun: yes;">     </span>35<span style="mso-spacerun: yes;">           </span>21.453125<span style="mso-spacerun: yes;">          </span>6624<span style="mso-spacerun: yes;">          </span>0.609375<span style="mso-spacerun: yes;">                  </span>2</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Check<span style="mso-spacerun: yes;">       </span>31<span style="mso-spacerun: yes;">           </span>0.812500<span style="mso-spacerun: yes;">           </span>11582<span style="mso-spacerun: yes;">         </span>0.023437<span style="mso-spacerun: yes;">                  </span>1</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">View<span style="mso-spacerun: yes;">        </span>327<span style="mso-spacerun: yes;">          </span>36.875000<span style="mso-spacerun: yes;">          </span>8159<span style="mso-spacerun: yes;">          </span>0.000000<span style="mso-spacerun: yes;">                  </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">UsrTab<span style="mso-spacerun: yes;">      </span>4<span style="mso-spacerun: yes;">            </span>0.468750<span style="mso-spacerun: yes;">           </span>1490<span style="mso-spacerun: yes;">          </span>0.000000<span style="mso-spacerun: yes;">                  </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: Times New Roman; font-size: small;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">Then I decided instead of waiting for the server to slowly correct itself I would go ahead and issue </span><span style="font-family: &amp;amp; color: blue; font-size: 10pt; mso-no-proof: yes;">DBCC</span><span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> FREEPROCCACHE</span><span style="font-family: &amp;amp; font-size: 8pt;"> to clear the procedure cache.</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 10pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">After DBCC command had run, Query Results:</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">CacheType<span style="mso-spacerun: yes;">  </span>Total Plans<span style="mso-spacerun: yes;">   </span>Total MBs<span style="mso-spacerun: yes;">    </span>Avg Use Count Total MBs &#8211; USE Count 1<span style="mso-spacerun: yes;">    </span>Total Plans</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212; &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211; &#8212;&#8212;&#8212;&#8212;</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Proc<span style="mso-spacerun: yes;">       </span>70<span style="mso-spacerun: yes;">            </span>21.031250<span style="mso-spacerun: yes;">          </span>20<span style="mso-spacerun: yes;">            </span>5.804687<span style="mso-spacerun: yes;">                   </span>17</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; background: yellow; font-size: 8pt; mso-no-proof: yes; mso-highlight: yellow;">Adhoc<span style="mso-spacerun: yes;">      </span>215<span style="mso-spacerun: yes;">           </span>5.745986<span style="mso-spacerun: yes;">           </span>3<span style="mso-spacerun: yes;">             </span>0.691299<span style="mso-spacerun: yes;">                   </span>128</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Prepared<span style="mso-spacerun: yes;">   </span>21<span style="mso-spacerun: yes;">            </span>1.625000<span style="mso-spacerun: yes;">           </span>5<span style="mso-spacerun: yes;">             </span>0.523437<span style="mso-spacerun: yes;">                   </span>9</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Trigger<span style="mso-spacerun: yes;">    </span>3<span style="mso-spacerun: yes;">            </span><span style="mso-spacerun: yes;"> </span>1.140625<span style="mso-spacerun: yes;">           </span>6<span style="mso-spacerun: yes;">             </span>0.382812<span style="mso-spacerun: yes;">                   </span>1</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">View<span style="mso-spacerun: yes;">       </span>28<span style="mso-spacerun: yes;">            </span>1.656250<span style="mso-spacerun: yes;">           </span>8<span style="mso-spacerun: yes;">             </span>0.000000<span style="mso-spacerun: yes;">                   </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Buffer Free Pages climbing from .7 million to 1.1 million. I thought I would see Buffer Free Pages go through the roof, but I’m guessing that data cache stepped in and took over what had become available.</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">What I expect to see in the next 12 hours is that the total plans and total MB’s will grow again, but not to the size that it was in the first query.<span style="mso-spacerun: yes;">  </span>I’m going to bed now and I’ll check the server in the morning……</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">And…I’m back… </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">Results 12 hours after cache was cleared:</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">CacheType<span style="mso-spacerun: yes;">  </span>Total Plans<span style="mso-spacerun: yes;">   </span>Total MBs<span style="mso-spacerun: yes;">    </span>Avg Use Count Total MBs &#8211; USE Count 1<span style="mso-spacerun: yes;">       </span>Total Plans</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212; &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211; &#8212;&#8212;&#8212;&#8212;</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; background: yellow; font-size: 8pt; mso-no-proof: yes; mso-highlight: yellow;">Adhoc<span style="mso-spacerun: yes;">      </span>61025<span style="mso-spacerun: yes;">         </span>1456.307067<span style="mso-spacerun: yes;">        </span>8<span style="mso-spacerun: yes;">             </span>523.744567<span style="mso-spacerun: yes;">                    </span>46369</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Proc<span style="mso-spacerun: yes;">       </span>348<span style="mso-spacerun: yes;">           </span>138.406250<span style="mso-spacerun: yes;">         </span>4961<span style="mso-spacerun: yes;">          </span>8.828125<span style="mso-spacerun: yes;">            </span><span style="mso-spacerun: yes;">          </span>40</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Prepared<span style="mso-spacerun: yes;">   </span>615<span style="mso-spacerun: yes;">           </span>76.601562<span style="mso-spacerun: yes;">          </span>279<span style="mso-spacerun: yes;">           </span>3.718750<span style="mso-spacerun: yes;">                      </span>46</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Check<span style="mso-spacerun: yes;">      </span>32<span style="mso-spacerun: yes;">            </span>0.781250<span style="mso-spacerun: yes;">           </span>279<span style="mso-spacerun: yes;">           </span>0.218750<span style="mso-spacerun: yes;">                      </span>11</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">Trigger<span style="mso-spacerun: yes;">    </span>16<span style="mso-spacerun: yes;">            </span>6.398437<span style="mso-spacerun: yes;">           </span>1247<span style="mso-spacerun: yes;">          </span>0.000000<span style="mso-spacerun: yes;">                      </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">View<span style="mso-spacerun: yes;">       </span>322<span style="mso-spacerun: yes;">           </span>30.742187<span style="mso-spacerun: yes;">          </span>299<span style="mso-spacerun: yes;">           </span>0.000000<span style="mso-spacerun: yes;">                      </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt; mso-layout-grid-align: none;">
  <span style="font-family: &amp;amp; font-size: 8pt; mso-no-proof: yes;">UsrTab<span style="mso-spacerun: yes;">     </span>3<span style="mso-spacerun: yes;">             </span>0.250000<span style="mso-spacerun: yes;">           </span>48<span style="mso-spacerun: yes;">            </span>0.000000<span style="mso-spacerun: yes;">                      </span></span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;"> </span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;">Conclusion:<span style="mso-spacerun: yes;">  </span>The total number of Adhoc plans in cache were higher than the original baseline, however, the space being used for Adhoc plans was significantly less with the “Optimize For Ad Hoc Workloads” configuration option set on.<span style="mso-spacerun: yes;">  </span>Because this server houses databases that are accessed with an unusual amount of Adhoc queries I will leave this option on.</span>
</p>

<p class="MsoNormal" style="margin: 0in 0in 0pt;">
  <span style="font-family: &amp;amp; font-size: 8pt;"> </span>
</p>