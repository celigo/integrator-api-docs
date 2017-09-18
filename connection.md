Connection
==========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/connections|GET|200|Get all connections.|
|/connections|POST|201|Create a new connection.|
|/connections/{_id}|GET|200|Get a specific connection.|
|/connections/{_id}|PUT|200|Update a specific connection.|
|/connections/{_id}|DELETE|204|Delete a specific connection.|
|/connections/{_id}/ping|GET|200|Test that a specific connection is operational.|
|/connections/ping|POST|200|Test a virtual connection where all details for the connection are submitted in the POST body, but should not be saved.|
|/connections/{_id}/debug|GET|200|@TODO|
|/connections/{_id}/debug/:typeId|GET|200|@TODO|
|/connections/{_id}/proxy|POST|200|@TODO|
|/connections/{_id}/export|POST|200|@TODO|
|/connections/{_id}/export/pages|POST|200|@TODO|
|/connections/{_id}/import|POST|200|@TODO|
|/connections/{_id}/import/map|POST|200|@TODO|

### Examples

# Get a specific connection.

```
GET /v1/connections/{_id} HTTP/1.1
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

# Create a connection.

```
POST /v1/connections HTTP/1.1
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
