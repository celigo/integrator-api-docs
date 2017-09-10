Introduction
============

The Integrator API is RESTful, uses JSON, and is secured by Bearer Tokens. The target audience for the API is developers that are interested in building one or more of the following.

1. **SmartConnectors**.  These are fully managed, pre-built integration apps listed in the [integrator.io marketplace](https://integrator.io/marketplace) that can be installed by any other integrtaor.io user.   These integration apps typically include an installer, uninstaller, licensing controls, settings pages, and receive on-going enhancements in functionality.

2. **Embedded Integrations**.  These are fully managed integration where integrator.io remains completely invisible to the end user.  These integrations will typically be enabled and/or configured from within an external application's UI, where application's backend servers use the integrator.io API to dynamically deploy and customize integration functionality based on settings stored in their application.

3. **Hooks**.  A 'flow' in integrator.io is composed of one or more 'exports' that feed into one or more 'imports'. The exports are responsible for pulling data out of one or more applications, and the imports are the imports are responsible for mapping and inserting the exported data into one more applications.  Hooks represent a framework whereby your custom code can be injected into the flow for the purpose of customizing the behavior of the flow beyond what is possible in the integrator.io UI.  See [here](https://github.com/celigo/integrator-extension#hooks) for more info.

4. **Wrappers**.  If you need to connect with an application that is not natively supported by integrator.io then you can use Wrappers to build totally custom export and/or import adaptors that can be plugged into any of your integrator.io flows.  See [here](https://github.com/celigo/integrator-extension#wrappers) for more info.

Enjoy, and happy integrating!


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

Connections are used to store credentials, along with other access information for an application or system. Currently the Integrator supports the following connection types.

-	[NetSuite](#NetSuite Connection)
-	[REST API](#REST Connection)
-	[SFTP/FTP](#FTP Connection)
-	[S3](#S3)

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connection.md) for more info on connection

Export
------

Exports are used to extract data from an application or system. Exports can run standalone, or in the context of a [Flow](#Flow).

Standalone Exports are useful for integrations where user actions (like clicking a button) need to invoke an external application's API in real-time and return the results immediately. Standalone exports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive. Celigo uses standalone Exports extensively in its spreadsheet and email sync products.

Exports running in the context of a Flow will execute asynchronously and automatically break exported data down into one or more pages and then stream those pages to one or more [Imports](#Import).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/export.md) for more info on export

Import
------

Imports are used to insert data into an application. Like [Exports](#Export), Imports can run standalone, or in the context of a [Flow](#Flow).

Standalone Imports are useful for integrations where user actions (like clicking a button) need to invoke an application's API in real-time and return the results immediately. Standalone imports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive. Celigo uses standalone Imports extensively in its spreadsheet and email sync products.

Imports running in the context of a Flow will also execute synchronously but will channel results back to the flow so that stats can be reported in a job record.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/import.md) for more info on import

Flow
----

Flows are used to export data out of one application and import it into another application. A Flow is basically an [Export](#Export) linked to one or more [Imports](#Import). A Flow can channel data generated by a real time export, or if real-time is not an options (based on the application where data is being exported) a Flow can be scheduled to run on a periodic basis. Flows can be scheduled to run as often as once per minute; however, only one instance of a Flow can run at any given time (i.e. you cannot run the same Flow more than once at the same time, and if a Flow is still running when it is scheduled to run again it will simply queue itself up and run when the currently running instance completes).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/flow.md) for more info on flow

Integration
-----------

Integrations are used group one or more [Imports](#Import), [Exports](#Export), or [Flows](#Flow). Integrations will show as tiles in the Integrator dashboard.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/integration.md) for more info on Integrations

Connector
---------

Connectors represent fully functional pre-built integrations that any user can install into their Integrator account, directly from the Integrator Marketplace. Connectors include an installer, uninstaller, and a settings interface. Connector developers can push updates at any time for their Connector to their entire install base. Connectors are mostly made up of code (that subsequently uses the Integrator API to interact with a user's account). A Connector can create any number of components in a user's account. All components created in a user's account are tagged with the Connector's \_id (via the \_connectorId field). A Connector can only modify components tagged with its own \_id. The schema below mostly serves the purpose of listing a Connector in the Integrator Marketplace (i.e. description, imageURL, websiteURL, etc...).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connector.md) for more info on connector

Job
---

Job represents execution state of a flow in integrator. When a flow is run, a job is created and it has a unique identifier associated with it. Job contains status property that that holds the current state of the job. These states are: queued, running, completed, failed, cancelling and cancelled. For example, when a flow is run, new job is created with the status 'queued'. When the worker thread picks up that particular job and start executing, job status is updated as 'running' till it completes its execution. Once the job is completed, its status could be 'completed' or 'failed' Along with the status, job contains information about number of success and number of errors. A job can go to 'completed' state even though there are errors as some of the records in the flow might have failed. In that event, failed records can be retried. Jobs can be referred using the unique Id associated with them.

On retry of the job, new job is created but the results of retried job will be still associated with the original parent job. At any given time, only one job will be in running state for a flow.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/job.md) for more info on job

IClient
-------

iClient provides the mode of authentication for the connectors to authenticate the resource access from integrator. oAuth 2.0 is the authorization protocol used for iClients.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/iClient.md) for more info on iClient

Retry
-----

This is the feature provided in the integrator to retry any flow, export or import in the event of failure. This will create new job for the retry execution but the results of the retry will be associated with the parent job.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/retry.md) for more info on retry

Job Error
---------

Any error that have caused the executing job to fail. There can be scenarios where job can be completed but it may have errors as some records involved in the job might have failed.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/jobError.md) for more info on jobError
