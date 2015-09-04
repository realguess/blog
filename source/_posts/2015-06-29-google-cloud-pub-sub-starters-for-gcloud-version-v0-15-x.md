title: Google Cloud Pub/Sub Starters for gcloud Version v0.15.x
date: 2015-06-29 12:00:00
categories: [Cloud]
tags: [starter, template, google-cloud-pubsub, node, topic, subscription, gcloud]
---

[Google Cloud Pub/Sub] starters/examples/references for [Node.js] gcloud version v0.15.x.

Usage:

```sh
$ PROJECT=my-project KEYFILENAME=/path/to/keyfile.json node file.sh
```

[Google Cloud Pub/Sub]: https://cloud.google.com/pubsub/docs
[Node.js]: https://nodejs.org/api/
[gcloud]: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub


<!-- more -->


**Create Topic**

```js
// Create Topic
// ============
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub?method=createTopic
//
// Create a new Google Cloud Pub/Sub topic and create the same topic again.
//
// New topic format: `projects/:project/topics/:topic`

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var topicName = 'example';


// Main
// ----
//
// Create a new topic.
//
// Think of the callback parameter `apiResponse` as the JSON response after
// making a HTTP request to an API endpoint.
pubsub.createTopic(topicName, function callback(err, topic, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  console.log('\n> topic:\n');
  console.log(topic);
  console.log('\n> apiResponse:\n');
  console.log(apiResponse);


  // Create the same topic again.
  pubsub.createTopic(topicName, callback);
});
```

Output:

```sh
> topic:

{ makeReq_: [Function],
  name: 'projects/my-project/topics/example',
  projectId: 'my-project',
  pubsub:
   { makeAuthorizedRequest_: { [Function: makeAuthorizedRequest] getCredentials: [Function] },
     projectId: 'my-project',
     projectName: 'projects/my-project' },
  unformattedName: 'example' }

> apiResponse:

{ name: 'projects/my-project/topics/example' }

> err:

{ [Error: Resource already exists in the project (resource=example).]
  errors: undefined,
  code: 409,
  message: 'Resource already exists in the project (resource=example).',
  response: undefined }
```

This is one way to determine the existence of a topic. If the topic (resource) already exists, an error will be thrown with error code `409`, this is likely originated from [HTTP status code 409][409]. Therefore, we can replace the error handling routine with:

```js
if (err.code === 409) {
  // Retrieve the topic
} else {
  // Handle other errors
  throw err;
}
```

[409]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html


**Retrieve Topic**

```js
// Retrieve Topic
// ==============
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub?method=topic
//
// There is no need to retrieve a Google Cloud Pub/Sub topic. A topic can be
// initiated locally without contacting the remote server, because the topic
// name is unique. Hence, to retrieve (initiate) a Google Cloud Pub/Sub topic
// with the following two situations:
//
// 1. An existing topic
// 2. Not yet existed topic (If `autoCreate` option is provided, then the
//    topic will be created automatically on the next request involving the
//    remote.)

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var topicName = 'example';
var options = {
  // Automatically create the topic if it does not exist.
  autoCreate: true
};


// Main
// ----
//
// Create a topic object.
var topic = pubsub.topic(topicName, options);

console.log(topic);
```

Output:

```sh
{ makeReq_: [Function],
  name: 'projects/my-project/topics/example',
  projectId: 'my-project',
  pubsub:
   { makeAuthorizedRequest_: { [Function: makeAuthorizedRequest] getCredentials: [Function] },
     projectId: 'my-project',
     projectName: 'projects/my-project' },
  unformattedName: 'example',
  origMakeReq_: [Function] }
```


**Retrieve Topics**

```js
// Retrieve Topics
// ===============
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub?method=getTopics
//
// Retrieve a list of topics. To retrieve all topics, page through.

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var options = {
  pageToken: undefined,
  // TODO: By default, max number of results will be returned. But what is the max?
  pageSize: 1
};


// Main
// ----
//
// Retrieve all topics. Parameter `options` is optional.
pubsub.getTopics(options, function callback(err, topics, next, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  console.log('\n> topics:\n');
  console.log(topics);

  console.log('\n> next:\n');
  console.log(next);

  console.log('\n> apiResponse:\n');
  console.log(apiResponse);

  // If exists, parameter `next` contains the entire query options to retrieve
  // next set of topics, not just the paging token.
  if (next) {
    pubsub.getTopics(next, callback);
  }
});
```

Output:

