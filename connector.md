Connector API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-----------|:-------|:------------:|:-------------|
|/connectors|GET|200|Get all connectors.|
|/connectors/{_id}|GET|200|Get a specific connector.|
|/connectors|POST|201|Create a new connector.|
|/connectors/{_id}|PUT|200|Update a specific connector.|
|/connectors/{_id}|DELETE|204|Delete a specific connector.|
|/connectors/{_id}/installBase|GET|200|Get the install base for a specific connector.|
|/connectors/{_id}/update|PUT|200|Push update to _integrationIds[].|


## Examples

#### 1.  Get a specific connector.

```
GET /v1/connectors/{_id} HTTP/1.1
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
