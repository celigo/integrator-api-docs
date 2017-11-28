Import API
==========
>**Guidance**: this API is typically only needed for SmartConnector development where you need to create imports in your installer, or you need to dynamically configure imports based on settings that you expose in your app.

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/imports|GET|200|Get all imports.|
|/imports/{_id}|GET|200|Get a specific import.|
|/imports|POST|201|Create a new import.|
|/imports/{_id}|PUT|200|Update a specific import.|
|/imports/{_id}|DELETE|204|Delete a specific import.|

## Examples

#### 1.  Get a specific import.

```
GET /v1/imports/594d5058c5a4c41a4ba4b662 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "_id": "594d5058c5a4c41a4ba4b662",
    "lastModified": "2017-11-28T18:38:19.851Z",
    "name": "Upload File to FTP Site",
    "_connectionId": "56fd5ad09aa1825c4e43077d",
    "distributed": false,
    "apiIdentifier": "ib8f75140f",
    "file": {
        "skipAggregation": false,
        "type": "csv",
        "csv": {
            "rowDelimiter": "\n",
            "columnDelimiter": ",",
            "includeHeader": false,
            "wrapWithQuotes": false,
            "replaceTabWithSpace": false,
            "replaceNewlineWithSpace": false
        }
    },
    "ftp": {
        "directoryPath": "/test",
        "fileName": "file-{{timestamp}}.csv"
    }
}
```

#### 2.  Update the name field for a specific import.

```
PUT /v1/imports/594d5058c5a4c41a4ba4b662 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "_id": "594d5058c5a4c41a4ba4b662",
    "lastModified": "2017-06-23T17:31:04.446Z",
    "name": "Upload File to FTP Site (updated!)",
    "_connectionId": "56fd5ad09aa1825c4e43077d",
    "distributed": false,
    "apiIdentifier": "ib8f75140f",
    "file": {
        "skipAggregation": false,
        "type": "csv",
        "lookups": [],
        "csv": {
            "rowDelimiter": "\n",
            "columnDelimiter": ",",
            "includeHeader": false,
            "wrapWithQuotes": false,
            "replaceTabWithSpace": false,
            "replaceNewlineWithSpace": false
        }
    },
    "ftp": {
        "directoryPath": "/test",
        "fileName": "file-{{timestamp}}.csv"
    }
}
```

Sample Response:

```
{
    "_id": "594d5058c5a4c41a4ba4b662",
    "lastModified": "2017-11-28T18:47:09.553Z",
    "name": "Upload File to FTP Site (updated!)",
    "_connectionId": "56fd5ad09aa1825c4e43077d",
    "distributed": false,
    "apiIdentifier": "ib8f75140f",
    "file": {
        "skipAggregation": false,
        "type": "csv",
        "csv": {
            "rowDelimiter": "\n",
            "columnDelimiter": ",",
            "includeHeader": false,
            "wrapWithQuotes": false,
            "replaceTabWithSpace": false,
            "replaceNewlineWithSpace": false
        }
    },
    "ftp": {
        "directoryPath": "/test",
        "fileName": "file-{{timestamp}}.csv"
    }
}
```
