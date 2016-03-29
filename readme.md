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

##### Connection Related HTTP Endpoints

| Relative URI       | Method | Success Code | Description                   |
|:-------------------|:-------|:------------:|:------------------------------|
| /connections       | GET    |     200      | List all connections.         |
| /connections       | POST   |     201      | Create new connection.        |
| /connections/{_id} | PUT    |     200      | Update existing connection.   |
| /connections/{_id} | GET    |     200      | Retrieve existing connection. |
| /connections/{_id} | DELETE |     204      | Delete existing connection.   |

TODO here! lots of routes to add still. :( GET /connections/:\_id/ping POST /connections/ping etc...

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/connection.md) for more info on connection

Export
------

##### What is an Export?

Exports are used to extract data from an application or system. Exports can run standalone, or in the context of a [Flow](#Flow).

Standalone Exports are useful for integrations where user actions (like clicking a button) need to invoke an external application's API in real-time and return the results immediately. Standalone exports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive. Celigo uses standalone Exports extensively in its spreadsheet and email sync products.

Exports running in the context of a Flow will execute asynchronously and automatically break exported data down into one or more pages and then stream those pages to one or more [Imports](#Import).

##### Export Related HTTP Endpoints

| Relative URI               | Method | Success Code | Description                                                                    |
|:---------------------------|:-------|:------------:|:-------------------------------------------------------------------------------|
| /exports                   | GET    |     200      | List all exports.                                                              |
| /exports                   | POST   |     201      | Create new export.                                                             |
| /exports/{_id}             | PUT    |     200      | Update existing export.                                                        |
| /exports/{_id}             | GET    |     200      | Retrieve existing export.                                                      |
| /exports/{_id}             | DELETE |     204      | Delete existing export.                                                        |
| /exports/{_id}/distributed | PUT    |     200      | Create or update a distributed component that is linked to an existing export. |
| /exports/{_id}/distributed | GET    |     200      | Retrieve a distributed component that is linked to an existing export.         |
| /{apiIdentifier}           | POST   |     200      | Invoke an export.                                                              |
| /exports/preview           | POST   |     200      | Invoke an export in test mode before saving it to preview the results.         |

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/export.md) for more info on export

Import
------

##### What is an Import?

Imports are used to insert data into an application. Like [Exports](#Export), Imports can run standalone, or in the context of a [Flow](#Flow).

Standalone Imports are useful for integrations where user actions (like clicking a button) need to invoke an application's API in real-time and return the results immediately. Standalone imports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive. Celigo uses standalone Imports extensively in its spreadsheet and email sync products.

Imports running in the context of a Flow will also execute synchronously but will channel results back to the flow so that stats can be reported in a job record.

##### Import Related HTTP Endpoints

| Relative URI               | Method | Success Code | Description                                                                    |
|:---------------------------|:-------|:------------:|:-------------------------------------------------------------------------------|
| /imports                   | GET    |     200      | List all imports.                                                              |
| /imports                   | POST   |     201      | Create new import.                                                             |
| /imports/{_id}             | PUT    |     200      | Update existing import.                                                        |
| /imports/{_id}             | GET    |     200      | Retrieve existing import.                                                      |
| /imports/{_id}             | DELETE |     204      | Delete existing import.                                                        |
| /imports/{_id}/distributed | PUT    |     200      | Create or update a distributed component that is linked to an existing import. |
| /imports/{_id}/distributed | GET    |     200      | Retrieve a distributed component that is linked to an existing import.         |
| /{apiIdentifier}           | POST   |     200      | Invoke an import on the req body (should be JSON array).                       |

Click [here](https://github.com/celigo/integrator-api-docs/blob/master/import.md) for more info on import

Flow
----

##### What is a Flow?

Flows are used to export data out of one application and import it into another application. A Flow is basically an [Export](#Export) linked to one or more [Imports](#Import). A Flow can channel data generated by a real time export, or if real-time is not an options (based on the application where data is being exported) a Flow can be scheduled to run on a periodic basis. Flows can be scheduled to run as often as once per minute; however, only one instance of a Flow can run at any given time (i.e. you cannot run the same Flow more than once at the same time, and if a Flow is still running when it is scheduled to run again it will simply queue itself up and run when the currently running instance completes).

##### Flow Related HTTP Endpoints

| Relative URI | Method | Success Code | Description             |
|:-------------|:-------|:------------:|:------------------------|
| /flows       | GET    |     200      | List all flows.         |
| /flows       | POST   |     201      | Create new flow.        |
| /flows/{_id} | PUT    |     200      | Update existing flow.   |
| /flows/{_id} | GET    |     200      | Retrieve existing flow. |
| /flows/{_id} | DELETE |     204      | Delete existing flow.   |

#### Relevant Schema Info

##### Flow

| Field              | Description |
|:-------------------|:------------|
| **_id**            | .           |
| **name**           | .           |
| **schedule**       | .           |
| **lastModified**   | .           |
| **_exportId**      | .           |
| **_importId**      | .           |
| **_integrationId** | .           |
| **_connectorId**   | .           |
| **disabled**       | .           |

Integration
-----------

##### What is an Integration?

Integrations are used group one or more [Imports](#Import), [Exports](#Export), or [Flows](#Flow). Integrations will show as tiles in the Integrator dashboard.

##### Integration Related HTTP Endpoints

| Relative URI                               | Method | Success Code | Description                                          |
|:-------------------------------------------|:-------|:------------:|:-----------------------------------------------------|
| /integrations                              | GET    |     200      | List all integrations.                               |
| /integrations                              | POST   |     201      | Create new integration.                              |
| /integrations/{_id}                        | PUT    |     200      | Update existing integration.                         |
| /integrations/{_id}                        | GET    |     200      | Retrieve existing integration.                       |
| /integrations/{_id}                        | DELETE |     204      | Delete existing integration.                         |
| /integrations/{_connectorId}/install       | POST   |     200      | Install connector.                                   |
| /integrations/{_id}/install                | DELETE |     200      | Uninstall connector.                                 |
| /integrations/{_id}/installer/{function}   | PUT    |     200      | Invoke 'function' (belonging to installer module).   |
| /integrations/{_id}/uninstaller/{function} | PUT    |     200      | Invoke 'function' (belonging to uninstaller module). |
| /integrations/{_id}/settings/{function}    | PUT    |     200      | Invoke 'function' (belonging to settings module).    |

#### Relevant Schema Info

##### Integration

| Field                | Description |
|:---------------------|:------------|
| **_id**              | .           |
| **name**             | .           |
| **tag**              | .           |
| **lastModified**     | .           |
| **_connectorId**     | .           |
| **mode**             | .           |
| **install**          | .           |
| **version**          | .           |
| **settings**         | .           |
| **updateInProgress** | .           |

Connector
---------

##### What is a Connector?

Connectors represent fully functional pre-built integrations that any user can install into their Integrator account, directly from the Integrator Marketplace. Connectors include an installer, uninstaller, and a settings interface. Connector developers can push updates at any time for their Connector to their entire install base. Connectors are mostly made up of code (that subsequently uses the Integrator API to interact with a user's account). A Connector can create any number of components in a user's account. All components created in a user's account are tagged with the Connector's \_id (via the \_connectorId field). A Connector can only modify components tagged with its own \_id. The schema below mostly serves the purpose of listing a Connector in the Integrator Marketplace (i.e. description, imageURL, websiteURL, etc...).

##### Connector Related HTTP Endpoints

| Relative URI                  | Method | Success Code | Description                                                            |
|:------------------------------|:-------|:------------:|:-----------------------------------------------------------------------|
| /connectors                   | GET    |     200      | List all connectors.                                                   |
| /connectors                   | POST   |     201      | Create new connector.                                                  |
| /connectors/{_id}             | PUT    |     200      | Update existing connector.                                             |
| /connectors/{_id}             | GET    |     200      | Retrieve existing connector.                                           |
| /connectors/{_id}             | DELETE |     204      | Delete existing connector.                                             |
| /connectors/{_id}/installBase | GET    |     200      | Retrieve information related to install base for given connector \_id. |
| /connectors/{_id}/update      | PUT    |     200      | Push update to _integrationIds[] provided in the request body.         |

#### Relevant Schema Info

##### Connector

| Field                   | Description |
|:------------------------|:------------|
| **_id**                 | .           |
| **name**                | .           |
| **handle**              | .           |
| **lastModified**        | .           |
| **published**           | .           |
| **_integrationId**      | .           |
| **description**         | .           |
| **imageURL**            | .           |
| **websiteURL**          | .           |
| **installerFunction**   | .           |
| **uninstallerFunction** | .           |
| **updateFunction**      | .           |

IClient
-------

Job
---

Job Error
---------

Retry
-----
