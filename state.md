State API
========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/state|GET|200|Get all keys.|
|/state/{key}|GET|200|Get a specific value.|
|/state/{key}|PUT|200|Create/update a specific value.|
|/state/{key}|DELETE|204|Delete a specific value.|
|/state|DELETE|204|Delete all values.|
|/{resource_type}/{_id}/state|GET|200|Get all keys associated with a specific resource in your account.|
|/{resource_type}/{_id}/state/{key}|GET|200|Get a specific value associated with a specific resource in your account.|
|/{resource_type}/{_id}/state/{key}|PUT|200|Create/update a specific value associated with a specific resource in your account.|
|/{resource_type}/{_id}/state/{key}|DELETE|200|Delete a specific value associated with a specific resource in your account.|
|/{resource_type}/{_id}/state|DELETE|200|Delete all values associated with a specific resource in your account.|

## Examples

#### 1.  Get state for key.

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
