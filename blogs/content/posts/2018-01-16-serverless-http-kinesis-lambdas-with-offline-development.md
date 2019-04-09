---
title: Serverless HTTP + Kinesis Lambdas with Offline Development
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2018-01-16T14:24:20+00:00
ID: 8865
url: /index.php/enterprisedev/cloud/serverless-http-kinesis-lambdas-with-offline-development/
views:
  - 3642
rp4wp_auto_linked:
  - 1
categories:
  - Cloud
tags:
  - FaaS
  - Kinesis
  - lambda
  - node.js
  - Serverless

---
Lately I've been exploring an idea around applying custom, user-defined rules to streams of events. I'm using a combination of technologies, but the core is a FaaS setup that I can run locally that utilizes the [serverless][1] package to deploy [AWS Lambda][2] functions that consume events from a [Kinesis stream][3]. 

I prefer the speed of local development feedback cycles. Getting HTTP Functions running locally was easy with serverless-offline, Kinesis was a lot trickier with more false starts. If you're trying to get local node.js Lambdas running for HTTP and Kinesis, hopefully this will help.

## Spoilers…

By the end of this post, we'll have an HTTP endpoint that can accept POSTed events and publish to a Kinesis stream. We'll have another function pulling events off that stream and processing them. No infrastructure, no polling code, no webserver configurations, less than 40 lines of code, and a simple cli command to deploy: `serverless deploy`

<div id="attachment_8867" style="width: 666px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/01/OfflineHttpAndKinesisOutput.png" alt="cUrl => HTTP Function => Kinesis Stream => Kinesis Function" width="656" height="161" class="size-full wp-image-8867" srcset="/wp-content/uploads/2018/01/OfflineHttpAndKinesisOutput.png 656w, /wp-content/uploads/2018/01/OfflineHttpAndKinesisOutput-300x74.png 300w" sizes="(max-width: 656px) 100vw, 656px" />
  
  <p class="wp-caption-text">
    cUrl => HTTP Function => Kinesis Stream => Kinesis Function
  </p>
</div>

