Connection API
==========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/connections|GET|200|Get all connections.|
|/connections/{_id}|GET|200|Get a specific connection.|
|/connections|POST|201|Create a new connection.|
|/connections/{_id}|PUT|200|Update a specific connection.|
|/connections/{_id}|DELETE|204|Delete a specific connection.|
|/connections/{_id}/ping|GET|200|Test that a specific connection is operational.|
|/connections/ping|POST|200|Test a virtual connection where all details for the connection are submitted in the POST body, but should not be saved.|
|/connections/{_id}/debug/{resourceId}|GET|200|Get debug logs for a given connection by Id. Use Optional resourceId to filter logs by a specific import or export.|
|/connections/{_id}/export|POST|200|Execute a 'virtual' export using a connection identified by id by posting the export model to this endpoint.|
|/connections/{_id}/export/pages|POST|200|Alternative virtual export execution with paging support.|
|/connections/{_id}/import|POST|200|Execute virtual import for a specific connection.|
|/connections/{_id}/import/map|POST|200|Exaluate only the mapping step in a virtual import and return the set of post-mapped records.|

## Examples

#### 1.  Get a specific connection.

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

#### 2.  Create a connection.

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
