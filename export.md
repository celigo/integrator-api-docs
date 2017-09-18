Export
==========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/exports|GET|200|Get all exports.|
|/exports/{_id}|GET|200|Get a specific export.|
|/exports|POST|201|Create a new export.|
|/exports/{_id}|PUT|200|Update a specific export.|
|/exports/{_id}|DELETE|204|Delete a specific export.|
|/exports/preview|POST|200|Invoke a virtual export where all the details for the export are submitted in the POST body, but should not be saved.|
|/exports/:_id/distributed|GET|200||
|/exports/:_id/distributed|GET|200||

## Examples

#### 1.  Get a specific export.

```
GET /v1/exports/{_id} HTTP/1.1
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

#### 2.  Create an export.

```
POST /v1/exports HTTP/1.1
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
