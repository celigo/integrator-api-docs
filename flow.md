Flow API
==========
>**Guidance**: this API is typically only needed for SmartConnector development where you are installing flows via your installer, or you are dynamically configuring flows based on the settings that you expose in your SmartConnector app.

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/flows|GET|200|Get all flows.|
|/flows/{_id}|GET|200|Get a specific flow.|
|/flows|POST|201|Create a new flow.|
|/flows/{_id}|PUT|200|Update a specific flow.|
|/flows/{_id}|DELETE|204|Delete a specific flow.|

## Examples

#### 1.  Get a specific flow.

```
GET /v1/flows/{_id} HTTP/1.1
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

#### 2.  Create an flow.

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