```sh
> topics:

[ { makeReq_: [Function],
    name: 'projects/my-project/topics/example-1',
    projectId: 'my-project',
    pubsub:
     { makeAuthorizedRequest_: [Object],
       projectId: 'my-project',
       projectName: 'projects/my-project' },
    unformattedName: 'projects/my-project/topics/example-1' } ]

> next:

{ pageToken: 'UhlcRDIhNGhoDBs', pageSize: 1 }

> apiResponse:

{ topics: [ { name: 'projects/my-project/topics/example-1' } ],
  nextPageToken: 'UhlcRDIhNGhoDBs' }

> topics:

[ { makeReq_: [Function],
    name: 'projects/my-project/topics/example-2',
    projectId: 'my-project',
    pubsub:
     { makeAuthorizedRequest_: [Object],
       projectId: 'my-project',
       projectName: 'projects/my-project' },
    unformattedName: 'projects/my-project/topics/example-2' } ]

> next:

null

> apiResponse:

{ topics: [ { name: 'projects/my-project/topics/example-2' } ] }
```


**Remove Topic**

```js
// Remove Topic
// ============
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub/topic?method=delete
//
// Remove a topic and subsequently delete all subscriptions of this topic.

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var topicName = 'example';


// Main
// ----
//
// Delete a topic that does not exist.
var topic = pubsub.topic(topicName);
topic.delete(function (err, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  // Successful response will be an empty object.
  console.log('\napiResponse\n');
  console.log(apiResponse);
});
```

Output:

```sh
> err:

{ [Error: Resource not found (resource=example).]
  errors: undefined,
  code: 404,
  message: 'Resource not found (resource=example).',
  response: undefined }
```


**Remove All Topics**

```js
// Remove All Topics
// =================
//
//  - gcloud: v0.15.x
//  - References:
//    - http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub?method=getTopics
//    - http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub/topic?method=delete
//
// Remove all topics.

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Main
// ----
//
// Remove all topics.
pubsub.getTopics(function callback(err, topics, next, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  console.log('\n> topics:\n');
  console.log(topics);

  console.log('\n> next:\n');
  console.log(next);

  console.log('\n> apiResponse:\n');
  console.log(apiResponse);

  topics.forEach(function (topic) {
    topic.delete(function (err, apiResponse) {
      if (err) {
        console.error('\n> err:\n');
        return console.error(err);
      }

      // Successful response will be an empty object.
      console.log('\napiResponse\n');
      console.log(apiResponse);
    });
  });

  if (next) {
    pubsub.getTopics(next, callback);
  }
});
```

Output:

```
> topics:

[ { makeReq_: [Function],
    name: 'projects/my-project/topics/example-1',
    projectId: 'my-project',
    pubsub:
     { makeAuthorizedRequest_: [Object],
       projectId: 'my-project',
       projectName: 'projects/my-project' },
    unformattedName: 'projects/my-project/topics/example-1' },
  { makeReq_: [Function],
    name: 'projects/my-project/topics/example-2',
    projectId: 'my-project',
    pubsub:
     { makeAuthorizedRequest_: [Object],
       projectId: 'my-project',
       projectName: 'projects/my-project' },
    unformattedName: 'projects/my-project/topics/example-2' } ]

> next:

null

> apiResponse:

{ topics:
   [ { name: 'projects/my-project/topics/example-1' },
     { name: 'projects/my-project/topics/example-2' } ] }

apiResponse

{}

apiResponse

{}
```


**Publish Messages**

```js
// Publish Messages
// ================
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub/topic?method=publish
//
// Publish a message or an array of messages to a topic.

'use strict';


// Dependencies                                                                                                                                         // ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var topicName = 'example';
var options = {
  autoCreate: true
};

// For publishing a single message.
var message = {
  // The `data` field will be base64 encoded, meaning the JavaScript object
  // specified here will be converted to JSON and encoded. The `data` will
  // be parsed to string first, then encoded in base64. And the receiving
  // message will be converted back to object automatically.
  data: {
    id: '0',
    name: 'root'
  },
  attributes: {
    key: 'val'
  }
};

// For publishing an array of messages.
var messages = [ message, message ];


// Functions
// ---------
//
// Whether publishing one or more messages, the callback parameters are the
// same.
function callback(err, messageIds, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  console.log('\n> messageIds:\n');
  console.log(messageIds);

  console.log('\n> apiResponse:\n');
  console.log(apiResponse);
}


// Main
// ----
//
// Publish a message.
var topic = pubsub.topic(topicName, options);
topic.publish(message, callback);
topic.publish(messages, callback);
```

