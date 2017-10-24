Job API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-----------|:-------|:------------:|:-------------|
|/jobs|GET|200|Get all jobs.  The following query parameters are supported. _integrationId, _exportId, _importId, _flowId, _flowJobId, _bulkJobId, _flowId_in, createdAt_lte, createdAt_gte, status, numSuccess_lte, numSuccess_gte, numExport_lte, numExport_gte, numIgnore_lte, numIgnore_gte, retriable, type, type_in|
|/jobs/{_id}|GET|200|Get a specific job.|
|/jobs/{_id}/joberrors|GET|200|If a job has <= 1000 errors use this API to retrieve the errors directly.|
|/jobs/{_id}/errorFile/signedURL|GET|200|If a job has > 1000 errors, or you prefer to get a file, use this API to get a signed URL that can then be used to download an error file directly from S3.|


## Examples

#### 1.  Get all jobs of type 'flow' that are currently 'running'.

```
GET /v1/jobs?type=flow&status=running HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
[
    {
        "_id": "59ee88c8e41d93365ef1adad",
        "type": "flow",
        "_integrationId": "55402ef64de3779c540000ab",
        "_flowId": "537d230f7ab1870200000001",
        "startedAt": "2017-10-24T00:27:19.259Z",
        "status": "running",
        "doneExporting": false,
        "createdAt": "2017-10-24T00:26:48.120Z",
        "lastModified": "2017-10-24T00:27:19.261Z"
    },
    {
        "_id": "59ee88c8e41d93365ef1adad",
        "type": "flow",
        "_integrationId": "55402ef64de3779c540000ab",
        "_flowId": "537d230f7ab1870200000004",
        "startedAt": "2017-10-24T00:27:19.259Z",
        "status": "running",
        "numError": 0,
        "numSuccess": 1,
        "numIgnore": 0,
        "numPagesExported": 1,
        "doneExporting": true,
        "createdAt": "2017-10-24T00:26:48.120Z",
        "lastModified": "2017-10-24T00:27:24.842Z"
    }
]
```
