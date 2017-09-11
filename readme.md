Introduction
============

The Integrator API is RESTful, uses JSON, and is secured by Bearer Tokens. The target audience for the API is developers that are interested in building one or more of the following.

1. **SmartConnectors**.  These are fully managed, pre-built integration apps listed in the [integrator.io marketplace](https://integrator.io/marketplace) that can be installed by any other integrator.io user.   These integration apps typically include an installer, uninstaller, licensing controls, settings pages, and receive on-going enhancements in functionality.

2. **Embedded Integrations**.  These are fully managed integrations where integrator.io remains completely invisible to the end user.  These integrations are typically enabled and/or configured within an external application's UI, where that application's backend servers use the integrator.io API to dynamically deploy and customize integration functionality.

3. **Hooks**.  integrator.io is all about creating and running flows. A flow is composed of one or more exports along with one or more imports.  Exports are responsible for pulling data out of an application, and imports are responsible for mapping and inserting data into an application.  Hooks are well defined points within the flow where custom code can be run (i.e. to customize the flow beyond what is possible in the integrator.io UI).  See [here](https://github.com/celigo/integrator-extension#hooks) for more info.

4. **Wrappers**.  These can be used to connect applications that are not natively supported by integrator.io.  See [here](https://github.com/celigo/integrator-extension#wrappers) for more info.

Enjoy, and happy integrating!


Authentication
============

### Bearer Tokens

API tokens can be generated and/or accessed via Options -> Tokens (within the integrator.io account).  API clients should transmit the token using the "**Authorization**" request header field and the "**Bearer**" authentication scheme.

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

### One-time Tokens
When using the integrator.io extension framework one-time tokens are supported (and highly recommended).  The one-time tokens are passed in the options argument to each your function, and can be used similar to [Bearer Tokens](https://github.com/celigo/integrator-api-docs/blob/master/readme.md#bearer-tokens) to call back into integrator.io. One-time tokens auto expire after being used (or after a 15 minute timeout if never used). For SmartConnectors (i.e. managed integration apps), one-time tokens passed are only granted access to invoke the resources that belong to the SmartConnector.

Resource Types
=========

### Connection

Connections are used to store credentials, along with other access information for the applications you are integrating.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connection.md) for more info regarding the connection resource type and all its related API endpoints.

### Export

Exports are used to extract data from an application. Exports can run standalone via the API, or in the context of a flow.

Standalone exports (invoked via the API) are useful for Embedded Integrations where user actions in an external app (like clicking a button) need to invoke an external application's API in real-time and return the results immediately. Standalone exports are also useful in Hooks to call back into an application dynamically (i.e. to get more data while a flow is running).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/export.md) for more info regarding the export resource type and all its related API endpoints.

### Import

Imports are used to insert data into an application. Imports can run standalone via the API, or in the context of a flow.

Standalone imports (invoked via the API) are useful for Embedded Integrations where user actions in an external app (like clicking a button) need to invoke an external application's API in real-time and return the results immediately. Standalone imports are also useful in Hooks to submit data into an application dynamically based on business logic.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/import.md) for more info regarding the import resource type and all its related API endpoints.

### Flow

Flows are used to comppose exports and imports so that data can exported out of one or more applications and then imported into one or more applications. 

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/flow.md) for more info regarding the flow resource type and all its related API endpoints.

### Integration

Integrations are used group flows.  All flows that belong to the same integration will share the same permissions, and stats will bubble up to the same tile, etc...

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/integration.md) for more info regarding the integration resource type and all its related API endpoints.

### State

State is an API only resource type that can be used to store arbitrary JSON data.  Typically the state API is used to persist data about a flow's last execution, and then use that data to parameterize the flow the next time it runs.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/state.md) for more info regarding the state resource type and all its related API endpoints.

Less Popular Resource Types (from API standpoint)
=========

### iClient

iClient provides the mode of authentication for the connectors to authenticate the resource access from integrator. oAuth 2.0 is the authorization protocol used for iClients.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/iClient.md) for more info on iClient

### Connector

Connectors represent fully functional pre-built integrations that any user can install into their Integrator account, directly from the Integrator Marketplace. Connectors include an installer, uninstaller, and a settings interface. Connector developers can push updates at any time for their Connector to their entire install base. Connectors are mostly made up of code (that subsequently uses the Integrator API to interact with a user's account). A Connector can create any number of components in a user's account. All components created in a user's account are tagged with the Connector's \_id (via the \_connectorId field). A Connector can only modify components tagged with its own \_id. The schema below mostly serves the purpose of listing a Connector in the Integrator Marketplace (i.e. description, imageURL, websiteURL, etc...).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connector.md) for more info on connector

### Template

TODO

### Job

Job represents execution state of a flow in integrator. When a flow is run, a job is created and it has a unique identifier associated with it. Job contains status property that that holds the current state of the job. These states are: queued, running, completed, failed, cancelling and cancelled. For example, when a flow is run, new job is created with the status 'queued'. When the worker thread picks up that particular job and start executing, job status is updated as 'running' till it completes its execution. Once the job is completed, its status could be 'completed' or 'failed' Along with the status, job contains information about number of success and number of errors. A job can go to 'completed' state even though there are errors as some of the records in the flow might have failed. In that event, failed records can be retried. Jobs can be referred using the unique Id associated with them.

On retry of the job, new job is created but the results of retried job will be still associated with the original parent job. At any given time, only one job will be in running state for a flow.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/job.md) for more info on job


### Retry

This is the feature provided in the integrator to retry any flow, export or import in the event of failure. This will create new job for the retry execution but the results of the retry will be associated with the parent job.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/retry.md) for more info on retry

### Job Error

Any error that have caused the executing job to fail. There can be scenarios where job can be completed but it may have errors as some records involved in the job might have failed.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/jobError.md) for more info on jobError

### Notifications

TODO

### Stack

TODO

### License

TODO

### Usage

TODO


Rate Limiting
============

The Integrator API is rate limited using a leaky bucket algorithm with a bucket size of 100 and a fill rate of 30 tokens every 1 second, which approximates to 108,000 requests allowed per hour.

Headers
============

The Integrator API supports the following custom HTTP headers.

-	Integrator-Relative-URI
-	Integrator-Record-Type
