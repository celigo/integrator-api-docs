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

API tokens can be generated and accessed via 'Options' -> 'API Tokens' (within your integrator.io account).  Please note that only the owner of an integrator.io account can access the API tokens page.  API clients should transmit API tokens using the "**Authorization**" request header field and the "**Bearer**" authentication scheme.

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
  "_userId": "5e03061cda20740022300f09"
}
```

### One-time Tokens
When using the integrator.io extension framework one-time tokens are also supported (and highly recommended).  The one-time tokens are passed in the options argument to each of your functions, and can be used similar to [Bearer Tokens](#bearer-tokens) to call back into integrator.io. One-time tokens auto expire after being used (or after a 15 minute timeout if never used). For SmartConnectors (i.e. managed integration apps), one-time tokens passed are only granted access to invoke the resources that belong to the SmartConnector.

Resource Types
=========
> **Very important tip!**  The integrator.io UI also uses the API, and if you ever get stuck with the API you can always revert back to the UI to create and/or modify something, and then use your API client (or the browser via https://integrator.io/api/relative_uri_here) to see the resulting JSON.

### Connection

Connections are used to store credentials, along with any other information needed to access the applications you are integrating.

Click [here](/connection.md) for details.

### Export

Exports are used to extract data from an application. Exports can run standalone via the API, or in the context of a flow.

Click [here](/export.md) for details.

### Import

Imports are used to insert data into an application. Imports can run standalone via the API, or in the context of a flow.

Click [here](/import.md) for details.

### Flow

Flows are used to compose exports and imports so that data can be exported out of one or more applications and then imported into one or more applications.

Click [here](/flow.md) for details.

### Integration

Integrations are used to group flows.  All flows that belong to the same integration will share the same permissions, and all stats will bubble up under the same tile on the home page.

Click [here](/integration.md) for details.

### State

State is an API only resource type that can be used to store arbitrary JSON data (associated with a custom key).  Typically the state API is used to persist data about a flow's last execution, and then to use that same data to parameterize the next execution of the flow (i.e. the next time it runs).

Click [here](/state.md) for details.

Less Common Resource Types
-----------

### iClient

iClients are used (mostly) by SmartConnectors to store the authentication data required to connect with a specific API (on behalf of the SmartConnector).  For example, if you are building a SmartConnector for Salesforce you will be required (by Salesforce) to register your app, and Salesforce will provide you with a client id, token, etc... and then you can use an iClient to store this data and also make it available ONLY to your SmartConnector install base.

Click [here](/iClient.md) for details.


### Connector + License

The connector resource type represents the SmartConnector solutions you build and list in the integrator.io marketplace.  This resource type has basic listing type fields to help market your solution, and then developer type fields to specify which functions on your server stack should be invoked when the connector is installed, uninstalled, etc... There is a license resource type available too (within the context of a connector) to formally provision your solution to an end user.  And, using the related licensing APIs you can fully automate provisioning of your solution with an external subscription and/or recurring payments platform.  You can even use an integrator.io account to manage the data flows between those apps (i.e. integrator.io and subscription/payment platform)!

Click [here](/connector.md) for details.


### Template

This is the listing record for Templates in the integrator.io marketplace.  Templates are different than SmartConnectors in that they are completely un-managed once installed by end users.

Click [here](/template.md) for details.

### Job

Jobs represent the state of a flow while it is running (start time, status, percentage complete, etc...), and then the final stats for a flow when it has finished running (num success, num error, end time, etc...).

Click [here](/job.md) for details.


Rate Limiting
============

The integrator.io API is rate limited using a leaky bucket algorithm with a bucket size of 1000 and a fill rate of 300 tokens every 1 second, which approximates to 1,080,000 requests allowed per hour.

Headers
============

The integrator.io API supports the following custom HTTP headers to facilitate proxing requests directly to a NetSuite RESTlet.

-    Integrator-NetSuite-ScriptId
-    Integrator-NetSuite-DeployId
