Flow API
==========
>**Guidance**: this API is typically only needed for SmartConnector development (where you need to create flows in your installer, or dynamically configure flows based on settings that you expose in your app), or for Embedded Integration development (where you want to automate deploying integration flows via cloning from a master copy or dynamically creating them from scratch)

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/flows|GET|200|Get all flows.|
|/flows/{_id}|GET|200|Get a specific flow.|
|/flows|POST|201|Create a new flow.|
|/flows/{_id}|PUT|200|Update a specific flow.|
|/flows/{_id}|DELETE|204|Delete a specific flow.|
|/flows/{_id}/clone|POST|201|Clone a specific flow.|


## Examples

#### 1.  Get a specific simple flow (i.e. a flow with just a single export and import).

```
GET /v1/flows/55ea78c6b751843c37b7b367 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "_id": "55ea78c6b751843c37b7b367",
    "lastModified": "2017-06-19T21:27:09.945Z",
    "name": "Send GitHub Events to Slack",
    "disabled": false,
    "timezone": "America/Los_Angeles",
    "_exportId": "55ea776bb751843c37b7b366",
    "_importId": "55ea78c546cf5825294bad14",
    "_integrationId": "58f107a36b3bdd4f8723d1bc",
    "skipRetries": false,
    "createdAt": "2017-06-19T21:27:09.900Z"
}
```

#### 2.  Get a specific orchestrated flow (i.e. a flow with multiple exports and imports linked together).

```
GET /v1/flows/5980e7fedd9939649f8a5d9a HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "_id": "5980e7fedd9939649f8a5d9a",
    "lastModified": "2017-08-19T17:04:54.005Z",
    "name": "Update Usage Stats for all Trialers",
    "disabled": false,
    "schedule": "? 0 2 ? * *",
    "timezone": "America/Los_Angeles",
    "_integrationId": "5937116e45696471856f874e",
    "skipRetries": false,
    "pageProcessors": [
        {
            "type": "export",
            "_exportId": "5980f1d5dd9939649f8a6428",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "numConnections"
                    }
                ]
            }
        },
        {
            "type": "export",
            "_exportId": "598153787ed11d467c44dd32",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "dlSuccessAndErrors"
                    }
                ]
            }
        },
        {
            "type": "export",
            "_exportId": "598154724a260022fde4d6c6",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "lastSignIn"
                    }
                ]
            }
        },
        {
            "type": "export",
            "_exportId": "598153484a260022fde4d638",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "numDataLoaders"
                    }
                ]
            }
        },
        {
            "type": "export",
            "_exportId": "598155a37ed11d467c44de0c",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "numFlows"
                    }
                ]
            }
        },
        {
            "type": "export",
            "_exportId": "59815b662207dd5f4fdb354b",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "numPasswordChanges"
                    }
                ]
            }
        },
        {
            "type": "export",
            "_exportId": "598155c97ed11d467c44de22",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "numPasswordResets"
                    }
                ]
            }
        },
        {
            "type": "export",
            "_exportId": "598155e77ed11d467c44de29",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "numSignIns"
                    }
                ]
            }
        },
        {
            "type": "export",
            "_exportId": "598156037ed11d467c44de37",
            "responseMapping": {
                "lists": [],
                "fields": [
                    {
                        "extract": "data",
                        "generate": "successAndErrors"
                    }
                ]
            }
        },
        {
            "type": "import",
            "_importId": "5980f529dd9939649f8a6615",
            "proceedOnFailure": true,
            "responseMapping": {
                "lists": [],
                "fields": []
            }
        },
        {
            "type": "import",
            "_importId": "5981765d1b6e553ad2edd9f9",
            "responseMapping": {
                "lists": [],
                "fields": []
            }
        }
    ],
    "pageGenerators": [
        {
            "_exportId": "5980ece14a260022fde4a286",
            "_id": "59986fb5833c1211526a7b36"
        },
        {
            "_exportId": "5981fb4b849b2c1c8c3e36dd",
            "_id": "59986fb5833c1211526a7b35"
        }
    ],
    "createdAt": "2017-08-01T20:43:42.156Z"
}
```
