State API
========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/state|GET|200|Get all state keys.|
|/state/{key}|GET|200|Get a specific state value.|
|/state/{key}|PUT|200|Create/update a specific state value.|
|/state/{key}|DELETE|204|Delete a specific state value.|
|/state/{key}|DELETE|204|Delete all state values.|
|/{resource_type}/{_id}/state|GET|200|Get all keys associated with a resource in your account.|
|/{resource_type}/{_id}/state/{key}|GET|200|Get a specific state value associated with a resource in your account.|
|/{resource_type}/{_id}/state/{key}|PUT|200|Create/update a specific state value associated with a resource in your account.|
|/{resource_type}/{_id}/state/{key}|DELETE|200|Delete a specific state value associated with a resource in your account.|
|/{resource_type}/{_id}/state|DELETE|200|Delete all state values associated with a resource in your account.|

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
