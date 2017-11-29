Connection API
==========
>**Guidance**: this API is typically only needed for Embedded Integration development where you want to expose a form in your application's UI to collect a user's credentials to the application being integrated, and then once their credentials are stored in integrator.io you can then use the larger integrator.io API to enable any sort of integration with that application.

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
|/connections/{_id}/export|POST|200|Execute a virtual export using a connection identified by _id by posting the export model to this endpoint.|
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

#### 3.  Export data out of an external application using a virtual export, with paging (page size 8).

```
POST /v1/connections/5541489353bb53af29000009/export/pages HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "export": {
    	"asynchronous": true,  // deprecating this field soon, but for now please set to true always.
    	"pageSize": 5,
    	"netsuite": {
        	"type": "search",
        	"searches": [{
              "recordType": "customRecord",
              "savedSearchId": "131298"
            }],
            "skipGrouping": true
    	}
    }
}
```
Sample Response:

```
{
    "data": [
        {
            "recType": "813",
            "Trial Start": "2017-09-15T14:02:00.000-07:00",
            "custrecord_license2_trial_end_date": "2017-10-19T07:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 18,
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2016-11-22T12:26:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-06-22T07:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 12,
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2017-11-13T01:31:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-12-13T08:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 9,
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2016-03-02T16:20:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-08-01T07:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 7,
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2016-12-20T04:26:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-12-31T08:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 6,
            "custrecord_usage_diy_month_s": "November"
        }
    ],
    "dataURIs": [
        null,
        null,
        null,
        null,
        null
    ],
    "pagedExportState": {
        "searchId": "WEBSERVICES_589861_112920171426266131263300914_3f26f3b32b360",
        "pageIndex": 2,
        "pageSize": 5,
        "totalPages": 165,
        "totalRecords": 821,
        "done": false,
        "issuedAt": 1511971115,
        "sig": "c796d1be0d905c184eacbc2ce06679f6d3ecab158bc88fc976516a1c6fe5af4b"
    }
}
```
