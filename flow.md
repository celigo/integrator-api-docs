Flow API
==========
>**Guidance**: this API is typically only needed for SmartConnector development (where you need to create flows in your installer, or dynamically configure flows based on settings that you expose in your app), or for Embedded Integration development (where you want to automate deploying your flows via cloning or dynamically creating them from scratch).

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/flows|GET|200|Get all flows.|
|/flows/{_id}|GET|200|Get a specific flow.|
|/flows|POST|201|Create a new flow.|
|/flows/{_id}|PUT|200|Update a specific flow.|
|/flows/{_id}|DELETE|204|Delete a specific flow.|
|/flows/{_id}/clone|POST|201|Clone a specific flow.|
|/flows/{_id}/run|POST|201|Run a specific flow on-demand.|


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
    "_id": "123ab456c789de1011fg12h13i",
    "lastModified": "2017-06-19T21:27:09.945Z",
    "name": "Send GitHub Events to Slack",
    "disabled": false,
    "timezone": "America/Los_Angeles",
    "_exportId": "55ea776bb751843c37b7ac6ab0",
    "_importId": "55ea78c546cf5825294aca0b",
    "_integrationId": "58f107a36b3bdd4f87aca76b",
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
    "_id": "123ab456c789de1011fg12h13i",
    "lastModified": "2017-08-19T17:04:54.005Z",
    "name": "Update Usage Stats for all Trialers",
    "disabled": false,
    "schedule": "? 0 2 ? * *",
    "timezone": "America/Los_Angeles",
    "_integrationId": "58f107a36b3bdd4f87aca76b",
    "skipRetries": false,
    "pageProcessors": [
        {
            "type": "export",
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_exportId": "55ea776bb751843c37b7ac6ab0",
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
            "_importId": "55ea776bb751843c37b7ac6ab0",
            "proceedOnFailure": true,
            "responseMapping": {
                "lists": [],
                "fields": []
            }
        },
        {
            "type": "import",
            "_importId": "55ea776bb751843c37b7ac6ab0",
            "responseMapping": {
                "lists": [],
                "fields": []
            }
        }
    ],
    "pageGenerators": [
        {
            "_exportId": "55ea776bb751843c37b7ac6ab0",
            "_id": "123ab456c789de1011fg12h13i"
        },
        {
            "_exportId": "55ea776bb751843c37b7ac6ab0",
            "_id": "123ab456c789de1011fg12h13i"
        }
    ],
    "createdAt": "2017-08-01T20:43:42.156Z"
}
```
