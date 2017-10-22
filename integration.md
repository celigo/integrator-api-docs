Integration API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/integrations|GET|200|Get all integrations.|
|/integrations/{_id}|GET|200|Get a specific integration.|
|/integrations|POST|201|Create a new integration.|
|/integrations/{_id}|PUT|200|Update a specific integration.|
|/integrations/{_id}|DELETE|204|Delete a specific integration.|

### SmartConnector Only Fields

| Field| Description|
|:------------|:------------|
| **mode**|???|
| **settings**|???|
| **version**|???|
| **tag**|???|
| **updateInProgress**|???|
| **install**|???|

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
