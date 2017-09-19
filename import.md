Import
==========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/imports|GET|200|Get all imports.|
|/imports/{_id}|GET|200|Get a specific import.|
|/imports|POST|201|Create a new import.|
|/imports/{_id}|PUT|200|Update a specific import.|
|/imports/{_id}|DELETE|204|Delete a specific import.|
|/imports/:_importId/map|POST|200||

## Examples

#### 1.  Get a specific import.

```
GET /v1/imports/{_id} HTTP/1.1
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

#### 2.  Create an import.

```
POST /v1/imports HTTP/1.1
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
