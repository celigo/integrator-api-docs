Integration
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/integrations|GET|200|Get all integrations.|
|/integrations/{_id}|GET|200|Get a specific integration.|
|/integrations|POST|201|Create a new integration.|
|/integrations/{_id}|PUT|200|Update a specific integration.|
|/integrations/{_id}|DELETE|204|Delete a specific integration.|

### Schema

| Field                | Required | Description                                                                            |
|:---------------------|:---------|:---------------------------------------------------------------------------------------|
| **_id**|Yes|System generated unique identifier for this integration.|
| **name**|Yes||
| **description**|No||
| **lastModified**|No|Read only field tracking last modified date/time.|
| **_connectorId**|Yes|If this flow belongs to a connector, this value will be hold the _id of that connector.|
| **mode**|No|Field that determines the mode of the integration i.e. install, settings, uninstall|
| **settings**|No|???|
| **version**|No|If this flow belongs to a connector, this value will be hold the id of that connector.|
| **tag**|No|???|
| **updateInProgress**|No|???|
| **install**|No|???|

## Examples

#### 1.  Get a specific integration.

```
GET /v1/integrations/{_id} HTTP/1.1
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
