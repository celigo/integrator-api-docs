State
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/state|GET|200|Get all state resources.|
|/state/{key}|GET|200|Get a specific state resource.|
|/state/{key}|PUT|200|Create/update a specific state resource.|
|/state/{key}|DELETE|204|Delete a specific state resource.|


## Examples

#### 1.  Get state for specific key.

```
GET /v1/state/{key} HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "myField1": "",
  "myField2":"*"
}
```
