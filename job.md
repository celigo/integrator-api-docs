Job API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-----------|:-------|:------------:|:-------------|
|/jobs|GET|200|Get all jobs.  The following query parameters are supported. _integrationId, _exportId, _importId, _flowId, _flowJobId, _bulkJobId, _flowId_in, createdAt_lte, createdAt_gte, status, numSuccess_lte, numSuccess_gte, numExport_lte, numExport_gte, numIgnore_lte, numIgnore_gte, retriable, type, type_in|
|/jobs/{_id}/joberrors|GET|200|If a job has <= 1000 errors use this API to retrieve the errors directly.|
|/jobs/{_id}/errorFile/signedURL|GET|200|If a job has > 1000 errors, or you prefer to get a file, use this API to get a signed URL that can then be used to download an error file directly from S3.|


## Examples

#### 1.  Get all jobs.

```
GET /v1/jobs/{_id} HTTP/1.1
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
