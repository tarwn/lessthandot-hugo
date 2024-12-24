---
title: Streaming Alerts using AWS Lambda, Kinesis, and DynamoDB
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2018-01-23T18:06:36+00:00
ID: 8898
url: /index.php/enterprisedev/cloud/streaming-alerts-using-aws-lambda-kinesis-and-dynamodb/
featured_image: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/Streaming.png
views:
  - 1713
rp4wp_auto_linked:
  - 1
categories:
  - Cloud
tags:
  - aws
  - DynamoDB
  - Kinesis
  - lambda
  - node.js
  - Serverless

---
Recently I was talking to some friends about alerting on complex streaming data. Between some past event processing work we had done and earlier experience aggregating real-time manufacturing data, I was thinking a lot about projecting and evaluating results as we go versus querying the for results on some frequency. It seemed like a realistic approach and I couldn't stop thinking about it, so I decided to prototype the pieces that I was least familiar with. 

The idea is based on one that is used frequently in CQRS and I'm far from the first to have it, professional tools like [Apache Spark][1] and [Amazon Kinesis Data Analytics][2] support this idea of a continuous query over time, but part of the driver was also the desire to support user-defined rules in a multi-tenant environment and see how that would impact my design thinking as I prototyped.

<div style="border: 1px solid #eeeeee; border-left: 8px solid #eeeeee; margin: .5em 0; padding: 1em;">
  <b>Examples:</b>

<ul>
<li>
  <a href="https://docs.aws.amazon.com/kinesisanalytics/latest/dev/app-simple-alerts.html">Kinesis Analytics: Stock Prices Changes > 1%</a>
</li>
<li>
  <a href="https://gallery.cortanaintelligence.com/Tutorial/Using-Azure-Stream-Analytics-for-Real-time-fraud-detection-2">Azure Analytics: Detecting SIM fraud</a>
</li></div> 

<p>
  It pushed me to learn some new (to me) things, so I thought I would share what I did and perhaps some of this will be helpful or interesting to others as well.
</p>

<h2>
  Just Enough Prototyping w/ Lambdas, Kinesis, and DynamoDB
</h2>

<p>
  So I set out to build just enough of a prototype to answer the unknowns, creating a small serverless application that receives a stream of events for 2 clients that each have 5 machines that change state simultaneously (warming up, running, shut down) so I could experiment with rules with different criteria and get a feel for how complex this would be.
</p>

<div id="attachment_8900" style="width: 718px" class="wp-caption aligncenter">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/AnalyticsOverview.png" alt="Analytics Overview" width="708" height="608" class="size-full wp-image-8900" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/AnalyticsOverview.png 708w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/AnalyticsOverview-300x258.png 300w" sizes="(max-width: 708px) 100vw, 708px" />
  
  <p class="wp-caption-text">
    Analytics Overview
  </p>
</div>

<p>
  I would use a fake front-end to manage some fake "machines" for 2 "clients", that would change state occasionally and publish those events. The fake site would push those events into the beginning of my real prototype, an <a href="https://aws.amazon.com/kinesis/">Amazon Kinesis</a> stream for events. An <a href="https://aws.amazon.com/lambda/">AWS Lambda</a> function, the "Rule Processor", would be triggered by a batch of events on the stream. It would load the appropriate rules out of <A href="https://aws.amazon.com/dynamodb/">DynamoDB</a> for each client, evaluate which rules each event applies to, then combine that with previously cached events from DynamoDB and evaluate whether the rule is satisfied. If so, an alert would be published to the Kinesis Alerts Stream. Further downstream, an "Alerts Processor" function would receive those alerts and, in a real system, decide what types of notifications were necessary.
</p>

<h2>
  Rules and DynamoDB Store
</h2>

<p>
  The buckets of data in AWS can be thought of as query results that I am constantly updating for each event that comes in.
</p>

<p>
  <strong>Example Event:</strong>
</p>

<pre lang="javascript">
{ 
    "eventType": "machineStatusChange", 
    "state": "stopped", 
    "clientId": "client-01", 
    "machineId": "machine-01"
}
</pre>

<p>
  <strong>Example Rule:</strong>
</p>
    
<pre lang="javascript">
// alert when all machines enter stopping state
new Rule({
    ruleId: 'hardcoded-rule-02',
    clientId: "client-01",
    where: [{ 'property': 'machineId', 'equals': '*' }],
    partitionBy: ['machineId'],
    evaluate: {
        type: 'all',
        having: {
            calc: 'latest',
            property: 'state',
            equals: 'stopped'
        }
    }
})
</pre>

<p>
  I wouldn't expect an end user to enter a rule like this and would probably rework the structure if this was intended to be a real system, but it is good enough to let me resolve the bigger unknowns around processing events and partitioning data.
</p>

<h2>
  Implementation
</h2>

<p>
  I used the <a href="https://serverless.com/">serverless</a> platform to define and build these functions, running locally with a combination of <a href="https://github.com/dherault/serverless-offline">serverless-offline</a>, <a href="https://github.com/mhart/kinesalite">kinesalite</a>, <a href="https://github.com/mhart/dynalite">dynalite</a> and some tooling I cooked up to run kinesis functions locally (see <a href="/index.php/enterprisedev/cloud/serverless-http-kinesis-lambdas-with-offline-development/" title="Prior Post: Serverless HTTP + Kinesis Lambdas with Offline Development">Serverless HTTP + Kinesis Lambdas with Offline Development</a>). I also used <a href="https://wallabyjs.com/">wallaby.js</a> heavily to give me fast, inline feedback with unit tests inside larger iterations of manual testing with the <a href="https://expressjs.com/">express</a> front-end I bolted into another HTTP Lambda.
