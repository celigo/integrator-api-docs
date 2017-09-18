Introduction
============

The integrator.io API is RESTful, uses JSON, and is secured by Bearer Tokens. The target audience for the API is developers that are interested in building one or more of the following.

1. **SmartConnectors**.  These are fully managed, pre-built integration apps listed in the [integrator.io marketplace](https://integrator.io/marketplace) that can be installed by any other integrator.io user.   These integration apps typically include an installer, uninstaller, licensing controls, settings pages, and receive on-going enhancements in functionality.

2. **Embedded Integrations**.  These are fully managed integrations where integrator.io remains completely invisible to the end user.  These integrations are typically enabled and/or configured within an external application's UI, where that application's backend servers use the integrator.io API to dynamically deploy and customize integration functionality.

3. **Hooks**.  integrator.io is all about creating and running flows. A flow is composed of one or more exports along with one or more imports.  Exports are responsible for pulling data out of an application, and imports are responsible for mapping and inserting data into an application.  Hooks are well defined points within the flow where custom code can be run (i.e. to customize the flow beyond what is possible in the integrator.io UI).  See [here](https://github.com/celigo/integrator-extension#hooks) for more info.

4. **Wrappers**.  These can be used to connect applications that are not natively supported by integrator.io.  See [here](https://github.com/celigo/integrator-extension#wrappers) for more info.

Enjoy, and happy integrating!


Authentication
============

### Bearer Tokens

API tokens can be generated and/or accessed via Options -> Tokens (within your integrator.io account).  API clients should transmit their token using the "**Authorization**" request header field and the "**Bearer**" authentication scheme.

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
When using the integrator.io extension framework one-time tokens are also supported (and highly recommended).  The one-time tokens are passed in the options argument to each of your functions, and can be used similar to [Bearer Tokens](https://github.com/celigo/integrator-api-docs/blob/master/readme.md#bearer-tokens) to call back into integrator.io. One-time tokens auto expire after being used (or after a 15 minute timeout if never used). For SmartConnectors (i.e. managed integration apps), one-time tokens passed are only granted access to invoke the resources that belong to the SmartConnector.

Resource Types
=========
Very important tip!  You can see the JSON data for any resource/record in your integrator.io account by using the following URL in your browser.  https://integrator.io/api/  If you are ever in doubt how to use the API to create something you can create it first in the UI and then view the JSON via the URL above.  

### Connection

Connections are used to store credentials, along with any other information needed to access the applications you are integrating.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connection.md) for details.

### Export

Exports are used to extract data from an application. Exports can run standalone via the API, or in the context of a flow.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/export.md) for details.

### Import

Imports are used to insert data into an application. Imports can run standalone via the API, or in the context of a flow.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/import.md) for details.

### Flow

Flows are used to compose exports and imports so that data can be exported out of one or more applications and then imported into one or more applications. 

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/flow.md) for details.

### Integration

Integrations are used to group flows.  All flows that belong to the same integration will share the same permissions, and all stats will bubble up under the same tile on the home page.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/integration.md) for details.

### State

State is an API only resource type that can be used to store arbitrary JSON data (associated with a custom key).  Typically the state API is used to persist data about a flow's last execution, and then to use that same data to parameterize the next execution of the flow (i.e. the next time it runs).

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/state.md) for details.

Less Common Resource Types
-----------

### iClient

iClients are used (mostly) by SmartConnectors to store the authentication data required to connect with a specific API (on behalf of the SmartConnector).  For example, if you are building a SmartConnector for Salesforce you will be required (by Salesforce) to register your app, and Salesforce will proide you with a client id, token, etc... and then you can use an iClient to store this data and also make it available ONLY to your SmartConnector install base.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/iClient.md) for details.


### Connector

Connectors represent SmartConnectors in the integrator.io marketplace.  This is the listing record for a SmartConnector, and also contains various fields to configure the installer, uninstaller, applications being integrated, etc...

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connector.md) for details.


### Template

This is the listing record for Templates in the integrator.io marketplace.  Templates are different than SmartConnectors in that they are completely un-managed once installed by end users.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/template.md) for details.

### Job

Jobs represent the state of a flow while it is running (start time, status, percentage complete, etc...), and then the final stats for a flow when it has finished running (num success, num error, end time, etc...). 

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/job.md) for details.

### Notification

This resource type is used to subscribe to notifications for specific events in integrator.io.  For example, send me an email when a connection goes offline in my account.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/notification.md) for details.

### Stack

Stacks are used to register servers and/or AWS Lambda accounts that can be invoked by SmartConnectors, Hooks or Wrappers.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/stack.md) for details.

### License

Licenses are used to provide access to features, add-ons, SmartConnectors, etc...

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/license.md) for details.

### Usage

Usage records track the amount of time your custom integrations are actively running on the integrator.io platform.

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/usage.md) for details.


Rate Limiting
============

The Integrator API is rate limited using a leaky bucket algorithm with a bucket size of 100 and a fill rate of 30 tokens every 1 second, which approximates to 108,000 requests allowed per hour.

Headers
============

The Integrator API supports the following custom HTTP headers.

-	Integrator-Relative-URI
-	Integrator-Record-Type
