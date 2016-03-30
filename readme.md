Introduction
============

The Integrator is an API first platform. Features are released here first, and then shortly after in the UI. The Integrator API is RESTful, uses JSON, and is secured by Bearer Tokens. The target audience for the API is developers building integration based apps. Complementing the API, the Integrator supports a rich extension framework. The integrations you build can include an installer, settings pages, along with any number of hooks or wrappers (hooks and wrappers give you the ability to write your own custom code, and are useful for requirements that cannot be implemented via configuration alone). Any integrations that you build can be listed in the Integrator Marketplace for easy install by any other Integrator user. Enjoy!

Authentication
--------------

### Bearer Tokens

Every Integrator account is provisioned with one API token. API clients should transmit this token using the "Authorization" request header field and the "Bearer" authentication scheme.

Sample HTTP Request:

```
GET /v1/tokenInfo HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{
  "_userId": "5e03061cda20740022300f09",
  "scope":"*"
}
```

### One Time Tokens

The Integrator will also pass one-time tokens to backend components implementing hooks, wrappers, installer, uninstaller, or settings interfaces. These tokens are passed in the options argument of each function, and can be used similarly to [Bearer Tokens](#Bearer Tokens) to call back into the Integrator. One-time tokens will auto expire after use (determined via a return statement, or after a 15 minute timeout). For Connectors (managed integration apps), tokens passed are only granted access to invoke resources belonging to the Connector itself (determined via the \_connectorId property). For hooks and wrappers not belonging to a Connector, one-time tokens are limited to only invoking Exports and Imports already defined in the Integrator account.

Rate Limiting
-------------

The Integrator API is rate limited using a leaky bucket algorithm with a bucket size of 100 and a fill rate of 30 tokens every 1 second, which approximates to 108,000 requests allowed per hour.

Headers
-------

The Integrator API supports the following custom HTTP headers.

-	Integrator-Relative-URI
-	Integrator-Record-Type

Resources
=========

Connection
----------

##### What is a Connection?

Connections are used to store credentials, along with other access information for an application or system. Currently the Integrator supports the following connection types.

-	[NetSuite](#NetSuite Connection)
-	[REST API](#REST Connection)
-	[SFTP/FTP](#FTP Connection)
-	[S3](#S3)

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connection.md) for more info on connection

TODO here! lots of routes to add still. :( GET /connections/:\_id/ping POST /connections/ping etc...

Export
------

##### What is an Export?

Exports are used to extract data from an application or system. Exports can run standalone, or in the context of a [Flow](#Flow).

Standalone Exports are useful for integrations where user actions (like clicking a button) need to invoke an external application's API in real-time and return the results immediately. Standalone exports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive. Celigo uses standalone Exports extensively in its spreadsheet and email sync products.

Exports running in the context of a Flow will execute asynchronously and automatically break exported data down into one or more pages and then stream those pages to one or more [Imports](#Import).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/export.md) for more info on export

Import
------

##### What is an Import?

Imports are used to insert data into an application. Like [Exports](#Export), Imports can run standalone, or in the context of a [Flow](#Flow).

Standalone Imports are useful for integrations where user actions (like clicking a button) need to invoke an application's API in real-time and return the results immediately. Standalone imports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive. Celigo uses standalone Imports extensively in its spreadsheet and email sync products.

Imports running in the context of a Flow will also execute synchronously but will channel results back to the flow so that stats can be reported in a job record.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/import.md) for more info on import

Flow
----

##### What is a Flow?

Flows are used to export data out of one application and import it into another application. A Flow is basically an [Export](#Export) linked to one or more [Imports](#Import). A Flow can channel data generated by a real time export, or if real-time is not an options (based on the application where data is being exported) a Flow can be scheduled to run on a periodic basis. Flows can be scheduled to run as often as once per minute; however, only one instance of a Flow can run at any given time (i.e. you cannot run the same Flow more than once at the same time, and if a Flow is still running when it is scheduled to run again it will simply queue itself up and run when the currently running instance completes).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/flow.md) for more info on flow

Integration
-----------

##### What is an Integration?

Integrations are used group one or more [Imports](#Import), [Exports](#Export), or [Flows](#Flow). Integrations will show as tiles in the Integrator dashboard.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/integration.md) for more info on Integrations

Connector
---------

##### What is a Connector?

Connectors represent fully functional pre-built integrations that any user can install into their Integrator account, directly from the Integrator Marketplace. Connectors include an installer, uninstaller, and a settings interface. Connector developers can push updates at any time for their Connector to their entire install base. Connectors are mostly made up of code (that subsequently uses the Integrator API to interact with a user's account). A Connector can create any number of components in a user's account. All components created in a user's account are tagged with the Connector's \_id (via the \_connectorId field). A Connector can only modify components tagged with its own \_id. The schema below mostly serves the purpose of listing a Connector in the Integrator Marketplace (i.e. description, imageURL, websiteURL, etc...).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connector.md) for more info on connector

Job
---

##### What is a Job?

Job represents execution state of a flow in integrator. When a flow is run, a job is created and it has a unique identifier associated with it. Job contains status property that that holds the current state of the job. These states are: queued, running, completed, failed, cancelling and cancelled. For example, when a flow is run, new job is created with the status 'queued'. When the worker thread picks up that particular job and start executing, job status is updated as 'running' till it completes its execution. Once the job is completed, its status could be 'completed' or 'failed' Along with the status, job contains information about number of success and number of errors. A job can go to 'completed' state even though there are errors as some of the records in the flow might have failed. In that event, failed records can be retried. Jobs can be referred using the unique Id associated with them.

On retry of the job, new job is created but the results of retried job will be still associated with the original parent job. At any given time, only one job will be in running state for a flow.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/job.md) for more info on job

IClient
-------

Job Error
---------

Retry
-----
