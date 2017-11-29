Connection API
==========
>**Guidance**: this API is typically only needed for Embedded Integration development where you want to expose a form in your application's UI to collect a user's credentials to the application being integrated, and then use the virtual export and import APIs below to sync data with that integrated application.

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/connections|GET|200|Get all connections.|
|/connections/{_id}|GET|200|Get a specific connection.|
|/connections|POST|201|Create a new connection.|
|/connections/{_id}|PUT|200|Update a specific connection.|
|/connections/{_id}|DELETE|204|Delete a specific connection.|
|/connections/{_id}/ping|GET|200|Test that a specific connection is operational.|
|/connections/ping|POST|200|Test a virtual connection where all details for the connection are submitted in the POST body, but are not saved.|
|/connections/{_id}/debug/{_resourceId}|GET|200|Get debug logs for a given connection by _id. Use optional _resourceId to filter logs by a specific import or export.|
|/connections/{_id}/export|POST|200|Execute a 'virtual' export using a connection identified by _id by posting the export model to this endpoint.|
|/connections/{_id}/export/pages|POST|200|Alternative virtual export execution with paging support.|
|/connections/{_id}/import|POST|200|Execute virtual import for a specific connection.|
|/connections/{_id}/import/map|POST|200|Evaluate only the mapping step in a virtual import and return the set of post-mapped records.|

## Examples

#### 1.  Get a specific connection.

```
GET /v1/connections/5541e17a1383a6ce6e00000d HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "_id": "5541e17a1383a6ce6e00000d",
    "lastModified": "2015-05-02T05:13:43.736Z",
    "type": "rest",
    "name": "Slack API",
    "rest": {
        "baseURI": "https://slack.com/api",
        "bearerToken": "******",
        "tokenLocation": "url",
        "tokenParam": "token",
        "mediaType": "urlencoded",
        "authType": "token",
        "encryptedFields": [],
        "unencryptedFields": [],
        "scope": [],
        "pingRelativeURI": "api.test"
    },
    "queues": [
        {
            "name": "5541e17a1383a6ce6e00000d",
            "size": 8
        }
    ]
}
```

#### 2.  Ping a specific connection.

```
GET /v1/connections/5541e17a1383a6ce6e00000d/ping HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "code": 200
}
```
