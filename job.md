Job API
===========
>**Guidance**: there are lots of use cases for using this API.  For example, you might want to extract stats about running integrations, and submit those stats to an anlytics platform.  Or, maybe you want to extract all your errors and process them in an external database.  The integrator.io UI is always evolving to better support these same use cases too, but if/when the UI falls short then this API is a nice fallback to get at the raw data. 


### Endpoints
| Relative URI| Method | Success Code | Description|
|:-----------|:-------|:------------:|:-------------|
|/jobs|GET|200|Get all jobs.  The following query parameters are supported: _integrationId, _exportId, _importId, _flowId, _flowJobId, _bulkJobId, _flowId_in, createdAt_lte, createdAt_gte, status, numSuccess_lte, numSuccess_gte, numExport_lte, numExport_gte, numIgnore_lte, numIgnore_gte, retriable, type, and type_in|
|/jobs/{_id}|GET|200|Get a specific job.|
|/jobs/{_id}/joberrors|GET|200|If a job has <= 1000 errors use this API to retrieve the errors directly.|
|/jobs/{_id}/errorFile/signedURL|GET|200|If a job has > 1000 errors, or you prefer to get a file, use this API to get a signed URL that can then be used to download an error file directly from S3.|


## Examples

#### 1.  Get all jobs where type = 'import' and status = 'running'.

```
GET /v1/jobs?type=import&status=running HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
[
    {
        "_id": "123ab456c789de1011fg12h13i",
        "type": "import",
        "_importId": "55ea78c546cf5825294aca0b",
        "_flowJobId": "59ee8f82c41g45g165ef1b9a9",
        "startedAt": "2017-10-24T00:55:49.128Z",
        "status": "running",
        "numError": 0,
        "numSuccess": 1,
        "numIgnore": 0,
        "numPagesProcessed": 1,
        "oIndex": 1,
        "retriable": false,
        "createdAt": "2017-10-24T00:55:49.128Z",
        "lastModified": "2017-10-24T00:55:49.642Z"
    }
]
```

#### 2.  Get job errors for a specific import job.

```
GET /v1/jobs/59ee8cad03785d04d8469918/jobErrors HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:
```
[
    {
        "resolve": false,
        "retry": false,
        "createdAt": "2017-10-24T00:43:30.434Z",
        "code": "422",
        "extract": "",
        "generate": "",
        "extracted": "",
        "generated": "",
        "message": "'{\"errors\":[{\"field\":\"expires\",\"code\":\"missing_required_field\"}]}'",
        "source": "integrator.io",
        "exportDataURI": "",
        "importDataURI": "",
        "_retryId": "59ee8cb20df374sf469sd919",
        "_jobId": "59ee8cad03784g7hd8469918"
    }
]
```