Output:

```sh
> messageIds:

[ '51952129766' ]

> apiResponse:

{ messageIds: [ '51952129766' ] }

> messageIds:

[ '51952904166', '51952904167' ]

> apiResponse:

{ messageIds: [ '51952904166', '51952904167' ] }
```


**Create Subscription**

```js
// Create Subscription
// ===================
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub?method=subscribe
//
// Create a subscription to a topic.
//
// The format: `projects/:project/subscriptions/:subscription`

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var topicName = 'example-topic';
var topicOptions = {
  autoCreate: true
};
var subscriptionName = 'example-subscription-1';
var subscriptionOptions = {
  // The maximum time in seconds before message is being redelivered.
  ackDeadlineSeconds: 60,

  // Automatic acknowledgement.
  autoAck: false,

  // Number of milliseconds between checking for new messages.
  interval: 10,

  // Reuse the existing subscription or throw error.
  reuseExisting: false
};


// Functions
// ---------
//
// Callback function.
function callback(err, subscription, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  console.log('\n> subscription:\n');
  console.log(subscription);

  console.log('\n> apiResponse:\n');
  console.log(apiResponse);
}


// Main
// ----
//
// Topic must exist.
var topic = pubsub.topic(topicName, topicOptions);

// Create a subscription to the topic.
pubsub.subscribe(topic, subscriptionName, callback);

// Create the same subscription again.
pubsub.subscribe(topic, subscriptionName, callback);
```

Output:

```sh
> subscription:

{ domain: null,
  _events: { newListener: [Function], removeListener: [Function] },
  _maxListeners: undefined,
  name: 'projects/my-project/subscriptions/example-subscription-1',
  makeReq_: [Function],
  autoAck: false,
  closed: false,
  interval: 10 }

> apiResponse:

{ name: 'projects/my-project/subscriptions/example-subscription-1',
  topic: 'projects/my-project/topics/example-topic',
  pushConfig: {},
  ackDeadlineSeconds: 60 }

> err:

{ [Error: Resource already exists in the project (resource=example-subscription-1).]
  errors: undefined,
  code: 409,
  message: 'Resource already exists in the project (resource=example-subscription-1).',
  response: undefined }
```

From the subscription and topic formats, we can tell that both subscription and topic are unique to the project. Subscription is not unique within the domain of topic. Therefore, if there are two different topics, they still cannot have the same subscription name.


**Retrieve Subscription**

```js
// Retrieve Subscription
// =====================
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub?method=subscription
//
// Similar to retrieving a topic, creating a subscription object will not
// prompt API call.

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var subscriptionName = 'example-subscription-1';
var options = {
  // Automatic acknowledgement.
  autoAck: false,

  // Number of milliseconds between checking for new messages.
  interval: 10
};


// Main
// ----
//
// Create a subscription object.
var subscription = pubsub.subscription(subscriptionName, options);

console.log(subscription);
```

Output:

```sh
{ domain: null,
  _events: { newListener: [Function], removeListener: [Function] },
  _maxListeners: undefined,
  name: 'projects/my-project/subscriptions/example-subscription-1',
  makeReq_: [Function],
  autoAck: false,
  closed: false,
  interval: 10 }
```


**Retrieve Subscriptions**

Since subscription is unique to the project, therefore, we can retrieve all subscriptions of a project additional to retrieving all subscriptions of a topic.

```js
// Retrieve Subscriptions
// ======================
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub?method=getSubscriptions
//
// Retrieve a list of subscriptions. To retrieve all subscriptions, page
// through.

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var options = {
  // Topic object or topic name.
  topic: undefined,
  pageToken: undefined,
  pageSize: 1
};


// Functions
// ---------
function callback(err, subscriptions, next, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  console.log('\n> subscriptions:\n');
  console.log(subscriptions);

  console.log('\n> next:\n');
  console.log(next);

  console.log('\n> apiResponse:\n');
  console.log(apiResponse);

  // If exists, parameter `next` contains the entire query options to retrieve
  // next set of subscriptions, not just the paging token.
  if (next) {
    pubsub.getSubscriptions(next, callback);
  }
}


// Main
// ----
//
// Retrieve all subscriptions (not particular to a topic).
pubsub.getSubscriptions(options, callback);
```

Output:

