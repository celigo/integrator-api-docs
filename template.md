Template API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-----------|:-------|:------------:|:-------------|
|/templates|GET|200|Get all templates.|
|/templates/{_id}|GET|200|Get a specific template.|
|/templates|POST|201|Create a new template.|
|/templates/{_id}|PUT|200|Update a specific template.|
|/templates/{_id}|DELETE|204|Delete a specific template.|


## Examples

#### 1.  Get a specific template.

```
GET /v1/template/{_id} HTTP/1.1
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