That's pretty standard FaaS stuff, but I can't stand the latency of deploying to test changes, so I'll add in some plugins and a little hackery to have all of that running in realtime, locally, with automatic syncing of changes as they're made (because I don't even like restarting services to try changes).

<div id="attachment_8890" style="width: 699px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/01/OfflineHttpAndKinesisOutputUpdated.png" alt="Updated Functions, Running Locally, No Restarts (SUPER!!!)" width="689" height="157" class="size-full wp-image-8890" srcset="/wp-content/uploads/2018/01/OfflineHttpAndKinesisOutputUpdated.png 689w, /wp-content/uploads/2018/01/OfflineHttpAndKinesisOutputUpdated-300x68.png 300w" sizes="(max-width: 689px) 100vw, 689px" />
  
  <p class="wp-caption-text">
    Updated Functions, Running Locally, No Restarts (SUPER!!!)
  </p>
</div>

Here comes the details…

## Laying the Groundwork: An HTTP Function

First I need some events flowing into a log. I'm choosing to use an HTTP endpoint to start getting setup for more complex functions and so I don't get distracted building something to produce real events.
  
I'm using [serverless]() to wire up and deploy my functions. Two of the benefits of this is not wiring my deployment to git commits and being able to simulate Lambda and run my function logic locally.

### 1. Set Up Serverless + AWS

First up, I followed the Getting Started directions to get serverless setup with AWS credentials: 

  * [AWS &#8211; Installation][4]
  * [AWS &#8211; Credentials][5]

Next, I define a very simple Http handler for my function:

**OfflineHttp/functions/eventsHttp.js** [(github)][6]

```javascript
module.exports.handler = (event, context, callback) => { 
	console.log("You POSTed an event!");
	console.log(event.body);
	callback(null, { statusCode: 200, body: "Success" });
};
```
Add the serverless-offline package: `npm install serverless-offline --save-dev`

And then configure serverless:

**OfflineHttp/serverless.yaml** [(github)][7]

```text
# the name of my service - used during deployment
service: example-offline-http

# serverless plugins to use with serverless
plugins:
  - serverless-offline

# set a default stage in case I don’t provide one
custom:
  defaults:
    stage: dev

# some details about the environment and language I'm going to use
provider:
  name: aws
  runtime: nodejs6.10
  region: us-east-1

# the function entry point and defining the events to trigger it
functions:
  events:
    handler: functions/eventsHttp.handler
    events:
      - http:
          method: POST
          path: events
          cors: true
```
Now to test it, we will start serverless and then post some events with curl:

Run `serverless offline`

```text
Serverless: Starting Offline: dev/us-east-1.
Serverless: Routes for events:
Serverless: POST /events
Serverless: Offline listening on http://localhost:3000
```
And now we run `curl -d "{'key1':'value1', 'key2':'value2'}" -H "Content-Type: application/json" -X POST http://localhost:3000/events` 
  
And serverless processes the response, and sends back a 200 Success:

```text
Serverless: POST /events (λ: events)
You POSTed an event!
{'key1':'value1', 'key2':'value2'}
Serverless: [200] {"statusCode":200,"body":"Success"}
```
To deploy this to real AWS, we run: `serverless deploy`

Serverless creates the stack for us, creates a CloudFormation file to deploy the Lambda, performs the update, then returns information about the environment and the new endpoint it created. Replace the `localhost` entry above with that new endpoint and try it out!

We can continue to make changes and redeploy them, later deploys update that same stack. When we're done, we can leave it running or remove it with `serverless remove`.

### 2. Adding in Kinesis

Adding a function to consume events from Kinesis is just as easy as the Http example above, but making this work locally is where it gets tricky. I looked at a few different approaches and eventually had to bundle something up on my own (I may go back to the drawing board and write a serverless-kinesalite plugin soon).

_Note: From here out I'll focus on running offline, you can then layer in the configurations for deploying to real Kinesis (lots of good posts on that)._

To simulate kinesis locally, we can use the [kinesalite][8] package. So let's start by adding a script to bootstrap a Kinesis stream locally that we can publish to from our Http function above. 

First, we can use a YAML file to define some environment-specific environment variables:

**OfflineHttpAndKinesis/config/env.yml** [(github)][9]

```text
---
offline: 
  KINESIS_HOST: 'localhost'
  KINESIS_PORT: 4567
  KINESIS_REGION: 'us-east-1'
  KINESIS_STREAM_NAME_EVENTS: 'offline-events'
```
Because I don't have a Kinesis plugin for serverless, we're going to be running the bootstrap.js script prior to starting `serverless offline`, so we can use these environment variables across that bootstrap file, the Kinesis Lambda watcher, and in server less for the HTTP functions to publish to.

This is easy to make available in the serverless config, using a coalesce to look at the set for a stage passed in from the command line or falling back to a default value:

**OfflineHttpAndKinesis/serverless.yml** [(github)][10]

```text
…

custom:
  defaults:
    stage: dev

provider:
  name: aws
  runtime: nodejs6.10
  region: us-east-1
  environment: ${file(./config/env.yml):${opt:stage, self:custom.defaults.stage}}

…
```
As we add the bootstrap and kinesis runner, we'll pull these environment variables in as well.

Now we'll need to add some additional npm packages:

  * `npm install aws-sdk --save` &#8211; For creating the Kinesis stream and publishing in the HTTP Function
  * `npm install js-yaml --save-dev` &#8211; For parsing the env.yml file for the bootstrap + runner
  * `npm install concurrently --save-dev` &#8211; To make a clean npm task to start everything
  * `npm install -g kinesalite` &#8211; Kinesis emulation

We're ready to add in local kinesis now.

### 3. Bootstrap the Stream

Before we run functions to consume events from the stream, we need to make sure we have started kinesalite and run the bootstrap to ensure the stream is created. We can use the AWS SDK with the env.yml values to get everything ready.

**OfflineHttpAndKinesis/utility/bootstrap.js** [(github)][11]

```javascript
const AWS = require('aws-sdk');
const envFromYaml = require('./envFromYaml');

envFromYaml.config('./config/env.yml','offline');

const kinesis = new AWS.Kinesis({
    endpoint: `${process.env.KINESIS_HOST}:${process.env.KINESIS_PORT}`,
    region: process.env.KINESIS_REGION,
    apiVersion: '2013-12-02',
    sslEnabled: false
});

ensureStreamExists(kinesis, process.env.KINESIS_STREAM_NAME_EVENTS);

function ensureStreamExists(kinesis, streamName){
    var req = kinesis.createStream({ ShardCount: 1, StreamName: streamName });
    req.send(function (err, data) { 
        if (err) {
            if (err.code === 'ResourceInUseException') {
                // Stream already exists, so no problem
                console.log(`Bootstrap: Success - Kinesis stream '${streamName}' already exists`);
                process.exit(0);
            }
            else {
                console.log(`Bootstrap: Failed - Create Kinesis stream '${streamName}' failed with error ${err.stack}`);
                process.exit(1);
            }
        }
        else { 
            console.log(`Bootstrap: Success - Kinesis stream '${streamName}' created`);
            process.exit(0);
        }
    });
}
```
At the top, we're pulling the environment variables from the YAML file and pushing it into process.env. We then use the variables to define our Kinesis connection and call the homegrown `ensureStreamExists` function for each Stream we need to create it if it doesn't already exist. My other application has multiple streams, so I have additional KINESIS\_STREAM\_NAME_* environment variables and call `ensureStreamExists` for each one. 

Now to try it out, we can use two console sessions to run kinesalite: `kinesalite` and then the bootstrap: `node utility/bootstrap.js`, and we'll know it's working when we see this:

```text
Bootstrap: Success - Kinesis stream 'offline-events' created
```
We'll come back and use concurrently to bundle this into a single, easy npm task.

### 4. Add a Kinesis Function

Adding a new function to consume Kinesis events is as easy as the HTTP function.

Here is the function we're adding:

**OfflineHttpAndKinesis/functions/eventsStream.js** [(github)][12]

```javascript
'use strict';

module.exports.handler = (event, context, callback) => {
    event.Records.forEach((record) => {
        const payload = new Buffer(record.kinesis.data, 'base64').toString('ascii');
        console.log("Received an event: " + payload);
    });
    callback(null, `Successfully processed ${event.Records.length} event.`);
};
```
This function accepts a batch of Kinesis events, loops through each to read the contents, and then console.log's that event content. In later posts, I'll go into more complex cases or you can look at the [serverless/examples][13] for ideas.

Now for the hard part.

I've adapted a runner I found online for this part, which is available on [github][14]. I've fixed some bugs and made changes for wider function support, but I'm still leaning toward writing a serverless plugin directly instead.

`npm install git+https://github.com/tarwn/local-kinesis-lambda-runner.git#package --save-dev` will install the version I'm using.

With the package above and the following script, we can bind functions locally to the Kinesis streams with minimal double-typing (there is still a little).

**OfflineHttpAndKinesis/utility/runOfflineStreamHandlers.js** [(github)][15]

```javascript
const AWS = require('aws-sdk');
const run = require('@rabblerouser/local-kinesis-lambda-runner');
const envFromYaml = require('./envFromYaml');

envFromYaml.config('./config/env.yml','offline');

const kinesis = new AWS.Kinesis({
    endpoint: `${process.env.KINESIS_HOST}:${process.env.KINESIS_PORT}`,
    region: process.env.KINESIS_REGION,
    apiVersion: '2013-12-02',
    sslEnabled: false
});
const functions = [
    { funName: 'EventProcessor', handlerPath: '../functions/eventsStream', handlerName: 'handler', kinesisStreamName: process.env.KINESIS_STREAM_NAME_EVENTS }
];
initialize(functions);

// … more code …
```
The key part of this file to edit is the names in the array of functions. `funName` is a human-readable name you will see in the console output, `handlerPath` is the relative path to the file the handler is in, `handlerName` is the module. Everything else is read from the environment variables that are pulled in from the env.yml file and you can add as few or as many entries to the functions array as you like. This file also invalidates the require() cache while it's running, so you can make changes to functions and fire new events and the new code will be picked up immediately without restarting anything.

We have one more step before we can bring it all together: publishing events.

## Publishing Events to Kinesis

We have all the pieces we need to publish events, an HTTP function, the AWS SDK, shared environment variables to identify the streams, so let's connect all the dots.

Updating the function to publish the events looks like this:

**OfflineHttpAndKinesis/functions/eventsHttp.js** [(github)][16]

```javascript
'use strict';

var AWS = require('aws-sdk');
    
const kinesis = new AWS.Kinesis({
	endpoint: `${process.env.KINESIS_HOST}:${process.env.KINESIS_PORT}`,
	region: process.env.KINESIS_REGION,
	apiVersion: '2013-12-02',
	sslEnabled: false
});

module.exports.handler = (event, context, callback) => { 
	console.log("You POSTed an event!");

	var putReq = kinesis.putRecord({
		Data: JSON.stringify(event.body),
		PartitionKey: '0',
		StreamName: process.env.KINESIS_STREAM_NAME_EVENTS
	}, function (err, data) { 
		if (err) {
			callback(err, { statusCode: 500, body: "Error writing to kinesis" } );
		}
		else { 
			callback(null, { statusCode: 200, body: "Ok" } );
		}
	});
};
```
Here we're initializing the Kinesis connection, then inside the HTTP handler we use `putRecord` to publish the POSTed event to our Kinesis stream.

The first thing to note is that we initialize the kinesis object outside the function call in both functions. AWS will re-use functions if a lot of events are coming in, so this ensures we don't lose time on every single call recreating that necessary resource.

The second to note is that I've hard-coded the PartitionKey. Kinesalite only handles a single partition, but for a real system you would want to replace this with some logic to calculate a PartitionKey, depending on whether you wanted events to consistently be placed in the same partition (maybe all events for a client are always on the same partition) or opt for something more random to level-load the partitions. Lambda will run a single function at a time against each partition, preserving the order of whatever processing you're doing (only to the partition, though, not the whole stream).

So, we have a Kinesis emulator, a script to bootstrap the stream, an Http endpoint that pushes an event to kinesis, a function that consumes those events, and a script to run the kinesis function as if it were running under serverless. Time to put it all together!

## Let's Go!

Instead of firing up 4 consoles and running things in the perfect order, we'll use `concurrently` to create a single npm task that will make our lives much easier (and more colorful). Here is the npm task:

**OfflineHttpAndKinesis/package.json** [(github)][17]

```javascript
"scripts": {
    "offline": "concurrently --names \"KNSL,BOOT,HTTP,STRM\" -c \"bgGreen.bold,bgGreen.bold,bgBlue.bold,bgMagenta.bold\" --kill-others-on-fail \"kinesalite\" \"node utility/bootstrap.js\" \"serverless offline start --stage offline\" \"node utility/runOfflineStreamHandlers.js\""
  },
```
This sets up 4 names to show on console output, each with a different color, and each with a different command. We're going to run kinesalite, the bootstrap, serverless offline, and the offline handler script. If we Ctrl+C, it will force a failure and exit them all.

To start, we just need to type `npm run offline`.

Initial output:
  


<div id="attachment_8868" style="width: 796px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/01/OfflineHttpAndKinesisStartupOutput.png" alt="Offline Lambda and Kinesis Output" width="786" height="391" class="size-full wp-image-8868" srcset="/wp-content/uploads/2018/01/OfflineHttpAndKinesisStartupOutput.png 786w, /wp-content/uploads/2018/01/OfflineHttpAndKinesisStartupOutput-300x149.png 300w, /wp-content/uploads/2018/01/OfflineHttpAndKinesisStartupOutput-768x382.png 768w" sizes="(max-width: 786px) 100vw, 786px" />
  
  <p class="wp-caption-text">
    Offline Lambda and Kinesis Output
  </p>
</div>

I've been ignoring the initial error that gets kicked out by the stream handler script, it doesn't cause any issues at this time. (It's adding to the &#8216;maybe I'll write a plugin' balance, though).

And after running the curl command to add an event:
  


<div id="attachment_8867" style="width: 666px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2018/01/OfflineHttpAndKinesisOutput.png" alt="cUrl => HTTP Function => Kinesis Stream => Kinesis Function" width="656" height="161" class="size-full wp-image-8867" srcset="/wp-content/uploads/2018/01/OfflineHttpAndKinesisOutput.png 656w, /wp-content/uploads/2018/01/OfflineHttpAndKinesisOutput-300x74.png 300w" sizes="(max-width: 656px) 100vw, 656px" />
  
  <p class="wp-caption-text">
    cUrl => HTTP Function => Kinesis Stream => Kinesis Function
  </p>
</div>

So we have two pretty simple bits of code and we're consuming events (with the first 1,000,000 runs/month being free). There were some complicated bits, but that was all for offline emulation. 

Hopefully this was helpful, next up I'll dive into the project that sparked this work and also adds in some DynamoDB and running an entire Express instance in a single HTTP Function (not recommend, by the way).

 [1]: https://serverless.com/
 [2]: https://aws.amazon.com/lambda/
 [3]: https://aws.amazon.com/kinesis/
 [4]: https://serverless.com/framework/docs/providers/aws/guide/installation/ "Serverless.com: AWS - Installation"
 [5]: https://serverless.com/framework/docs/providers/aws/guide/credentials/ "Serverless.com: AWS - Credentials"
 [6]: https://github.com/tarwn/serverless-examples/blob/master/OfflineHttp/functions/eventsHttp.js
 [7]: https://github.com/tarwn/serverless-examples/tree/master/OfflineHttp/serverless.yaml
 [8]: https://github.com/mhart/kinesalite
 [9]: https://github.com/tarwn/serverless-examples/blob/master/OfflineHttpAndKinesis/config/env.yml
 [10]: https://github.com/tarwn/serverless-examples/blob/master/OfflineHttpAndKinesis/serverless.yml
 [11]: https://github.com/tarwn/serverless-examples/blob/master/OfflineHttpAndKinesis/utility/bootstrap.js
 [12]: https://github.com/tarwn/serverless-examples/blob/master/OfflineHttpAndKinesis/functions/eventsStream.js
 [13]: https://github.com/serverless/examples "github: serverless/examples"
 [14]: https://github.com/tarwn/local-kinesis-lambda-runner "github: tarwn/local-kinesis-lambda-runner"
 [15]: https://github.com/tarwn/serverless-examples/blob/master/OfflineHttpAndKinesis/utility/runOfflineStreamHandlers.js
 [16]: https://github.com/tarwn/serverless-examples/blob/master/OfflineHttpAndKinesis/functions/eventsHttp.js
 [17]: https://github.com/tarwn/serverless-examples/blob/master/OfflineHttpAndKinesis/package.json