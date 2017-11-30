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
|/connections/{_id}/import|POST|200|Execute a virtual import using a connection identified by _id by posting the import model to this endpoint.|
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

#### 3.  Export data out of an external application using a virtual export, with paging (page size 5).

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
            "custrecord_usage_diy_phone": "614-286-9031",
            "custrecord_usage_diy_ms": 65894782,
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_company": "Socius1",
            "custrecord_usage_diy_userid": "59bc3e4d2561c707bd1d39e1",
            "custrecord_usage_diy_name": "Matthew Holden",
            "custrecord_usage_diy_hours": 18,
            "custrecord_usage_diy_role": "Consultant",
            "custrecord_usage_diy_email": "mholden@socius1.com",
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2016-11-22T12:26:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-06-22T07:00:00.000+00:00",
            "custrecord_usage_diy_phone": "6102340512",
            "custrecord_usage_diy_ms": 44439899,
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_company": "Pet360",
            "custrecord_usage_diy_userid": "58349ead27b52153647f4810",
            "custrecord_usage_diy_name": "Nicole Jimenez",
            "custrecord_usage_diy_hours": 12,
            "custrecord_usage_diy_role": "Technology",
            "custrecord_usage_diy_email": "njimenez@pet360.com",
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2017-11-13T01:31:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-12-13T08:00:00.000+00:00",
            "custrecord_usage_diy_phone": "+78124632456",
            "custrecord_usage_diy_ms": 33199382,
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_company": "Zodiac TV",
            "custrecord_usage_diy_userid": "5a0963e69b897a162637f589",
            "custrecord_usage_diy_name": "Peter Glebovskiy",
            "custrecord_usage_diy_hours": 9,
            "custrecord_usage_diy_role": "Manager",
            "custrecord_usage_diy_email": "petr.glebovskiy@dev.zodiac.tv",
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2016-03-02T16:20:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-08-01T07:00:00.000+00:00",
            "custrecord_usage_diy_ms": 24450156,
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_userid": "56d7529b00cc8ed70dd4f0c9",
            "custrecord_usage_diy_name": "Ko Kiuchi",
            "custrecord_usage_diy_hours": 7,
            "custrecord_usage_diy_email": "kkiuchi@luxurylane.com",
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2016-12-20T04:26:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-12-31T08:00:00.000+00:00",
            "custrecord_usage_diy_ms": 20146954,
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_company": "Velasca",
            "custrecord_usage_diy_userid": "5858f2b08975b829ea8e164d",
            "custrecord_usage_diy_name": "Marco Lacchini",
            "custrecord_usage_diy_hours": 6,
            "custrecord_usage_diy_email": "marco.lacchini@velasca.com",
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
        "searchId": "WEBSERVICES_589861_11292017182391545425518660_73666d20757aff",
        "pageIndex": 2,
        "pageSize": 5,
        "totalPages": 165,
        "totalRecords": 822,
        "done": false,
        "issuedAt": 1512010880,
        "sig": "ac7463cf6160937468e5cc575f541d438763719f5985c0ee85642e0de4d75d0d"
    }
}
```

#### 4.  Building on example #3, let's get the next page of data for the same virtual export by including the exact pagedExportState obj that was returned by the first API response.  And then of course to get the next next page after this one you would just submit the pagedExportState returned by this API response.

```
POST /v1/connections/5541489353bb53af29000009/export/pages HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "pagedExportState": {
        "searchId": "WEBSERVICES_589861_11292017182391545425518660_73666d20757aff",
        "pageIndex": 2,
        "pageSize": 5,
        "totalPages": 165,
        "totalRecords": 822,
        "done": false,
        "issuedAt": 1512010880,
        "sig": "ac7463cf6160937468e5cc575f541d438763719f5985c0ee85642e0de4d75d0d"
    },
    "export": {
    	"asynchronous": true,
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
            "Trial Start": "2017-06-14T09:02:00.000-07:00",
            "custrecord_license2_trial_end_date": "2017-09-30T07:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 4,
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2016-03-09T08:18:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-09-02T07:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 2,
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2016-11-17T12:25:00.000-08:00",
            "custrecord_license2_trial_end_date": "2017-12-04T08:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 2,
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2017-10-31T11:30:00.000-07:00",
            "custrecord_license2_trial_end_date": "2017-12-02T08:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 1,
            "custrecord_usage_diy_month_s": "November"
        },
        {
            "recType": "813",
            "Trial Start": "2017-09-16T16:02:00.000-07:00",
            "custrecord_license2_trial_end_date": "2017-12-25T08:00:00.000+00:00",
            "custrecord_usage_diy_year": 2017,
            "custrecord_usage_diy_hours": 1,
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
        "searchId": "WEBSERVICES_589861_11292017182391545425518660_73666d20757aff",
        "pageIndex": 3,
        "pageSize": 5,
        "totalPages": 165,
        "totalRecords": 822,
        "done": false,
        "issuedAt": 1512010952,
        "sig": "de523935aa3831a8d4ab49e788b22ba5c1be021a60db597b7c5a0033a669af9f"
    }
}
```

#### 5.  Import 4 records into an external application using a virtual import.  The 'import' obj in the request defines how the data should be imported, and the 'data' array contains all the records that you want to import.
```
POST /v1/connections/5a1498b3bc227369ab2aa77b/import HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "import": {
        "mapping": {
            "fields": [
                {
                    "extract": "timestamp",
                    "generate": "timestamp"
                },
                {
                    "generate": "key",
                    "hardCodedValue": "sqs_flow"
                }
            ]
        },
        "rest": {
            "relativeURI": [
                "/system/v1/heartbeat"
            ],
            "method": [
                "PUT"
            ]
        }
    },
    "data": [
    	{"timestamp": 1512012818751}, 
    	{"timestamp": 1512012818751}, 
    	{"timestamp": 1512012818751},
    	{"timestamp": 1512012818751}
    ]
}
```
Sample Response:

```
[
    {
        "statusCode": 200,
        "id": "5a149c86bc227369ab2aaab6",
        "_json": {
            "_id": "5a149c86bc227369ab2aaab6",
            "lastModified": "2017-11-30T03:33:51.703Z",
            "createdAt": "2017-11-21T21:37:10.497Z",
            "key": "sqs_flow",
            "timestamp": "2017-11-30T03:33:38.751Z",
            "__v": 0
        }
    },
    {
        "statusCode": 200,
        "id": "5a149c86bc227369ab2aaab6",
        "_json": {
            "_id": "5a149c86bc227369ab2aaab6",
            "lastModified": "2017-11-30T03:33:51.704Z",
            "createdAt": "2017-11-21T21:37:10.497Z",
            "key": "sqs_flow",
            "timestamp": "2017-11-30T03:33:38.751Z",
            "__v": 0
        }
    },
    {
        "statusCode": 200,
        "id": "5a149c86bc227369ab2aaab6",
        "_json": {
            "_id": "5a149c86bc227369ab2aaab6",
            "lastModified": "2017-11-30T03:33:51.700Z",
            "createdAt": "2017-11-21T21:37:10.497Z",
            "key": "sqs_flow",
            "timestamp": "2017-11-30T03:33:38.751Z",
            "__v": 0
        }
    },
    {
        "statusCode": 200,
        "id": "5a149c86bc227369ab2aaab6",
        "_json": {
            "_id": "5a149c86bc227369ab2aaab6",
            "lastModified": "2017-11-30T03:33:51.701Z",
            "createdAt": "2017-11-21T21:37:10.497Z",
            "key": "sqs_flow",
            "timestamp": "2017-11-30T03:33:38.751Z",
            "__v": 0
        }
    }
]
```