</p>

<p>
  The code can be found here: <a href="https://github.com/tarwn/serverless-eventing-analytics">github: tarwn/serverless-eventing-analytics</a>
</p>

<p>
  The core of the system is the "Rule Processor":
</p>

<div id="attachment_8901" style="width: 463px" class="wp-caption aligncenter">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/RuleProcessor.png" alt="Rule Processor Logic" width="453" height="624" class="size-full wp-image-8901" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/RuleProcessor.png 453w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/RuleProcessor-218x300.png 218w" sizes="(max-width: 453px) 100vw, 453px" />
  
  <p class="wp-caption-text">
    Rule Processor Logic
  </p>
</div>

<p>
  The Rule Processor is triggered by a batch of events from Kinesis:
</p>

<ul>
  <li>
    Extract the event data from the batch
  </li>
  <li>
    Group them by Client
  </li>
  <li>
    For Each Client's group of events: <ul>
      <li>
        Load the client's rules
      </li>
      <li>
        Create result buckets for each rule and add applicable events to it
      </li>
      <li>
        Load past result data from DynamoDB and append local result data
      </li>
      <li>
        Evaluate rule conditions and output any alerts
      </li>
    </ul>
  </li>
  
  <li>
    Publish the collected alerts to the Alerts Kinesis Stream
  </li>
</ul>

<p>
  Each of the "Event x Rule" result buckets can be thought of as the results of a query we're running continuously as events flow in (which is about the time I realized I was duplicating work a bunch of much smarter folks had put into stream analytics packages above). So if I have 6 rules that care about machine events and 1 event comes in for "machineStatusChange" on "machine-01", then I'll add that event to all 6 buckets individually (storage is cheap).
</p>

<p>
  The rules are responsible for generating their own unique result name, which by convention is {clientId}/rules/{rule id}/{where clause specific bits}. You can see more in <a href="https://github.com/tarwn/serverless-eventing-analytics/blob/master/functions/ruleProcessor/lib/rule.js">/functions/ruleProcessor/lib/rule.js</a> .
</p>

<p>
  The function code is a slightly messier version of the steps above:
</p>

<p>
  /functions/ruleProcessor/handler.js (<a href="https://github.com/tarwn/serverless-eventing-analytics/blob/master/functions/ruleProcessor/handler.js">github</a>)
</p>

<pre lang="javascript">
module.exports.streamProcessor = (kinesisEvent, context, callback) => {
    const events = helper.extractEventsFromKinesisEvent(kinesisEvent);
    const eventGroups = helper.groupEventsByClient(events);

    Promise.all(eventGroups.map((group) => evaluateAndStore(group)))
    // ...
    .then((nestedAlerts) => {
        var alerts = flatten(nestedAlerts);

        const publishAlerts = alerts.map((alert) => {
            return publishAlert(alert);
        });
        return Promise.all(publishAlerts);
    })
    // ...
};

function evaluateAndStore(eventGroup){
    const clientRules = rules.get(eventGroup.clientId);
    const localResults = helper.applyRules(clientRules, eventGroup);

    // open per-rule results to merge with stored results and evaluate for alert
    return Promise.all(localResults.map((result) => { 
        const appliedRule = clientRules.find((cr) => cr.ruleId = result.ruleId);
        return results.applyLocalResultToStoredResult(result, appliedRule)
            .then((completeResult) => {
                if (appliedRule.meetsConditionsFor(completeResult)) { 
                    console.log(`ALERTED for ${completeResult.uniqueResultKey}`);
                    return appliedRule.getAlertFor(completeResult);
                }
                else{
                    console.log(`No alerts for ${completeResult.uniqueResultKey}`);
                    return null;
                }
            });
    }))
    .then((nestedAlerts) => flatten(nestedAlerts));
}
</pre>
    
    <p>
      When events start flowing through, we can see data getting loaded and applied for each of those rules. Client-02 alerts when any machine enters a stopped state (as opposed to all machines, like client-01 above), so we can see
    </p>
    
    <div id="attachment_8902" style="width: 837px" class="wp-caption aligncenter">
      <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/RuleProcessorOutput.png" alt="Rule Processor Output" width="827" height="255" class="size-full wp-image-8902" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/RuleProcessorOutput.png 827w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/RuleProcessorOutput-300x93.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2018/01/RuleProcessorOutput-768x237.png 768w" sizes="(max-width: 827px) 100vw, 827px" />
      
      <p class="wp-caption-text">
        Rule Processor Output
      </p>
    </div>
    
    <p>
      You can run this yourself by cloning the <a href="https://github.com/tarwn/serverless-eventing-analytics">github repo</a>, running <code>npm run offline</code>, and then opening your browser to <a href="http://localhost:3000">http://localhost:3000</a>.
    </p>
    
    <h2>
      Next Steps
    </h2>
    
    <p>
      This proved out the big question I had, and maybe helped some folks with running Kinesis functions locally (previous post), so I'm calling it a win. If I were building this for production, the next steps would be to continue making the rules richer, figure out how to age data out of the result set I'm storing in DynamoDB, and start building an interface that would allow you to create and store a rule on your own. Another big question would be whether I also need some sort of regular event (second? Minute) to re-evaluate rules for data to fall off or for queries that want to rely on the continued state of a value without needing new events to come in and restart it (the machine is still running, still running, still running...).
    </p>

 [1]: https://spark.apache.org/
 [2]: https://aws.amazon.com/kinesis/data-analytics/