```sh
> subscriptions:

[ { domain: null,
    _events: { newListener: [Function], removeListener: [Function] },
    _maxListeners: undefined,
    name: 'projects/my-project/subscriptions/example-subscription-1',
    makeReq_: [Function],
    autoAck: false,
    closed: false,
    interval: 10 } ]

> next:

{ topic: undefined, pageToken: 'QwxNDBs', pageSize: 1 }

> apiResponse:

{ subscriptions:
   [ { name: 'projects/my-project/subscriptions/example-subscription-1',
       topic: 'projects/my-project/topics/example-topic',
       pushConfig: {},
       ackDeadlineSeconds: 60 } ],
  nextPageToken: 'QwxNDBs' }

> subscriptions:

[ { domain: null,
    _events: { newListener: [Function], removeListener: [Function] },
    _maxListeners: undefined,
    name: 'projects/my-project/subscriptions/tmp',
    makeReq_: [Function],
    autoAck: false,
    closed: false,
    interval: 10 } ]
> next:

null

> apiResponse:

{ subscriptions:
   [ { name: 'projects/my-project/subscriptions/tmp',
       topic: '_deleted-topic_',
       pushConfig: {},
       ackDeadlineSeconds: 60 } ] }
```


**Remove Subscription**

```js
// Remove Subscription
// ===================
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub/subscription?method=delete
//
// Remove a subscription. Notice that when removing a topic, subscriptions to
// the topic will also be removed.

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var subscriptionName = 'example-subscription';


// Main
// ----
//
// Delete a subscription that does not exist.
var subscription = pubsub.subscription(subscriptionName);
subscription.delete(function (err, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  // Successful response will be an empty object.
  console.log('\napiResponse\n');
  console.log(apiResponse);
});
```

Output:

```sh
> err:

{ [Error: Resource not found (resource=example-subscription).]
  errors: undefined,
  code: 404,
  message: 'Resource not found (resource=example-subscription).',
  response: undefined }
```


**Pull Messages**

```js
// Pull Messages
// =============
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub/subscription?method=pull
//
// Pull messages (an array of messages) from a subscribed topic.
//
// > Note that messages are pulled automatically once you register your first
// event listener to the subscription, thus the call to pull is handled for
// you. If you don't want to start pulling, simply don't register a
// `subscription.on('message', function() {})` event handler.

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var subscriptionName = 'example-subscription-1';
var options = {
  // By setting to `true`, server will respond immediately, even no messages
  // on the server. Otherwise, it will wait for some time before responding.
  // Default is `false`. And the default waiting time is about one minute.
  returnImmediately: false,

  // Maximum number of messages to pull.
  //
  // TODO: What is the max?
  maxResults: undefined
};


// Functions
// ---------
function callback(err, messages, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  console.log('\n> messages:\n');
  messages.forEach(function (message) {
    console.log('\n> ackId:\n');
    console.log(message.ackId);

    console.log('\n> id:\n');
    console.log(message.id);

    console.log('\n> messages:\n');
    console.log(typeof message.data, ':', message.data);

    console.log('\n> attributes:\n');
    console.log(message.attributes);
  });

  console.log('\n> apiResponse:\n');
  console.log(apiResponse);
}


// Main
// ----
//
// Pull messages.
var subscription = pubsub.subscription(subscriptionName);
subscription.pull(options, callback);
```

Output:

```sh
> messages:


> ackId:

dR5LHlAbEGEIBERNK0EPKVgUWQYyODM2LwgRHFEZDDsLRk1SKhsNN1METwZzfms3KgkXEzgUACUwAk8HeE4YLRpQDnZyfWF1alsXVAEWBntZWRtYYl9WdRVrLyJ3fGhyb18QCQZBW1bo3dA2Zj9RDk9eIz81ICg1lVWBwY9HFELH3cOAjYYFVUGICIjIg

> id:

51975538448

> messages:

object : { id: '0', name: 'root' }

> attributes:

{ key: 'val' }

> apiResponse:

{ receivedMessages:
   [ { ackId: 'dR5LHlAbEGEIBERNK0EPKVgUWQYyODM2LwgRHFEZDDsLRk1SKhsNN1METwZzfms3KgkXEzgUACUwAk8HeE4YLRpQDnZyfWF1alsXVAEWBntZWRtYYl9WdRVrLyJ3fGhyb18QCQZBW1bo3dA2Z9RDk9eIz81ICg1QlVWBwY9HFELH3cOAjYYFVUGICIjIg',
       message: [Object] } ] }
```


**Wait Messages**

