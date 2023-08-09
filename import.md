Import API
==========
>**Guidance**: this API is typically only needed for SmartConnector development (where you need to create imports in your installer, or dynamically configure imports based on settings that you expose in your app), or for Embedded Integration development (where you want to automate deploying your imports via cloning or dynamically creating them from scratch).


### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/imports|GET|200|Get all imports.|
|/imports/{_id}|GET|200|Get a specific import.|
|/imports|POST|201|Create a new import.|
|/imports/{_id}|PUT|200|Update a specific import.|
|/imports/{_id}|DELETE|204|Delete a specific import.|
|/imports/{_id}/clone|POST|201|Clone a specific import.|

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
    "_id": "123ab456c789de1011fg12h13i",
    "lastModified": "2017-11-28T18:38:19.851Z",
    "name": "Upload File to FTP Site",
    "_connectionId": "56fd5ad09aa1825c4e678aca76",
    "distributed": false,
    "apiIdentifier": "ib8f75411f4",
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
    },
    "mapping": {
        "fields": [
            {
                "extract": "internal_id",
                "generate": "id"
            },
            {
                "extract": "item_id",
                "generate": "sku"
            },
            {
                "extract": "name",
                "generate": "name"
            },
            {
                "extract": "sales_description",
                "generate": "description"
            },
            {
                "extract": "rate",
                "generate": "price"
            },
            {
                "extract": "qty_on_hand",
                "generate": "qty"
            }
        ]
    }
}
```

#### 2.  Update a specific import to change the name field and also one of the field mappings.

```
PUT /v1/imports/594d5058c5a4c41a4ba4b662 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "_id": "123ab456c789de1011fg12h13i",
    "lastModified": "2017-06-23T17:31:04.446Z",
    "name": "Upload File to FTP Site (updated!)",
    "_connectionId": "56fd5ad09aa1825c4e678aca76",
    "distributed": false,
    "apiIdentifier": "ib8f75411f4",
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
    },
    "mapping": {
        "fields": [
            {
                "extract": "internal_id",
                "generate": "updated_id"
            },
            {
                "extract": "item_id",
                "generate": "sku"
            },
            {
                "extract": "name",
                "generate": "name"
            },
            {
                "extract": "sales_description",
                "generate": "description"
            },
            {
                "extract": "rate",
                "generate": "price"
            },
            {
                "extract": "qty_on_hand",
                "generate": "qty"
            }
        ]
    }
}
```

Sample Response:

```
{
    "_id": "123ab456c789de1011fg12h13i",
    "lastModified": "2017-11-28T18:47:09.553Z",
    "name": "Upload File to FTP Site (updated!)",
    "_connectionId": "56fd5ad09aa1825c4e678aca76",
    "distributed": false,
    "apiIdentifier": "ib8f75411f4",
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
    },
    "mapping": {
        "fields": [
            {
                "extract": "internal_id",
                "generate": "updated_id"
            },
            {
                "extract": "item_id",
                "generate": "sku"
            },
            {
                "extract": "name",
                "generate": "name"
            },
            {
                "extract": "sales_description",
                "generate": "description"
            },
            {
                "extract": "rate",
                "generate": "price"
            },
            {
                "extract": "qty_on_hand",
                "generate": "qty"
            }
        ]
    }
}
```
