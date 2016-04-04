Export
======

##### What is an Export?

Exports are used to extract data from an application or system. Exports can run standalone, or in the context of a [Flow](#Flow).

Standalone Exports are useful for integrations where user actions (like clicking a button) need to invoke an external application's API in real-time and return the results immediately. Standalone exports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive. Celigo uses standalone Exports extensively in its spreadsheet and email sync products.

Exports running in the context of a Flow will execute asynchronously and automatically break exported data down into one or more pages and then stream those pages to one or more [Imports](#Import).

##### Export Related HTTP Endpoints

| Relative URI               | Method | Success Code | Description                                                                    |
|:---------------------------|:-------|:------------:|:-------------------------------------------------------------------------------|
| /exports                   | GET    |     200      | List all exports.                                                              |
| /exports                   | POST   |     201      | Create new export.                                                             |
| /exports/{_id}             | PUT    |     200      | Update existing export.                                                        |
| /exports/{_id}             | GET    |     200      | Retrieve existing export.                                                      |
| /exports/{_id}             | DELETE |     204      | Delete existing export.                                                        |
| /exports/{_id}/distributed | PUT    |     200      | Create or update a distributed component that is linked to an existing export. |
| /exports/{_id}/distributed | GET    |     200      | Retrieve a distributed component that is linked to an existing export.         |
| /{apiIdentifier}           | POST   |     200      | Invoke an export.                                                              |
| /exports/preview           | POST   |     200      | Invoke an export in test mode before saving it to preview the results.         |

Below are the common properties in all exports:

| Field                        | Required | Description                                                                                                                                     |
|:-----------------------------|:---------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **_id**                      | Yes      | System generated unique identifier for this connection.                                                                                         |
| **name**                     | No       | Give your export an intuitive name to stay organized.                                                                                           |
| **_connectionId**            | Yes      | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported.      |
| **asynchronous**             | No       | Boolean value to determine the export to be asynchronous or not                                                                                 |
| **type**                     | Yes      | String value to set the type of export. (webhook, distributed, test, delta, once, tranlinedelta, simple)                                        |
| **lastModified**             | No       | Read only field tracking last modified date/time.                                                                                               |
| **apiIdentifier**            | No       | .                                                                                                                                               |
| **_integrationId**           | Yes      | If this connection is part of an integration, this value will hold the id of that integration.                                                  |
| **_connectorId**             | Yes      | If this connection belongs to a connector, this value will be hold the id of that connector.                                                    |
| **pageSize**                 | No       | Number of records to hold in each page                                                                                                          |
| **preserveOrder**            | No       | Boolean value, if set the order of export data is persisted same for the import                                                                 |
| **sampleData**               | No       | Used in UI, which helps in populating data for mapping                                                                                          |
| **test.limit**               | No       | For testing purpose, records exported will be limited to the number specified                                                                   |
| **delta.dateField**          | No       | Field in the data to be exported based on which delta to be considered                                                                          |
| **delta.dateFormat**         | No       | Date format of the field in delta.dateField                                                                                                     |
| **delta.startDate**          | No       | All the records created/modified (based on the dataField) after the specified date in this field will be exported                               |
| **once.booleanField**        | No       | Boolean value, when set the records will be exported only once. The subsequent request for export will fetch the records which are not exported |
| **valueDelta.exportedField** | No       | .                                                                                                                                               |
| **valueDelta.pendingField**  | No       | .                                                                                                                                               |

The following sections are organized by application and adaptor, and ordered by popularity.

### NetSuite Distributed Adaptor Exports

If you are using the Integrator to build NetSuite based integrations, we highly recommend you install our Distributed Adaptor (DA for short). It's a SuiteApp that gets installed as a Bundle directly in your NetSuite account. Once installed, it enables a super powerful export engine that can both export NetSuite data in realtime, intercepting events in NetSuite as they happen, and also run batch based exports that utilize NetSuite's SuiteScript APIs for more advanced and efficient search and custom logic capabilities. Two examples follow. The first outlines the steps required to create a realtime export, and the second does the same for a batch based export.

#### NetSuite Realtime Export

##### First, create your export resource. POST /exports

```javascript
{
  "name": "Realtime Item Export",
  "_connectionId": "5587092ed78128000000000a",
  "type": "distributed",
  "distributed": {
    "bearerToken": "********"
  }
}
```

You should receive a response that includes the following fields. Note that we will use the \_id in the next step.

```javascript
{
  "_id": "507f1f77bcf86cd799439044",
  "apiIdentifier": "e22ef316"
}  
```

##### Second, create your distributed export resource. PUT /exports/507f1f77bcf86cd799439044/distributed

```javascript
{
  "recordType": "salesorder",
  "sublists": ['item', 'shipgroup'],
  "executionContext": ["userinterface", "webservices", "webstore"],
  "qualifier": ['total', '>=', '1000'],
  "hooks": {
    "preSend": {
      "fileInternalId": "1234",
      "function": "myPreSendLogic",
    }
  },
  "settings": {
    "myprop1": "value",
    "myprop2": "value"
  },
  "disabled": false
}
```

#### Relevant Schema Info

##### Export Resource Fields (/exports)

| Field                       | Required | Description                            |
|:----------------------------|:---------|:---------------------------------------|
| **distributed.bearerToken** | Yes      | Token used for authentication purpose. |

##### Distributed Export Resource Fields (/exports/{_id}/distributed)

| Field                            | Required | Description |
|:---------------------------------|:---------|:------------|
| **_id**                          |          | .           |
| **recordType**                   |          | .           |
| **sublists**                     |          | .           |
| **executionContext**             |          | .           |
| **qualifier**                    |          | .           |
| **hooks.preSend.fileInternalId** |          | .           |
| **hooks.preSend.function**       |          | .           |
| **settings**                     |          | .           |
| **disabled**                     |          | .           |

#### NetSuite Batch Export (utilizing a RESTlet in the NetSuite DA)

##### POST /exports

```javascript
{
  "name": "Delta Customer Export",
  "_connectionId": "5587092ed78128000000000a",
  "asynchronous": true,
  "type": "delta",
  "delta": {
    "dateField": "lastmodifieddate"
  },
  "netsuite": {
    "type": "restlet",
    "restlet": {
      "recordType": "customer",
      "searchId": "69422",
      "hooks": {
        "preSend": {
          "fileInternalId": "1234",
          "function": "myPreSendLogic"
        }
      }
    }
  },
  "pageSize": 50
}
```

You should receive a response that includes the following fields.

```javascript
{
  "_id": "507f1f77bcf86cd788439431",
  "apiIdentifier": "e66ec422"
}
```

#### Relevant Schema Info

##### Export Resource Fields (/exports)

| Field                                             | Required | Description                                                                                                                                  |
|:--------------------------------------------------|:---------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| **netsuite.type**                                 | Yes      | This field determines the type of the operation to be performed on the NS account (Ex: search, basicSearch, metadata, selectoption, restlet) |
| **netsuite.searches**                             | No       | If the type is set to 'search' or 'basicSearch' this field holds an array of objects describing the search criteria for the NS records.      |
| **netsuite.metadata**                             | No       | If the type is set to 'metadata'. This is the json path containing the recordType for which the metadata is to be exported                   |
| **netsuite.selectoption**                         | No       | If the type is set to 'selectoption'. This contains the json path for the recordType and fields in the record to be exported.                |
| **netsuite.customFieldMetadata**                  | No       | This field contains the customField name in the NS account for which the metadata is expected                                                |
| **netsuite.skipGrouping**                         | No       | .                                                                                                                                            |
| **netsuite.restlet.recordType**                   | Yes      | Type of the record to be exported.                                                                                                           |
| **netsuite.restlet.searchId**                     | Yes      | Search identifier created in the NS on the record to be exported                                                                             |
| **netsuite.restlet.hooks.preSend.fileInternalId** | .        | .                                                                                                                                            |
| **netsuite.restlet.hooks.preSend.function**       | .        | .                                                                                                                                            |
| **netsuite.basicSearch.bodyFieldsOnly**           | No       | Boolean value.                                                                                                                               |
| **netsuite.basicSearch.pageSize**                 | No       | Page size can be set on the basicSearch results fetched back from the NS.                                                                    |

### NetSuite (Non-Distributed) Adaptor Exports

If installing the Distributed Adaptor in your NetSuite account is not an option, you can still create NetSuite exports that use NetSuite's web services API. The capabilities are slightly more limited and the performance can also be slower depending on the complexity of your exports, especially related to the number of dynamic searches each record will require before export. But, the really nice thing about using the web services API is that you can get started right away, and you do not need to involve a NetSuite admin to install anything first. With that, here is a sample NetSuite web services based export.

##### POST /exports

```javascript
{
  "name": "Delta Customer Export",
  "_connectionId": "5587092ed78128000000000a",
  "asynchronous": true,
  "type": "delta",
  "delta": {
    "dateField": "lastModifiedDate"
  },
  "netsuite": {
    "type": "search",
    "searches": [
      {
        "recordType": "customer",
        "searchId": "613"
      }
    ]
  },
  "pageSize": 50,
  "hooks": {
    "_preSavePageId": "5587092fd78228000000000b"
  }
}
```

You should receive a response that includes the following fields.

```javascript
{
  "_id": "507f1e67bcf99ef788439431",
  "apiIdentifier": "e66ec411"
}
```

#### Relevant Schema Info

##### Export Resource Fields (/exports)

| Field                            | Required | Description                                                                                                                                  |
|:---------------------------------|:---------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| **netsuite.type**                | Yes      | This field determines the type of the operation to be performed on the NS account (Ex: search, basicSearch, metadata, selectoption, restlet) |
| **netsuite.searches.recordType** | .        |                                                                                                                                              |
| **netsuite.searches.searchId**   | .        |                                                                                                                                              |
| **netsuite.skipGrouping**        | .        |                                                                                                                                              |

### REST API Adaptor Exports

The Integrator supports the ability to define exports for any RESTful JSON API. Following is an example.

##### POST /exports

```javascript
{
  "name": "My Export",
  "_connectionId": "5587092ef78228000000000a",
  "asynchronous": true,
  "rest": {
    "relativeURI": "/customers",
    "method": "GET",
    "resourcePath": "customers",
    "pagingMethod": "nextpageurl",
    "nextPagePath": "next_page"
  },
  "pageSize": 20,
  "hooks": {
    "_preSavePageId": "5587092fd78228000f0e001b"
  }
}
```

You should receive a response that includes the following fields.

##### Sample Export Response

```javascript
{
  "_id": "507f1f77bcf86cd799439011",
  "apiIdentifier": "e53ec313",
}
```

#### Relevant Schema Info

##### Export Resource Fields (/exports)

| Field                        | Required | Description                                                                                                                             |
|:-----------------------------|:---------|:----------------------------------------------------------------------------------------------------------------------------------------|
| **rest.relativeURI**         | Yes      | Relative URI to fetch the resource in context of baseURI                                                                                |
| **rest.method**              | Yes      | Method for the operation to be performed (GET, POST)                                                                                    |
| **rest.headers**             | .        | Header values to be set for the request                                                                                                 |
| **rest.query**               | .        | .                                                                                                                                       |
| **rest.resourcePath**        | No       | This is the json path of resource location in the response body                                                                         |
| **rest.postBody**            | Yes      | Body content to be created/updated when the method is POST                                                                              |
| **rest.pagingMethod**        | No       | Determine the paging method for the exported data based on the resource. Ex: nextpageurl, pageargument, relativeuri, linkheader         |
| **rest.nextPagePath**        | No       | Path of the next page based on the pagingMathod for respective resource                                                                 |
| **rest.pageArgument**        | No       | Name of the pageArgument for the next page                                                                                              |
| **rest.nextPageRelativeURI** | No       | This can have placeholders that are populated from the last record of the previous page                                                 |
| **rest.maxPagePath**         | No       | Some APIs provide the count of the maximum page for responses that are paged. This is the JOSN path of the field containing said value. |
| **rest.maxCountPath**        | No       | This is the same as maxPagePath but is the JSON path to the field holding the total record count.                                       |
| **hooks._preSavePageId**     | No       | Hook id can be defined that gets executed before a page is saved                                                                        |

### File Adaptor Exports

The Integrator also supports the ability to define exports for CSV or JSON based file systems from different file storage servers. As of now integrator supports two different file servers. They are: FTP and Amazon S3.

#### Relevant Schema Info

##### Export Resource Fields (/exports)

| Field                        | Required | Description                                                                                                                  |
|:-----------------------------|:---------|:-----------------------------------------------------------------------------------------------------------------------------|
| **file.encoding**            | No       | Method for the operation to be performed (GET, POST)                                                                         |
| **file.type**                | No       | Type of the file to be exported Ex: csv                                                                                      |
| **file.skipDelete**          | No       | Boolean value, when set file will not be deleted from the source                                                             |
| **file.csv.columnDelimiter** | No       | Column delimiter in the file. Ex: ',', '                                                                                     |
| **file.csv.rowDelimiter**    | No       | Row delimiter in the file. Ex: '\n', '\r\n', '\r'                                                                            |
| **file.csv.keyColumns**      | No       | An array containing the column names to be considered to merge together the records                                          |
| **file.csv.hasHeaderRow**    | No       | Boolean value, when set it means the file has the header row and it is considered for the properties of the records exported |

Following are the two different file adaptors supported.

#### FTP Adaptor Exports

FTP Adaptor Export helps in exporting CSV or JSON data from files residing on FTP server. Following is the example for creating the export.

##### POST /exports

```javascript
{
  "name": "My File Adaptor",
  "_connectionId": "5587092ef78228000000000a",
  "asynchronous": true,
  "pageSize": "100",
  "ftp": {
    "directoryPath": "testDir/download"
  }
  "file": {
    "encoding": "utf8",
    "type": "csv",
    "skipDelete": true,
    "csv": {
      "coulumnDelimiter": ",",
      "rowDelimiter": "\n",
      "keyColumns": []
      "hasHeaderRow": true
    }
  }
}
```

You should receive a response that includes the following fields.

##### Sample Export Response

```javascript
{
  "_id": "507f1f77bcf86cd799439011",
  "apiIdentifier": "e53ec313",
}
```

#### Relevant Schema Info

##### Export Resource Fields (/exports)

| Field                      | Required | Description                                                                                                                            |
|:---------------------------|:---------|:---------------------------------------------------------------------------------------------------------------------------------------|
| **ftp.directoryPath**      | Yes      | ftp directory path where the file is located                                                                                           |
| **ftp.fileNameStartsWith** | No       | If the file name on the ftp is known, starting string for the file name can be provided, that helps in filtering out the file from FTP |
| **ftp.fileNameEndsWith**   | No       | Same as that of fileNameStartsWith but string will be matched to the ending of the file name                                           |

#### S3 Adaptor Exports

S3 Adaptor Export helps in exporting CSV or JSON data from files residing on Amazon S3 server. Following is the example for creating the export.

##### POST /exports

```javascript
{
  "name": "My File Adaptor",
  "_connectionId": "5587092ef78228000000000a",
  "asynchronous": true,
  "pageSize": "100",
  "s3": {
    "region": "us-east-1",
    "bucket": "integrator-bucket"
  }
  "file": {
    "encoding": "utf8",
    "type": "csv",
    "skipDelete": true,
    "csv": {
      "coulumnDelimiter": ",",
      "rowDelimiter": "\n",
      "keyColumns": []
      "hasHeaderRow": true
    }
  }
}
```

You should receive a response that includes the following fields.

##### Sample Export Response

```javascript
{
  "_id": "507f1f77bcf86cd799439011",
  "apiIdentifier": "e53ec313",
}
```

#### Relevant Schema Info

##### Export Resource Fields (/exports)

| Field                     | Required | Description                                                                                                                               |
|:--------------------------|:---------|:------------------------------------------------------------------------------------------------------------------------------------------|
| **s3.region**             | No       | Name of the nearest amazon s3 region to the location from where the request is being made. If not set, by default 'us-east-1' is selected |
| **s3.fileNameStartsWith** | No       | If the file name on the S3 is known, starting string for the file name can be provided, that helps in filtering out the file from S3      |
| **s3.fileNameEndsWith**   | No       | Same as that of fileNameStartsWith but string will be matched to the ending of the file name                                              |

### Webhook Adaptor Exports

The Integrator also supports the ability to integrate with any JSON based webhook. An export record is used to register a listener that can then be posted to. Following is a sample Webhook based export.

##### POST /exports

```javascript
{
  "name": "My Webhook",
  "type": "webhook",
  "webhook": {
    "verify": "hmac",
    "algorithm": "sha256",
    "encoding": "hex",
    "key": "********",
    "header": "X-Apple-Banana"
  }
}
```

You should receive a response that includes the following fields.

##### Sample Export Response

```javascript
{
  "_id": "507f1f77bcf86cd799439012",
  "apiIdentifier": "e53ec314"
}
```

#### Relevant Schema Info

##### Export Resource Fields (/exports)

| Field                 | Required | Description                                                                                                |
|:----------------------|:---------|:-----------------------------------------------------------------------------------------------------------|
| **webhook.provider**  | Yes      | Determines the name of the service on which the webhook has been used (Ex: github, slack, travis etc)      |
| **webhook.verify**    | Yes      | Method type to verify the authenticity of the webbook (Ex: token hmac)                                     |
| **webhook.token**     | Yes      | Token used to authenticate the webhook with the provider                                                   |
| **webhook.path**      | No       | Incase of token based authentication path needs to be set, it is the json path where the token is present. |
| **webhook.algorithm** | No       | Algorithm to be used for security and data integrity purpose. (Ex: sha1, sha256)                           |
| **webhook.encoding**  | No       | Encoding type to be used for the data (Ex: hex, base64)                                                    |
| **webhook.key**       | Yes      | For some services authentication happens using the key that we set in this field                           |
| **webhook.header**    | .        | .                                                                                                          |