```js
// Wait Messages
// =============
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub/subscription
//
// Wait for messages by attaching event listener. If there are no more messages
// the listener will not terminate, it is just like an HTTP server that will
// continue serving and polling for new messages.
//
// > Note that messages are pulled automatically once you register your first
// event listener to the subscription, thus the call to pull is handled for
// you. If you don't want to start pulling, simply don't register a
// `subscription.on('message', function() {})` event handler.
//
// TODO: What are all the events?

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var subscriptionName = 'example-subscription-1';
var subscriptionOptions = {
  autoAck: false,
  interval: 500 // ms (twice a second)
};


// Listeners
// ---------
//
// Error listener.
function errorListener(err) {
  console.error('\n> err:\n');
  console.error(err);
}

// Message listener.
function messageListener(message) {
  console.log('\n> ackId:\n');
  console.log(message.ackId);

  console.log('\n> id:\n');
  console.log(message.id);

  console.log('\n> messages:\n');
  console.log(typeof message.data, ':', message.data);

  console.log('\n> attributes:\n');
  console.log(message.attributes);

  // Remove listener and terminate the polling server. Otherwise, it will
  // continue polling for new messages.
  subscription.removeListener('message', messageListener);
}


// Main
// ----
//
// Create a subscription.
var subscription = pubsub.subscription(subscriptionName, subscriptionOptions);

// Register an error listener.
subscription.on('error', errorListener);

// Register a message listener.
subscription.on('message', messageListener);
```

Output:

```sh
> ackId:

dR5LHlAbEGEIBERNK0EPKVgUWQYyODM2LwgRHFEZDDsLRk1SKhsNN1METwZzfms3KgkXEzgUACUwAk8HeE4YLRpQDnZyfWF1alsXVAEWBntZWRtYYl9WdRVrLyJ3fGhyb18QCQZBW1bo3dA2Zj9RDk9eIz81ICg1QlVWBwY9HFELH3cOAjYYFVUGICIjIg

> id:

51975538448

> messages:

object : { id: '0', name: 'root' }

> attributes:

{ key: 'val' }
```


**Acknowledge Message**

Do not automatically acknowledge the message, do it when you are actually done with it.

```js
// Ack Messages
// ============
//
//  - gcloud: v0.15.x
//  - Reference: http://googlecloudplatform.github.io/gcloud-node/#/docs/v0.15.0/pubsub/subscription?method=ack
//
// Acknowledge to the message server on the retrieval and handling of one or
// more messages.

'use strict';


// Dependencies
// ------------
var pubsub = require('gcloud').pubsub({
  projectId  : process.env.PROJECT,
  keyFilename: process.env.KEYFILENAME
});


// Parameters
// ----------
var subscriptionName = 'example-subscription-1';
var subscriptionOptions = {
  autoAck: false,
  interval: 500 // ms (twice a second)
};


// Functions
// ---------
function callback(err, apiResponse) {
  if (err) {
    console.error('\n> err:\n');
    return console.error(err);
  }

  console.log('\n> apiResponse:\n');
  console.log(apiResponse);
}


// Listeners
// ---------
//
// Error listener.
function errorListener(err) {
  console.error('\n> err:\n');
  console.error(err);
}

// Message listener.
function messageListener(message) {
  console.log('\n> ackId:\n');
  console.log(message.ackId);

  console.log('\n> id:\n');
  console.log(message.id);

  console.log('\n> messages:\n');
  console.log(typeof message.data, ':', message.data);

  console.log('\n> attributes:\n');
  console.log(message.attributes);

  // Acknowledge the message after done processing it. To process an array of
  // messages, replace the first parameter with an array of strings.
  subscription.ack(message.ackId, callback);
}


// Main
// ----
//
// Create a subscription.
var subscription = pubsub.subscription(subscriptionName, subscriptionOptions);

// Register an error listener.
subscription.on('error', errorListener);

// Register a message listener.
subscription.on('message', messageListener);
```

Output:

```sh
> ackId:

dR5LHlAbEGEIBERNK0EPKVgUWQYyODM2LwgRHFEZDDsLRk1SKhsNN1METwZzfms3KgkXEzgUACUwAk8HeE4YLRpQDnZyfWF1alsXVAEWBntZWRtYYl9WdRVrLyJ3fGhyb18QCQZBW1bo3dA2Zj9RDk9eIz81ICg$QlVWBwY9HFELH3cOAjYYFVUGICIjIg

> id:

51975538448

> messages:

object : { id: '0', name: 'root' }

> attributes:

{ key: 'val' }

> apiResponse:

{}
```