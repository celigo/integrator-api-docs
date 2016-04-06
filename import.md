Import
======

##### What is an Import?

Imports are used to insert data into an application. Like [Exports](#Export), Imports can run standalone, or in the context of a [Flow](#Flow).

Standalone Imports are useful for integrations where user actions (like clicking a button) need to invoke an application's API in real-time and return the results immediately. Standalone imports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive. Celigo uses standalone Imports extensively in its spreadsheet and email sync products.

Imports running in the context of a Flow will also execute synchronously but will channel results back to the flow so that stats can be reported in a job record.

##### Import Related HTTP Endpoints

| Relative URI               | Method | Success Code | Description                                                                    |
|:---------------------------|:-------|:------------:|:-------------------------------------------------------------------------------|
| /imports                   | GET    |     200      | List all imports.                                                              |
| /imports                   | POST   |     201      | Create new import.                                                             |
| /imports/{_id}             | PUT    |     200      | Update existing import.                                                        |
| /imports/{_id}             | GET    |     200      | Retrieve existing import.                                                      |
| /imports/{_id}             | DELETE |     204      | Delete existing import.                                                        |
| /imports/{_id}/distributed | PUT    |     200      | Create or update a distributed component that is linked to an existing import. |
| /imports/{_id}/distributed | GET    |     200      | Retrieve a distributed component that is linked to an existing import.         |
| /{apiIdentifier}           | POST   |     200      | Invoke an import on the req body (should be JSON array).                       |

Below are the common properties in all imports:

| Field              | Required | Description                                                                                                                                |
|:-------------------|:---------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| **_id**            | Yes      | System generated unique identifier for this connection.                                                                                    |
| **name**           | No       | Give your import an intuitive name to stay organized.                                                                                      |
| **_connectionId**  | Yes      | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being imported. |
| **lastModified**   | No       | Read only field tracking last modified date/time.                                                                                          |
| **apiIdentifier**  | No       | .                                                                                                                                          |
| **_integrationId** | Yes      | If this connection is part of an integration, this value will hold the id of that integration.                                             |
| **_connectorId**   | Yes      | If this connection belongs to a connector, this value will be hold the id of that connector.                                               |
| **sampleData**     | No       | Used in UI, which helps in populating data for mapping                                                                                     |
| **distributed**    | No       | Boolean value, if set the resulting import would be NS Distributed Import and dependendant fields to be set accordingly                    |
| **maxAttempts**    | No       | Maximum number of retries in the event of request failure                                                                                  |
| **ignoreExisting** | No       | Boolean value, if set the creation of already existing resource will be avoided                                                            |
| **ignoreMissing**  | No       | Boolean value, if set the updation is skipped incase of the record is missing                                                              |
| **idLockTemplate** | No       | .                                                                                                                                          |

The following sections are organized by application and adaptor, and ordered by popularity.

### NetSuite Distributed Adaptor Imports

If you are using the Integrator to build NetSuite based integrations, we highly recommend you install our Distributed Adaptor (DA for short). It's a SuiteApp that gets installed as a Bundle directly in your NetSuite account. Once installed, it enables a super rich import engine that can be used to map and load NetSuite data in very creative (and efficient) ways. Here are the steps required to create an import using the API. Please note that the samples below were generated to illustrate capabilities, not to provide a fully functional NetSuite import.

##### First, create your import resource.

POST /imports

```javascript
{
  "name": "NetSuite Order Import",
  "_connectionId": "5587092ed7822800e040000a",
  "_integrationId": "5560092ed7922800000f000e",
  "distributed": true
}
```

You should receive a response that includes the following fields. Note that we will use the \_id in the next step.

```javascript
{
  "_id": "507f1f77bcf86cd799439032",
  "apiIdentifier": "i66ec314"
}  
```

##### Second, create your distributed import resource.

PUT /imports/507f1f77bcf86cd799439032/distributed

```javascript
{
  "recordType": "salesorder",
  "operation": "addupdate",
  "internalIdLookup" : {
    "extract": "web_order_num",
    "searchField": "tranid"
  },
  "lookups": [
    {
      "name": "customerLookup",
      "recordType": "customer",
      "searchField": "email",
      "resultField": "internalid",
      "allowFailures": false
    },
    {
      "name": "currencyMap",
      "map": {
        "USA": "USD",
        "JAPAN": "JPY",
        "UK": "GDP"
      },
      "allowFailures": false
    },
    {
      "name": "itemLookup",
      "recordType": "item",
      "searchField": "itemid",
      "resultField": "internalid",
      "allowFailures": true,
      "default": "1234"
    },
    {
      "name": "orderLookup",
      "recordType": "salesorder",
      "expression": [['tranid','is','{{last_order}}'],'OR',['custbody_web_id','is','{{last_order}}']],
      "allowFailures": true
    }
  ],
  "mapping": {
    "fields": [
      {"extract": "city", "generate": "billcity"},
      {"extract": "zip", "generate": "billzip"},
      {"extract": "{first_name} {last_name}", "generate": "billaddressee"},
      {"generate": "ismultishipto", "hardCodedValue": false},
      {"generate": "custbody_source", "hardCodedValue": "webstore"},
      {"extract": "email", "generate": "entity", "lookupName": "customerLookup"},
      {"extract": "country", "generate": "currency", "lookupName": "currencyMap"},
      {"generate": "custbody_previous", "lookupName": "orderLookup"}
    ],
    "lists": [
      {
        "generate": "item",
        "fields": [
          {"extract": "order_lines[*].sku", "generate": "item", "lookupName": "itemLookup"},
          {"extract": "order_lines[*].price", "generate": "rate"},
          {"extract": "order_lines[*].quantity", "generate": "quantity"},
          {"extract": "shipments[*].ship_via", "generate": "shipmethod"},
          {"extract": "shipments[*].weight", "generate": "custcolumn_weight"},
          {"extract": "tax_percent", "generate": "taxrate1"},
          {"extract": "is_taxable", "generate": "istaxable"}
        ]
      }
    ]
  },
  "hooks": {
    "preMap": {
      "fileInternalId": "1234",
      "function": "mypreMapLogic"
    },
    "postMap": {
      "fileInternalId": "1234",
      "function": "mypostMapLogic"
    },
    "postSubmit": {
      "fileInternalId": "1234",
      "function": "myPostSubmitLogic"
    }
  }
}
```

#### Relevant Schema Info

##### Distributed Import Resource Fields (/imports/{_id}/distributed)

| Field                                  | Required | Description |
|:---------------------------------------|:---------|:------------|
| **recordType**                         | .        |             |
| **operation**                          | .        |             |
| **internalIdLookup.extract**           | .        |             |
| **internalIdLookup.searchField**       | .        |             |
| **internalIdLookup.expression**        | .        |             |
| **maxAttempts**                        | .        |             |
| **ignoreExisting**                     | .        |             |
| **ignoreMissing**                      | .        |             |
| **lookups.name**                       | .        |             |
| **lookups.map**                        | .        |             |
| **lookups.recordType**                 | .        |             |
| **lookups.searchField**                | .        |             |
| **lookups.resultField**                | .        |             |
| **lookups.operator**                   | .        |             |
| **lookups.expression**                 | .        |             |
| **lookups.allowFailures**              | .        |             |
| **lookups.default**                    | .        |             |
| **mapping.fields.extract**             | .        |             |
| **mapping.fields.generate**            | .        |             |
| **mapping.fields.hardCodedValue**      | .        |             |
| **mapping.fields.lookupName**          | .        |             |
| **mapping.fields.extractDateFormat**   | .        |             |
| **mapping.fields.extractDateTimezone** | .        |             |
| **mapping.fields.generateDateFormat**  | .        |             |
| **mapping.fields.internalId**          | .        |             |
| **mapping.lists.generate**             | .        |             |
| **mapping.lists.fields**               | .        |             |
| **hooks.preMap.fileInternalId**        | .        |             |
| **hooks.preMap.function**              | .        |             |
| **hooks.postMap.fileInternalId**       | .        |             |
| **hooks.postMap.function**             | .        |             |
| **hooks.postSubmit.fileInternalId**    | .        |             |
| **hooks.postSubmit.function**          | .        |             |

### NetSuite (Non-Distributed) Adaptor Imports

If installing the Distributed Adaptor in your NetSuite account is not an option, you can still create NetSuite imports that use NetSuite's web services API. The capabilities are slightly more limited and the performance can also be slower depending on the complexity of your imports, especially related to the number of dynamic internal id lookups each record in NetSuite will require before import. But, the really nice thing about using the web services API is that you can get started right away, and you do not need to involve a NetSuite admin to install anything first. With that, here is a sample NetSuite web services based import. Please note that this sample was generated to illustrate capabilities, not to provide a fully functional NetSuite import.

##### POST /imports

```javascript
{
  "name": "NetSuite Order Import",
  "_connectionId": "5587092ed78228009a00000a",
  "netsuite": {
    "recordType": "SalesOrder",
    "operation": "addupdate",
    "internalIdLookup" : {
      "extract": "web_order_num",
      "searchField": "tranId"
    },
    "lookups": [
      {
        "name": "customerLookup",
        "recordType": "Customer",
        "searchField": "email",
        "resultField": "internalId",
        "allowFailures": false,
      },
      {
        "name": "currencyMap",
        "map": {
          "USA": "USD",
          "JAPAN": "JPY",
          "UK": "GDP"
        },
        "allowFailures": false
      },
      {
        "name": "itemLookup",
        "recordType": "Item",
        "searchField": "itemId",
        "resultField": "internalId",
        "allowFailures": false,
      }
    ],
    "customFieldMetadata": {
      "custbody_source": {
        "type": "_freeFormText"
      },
      "custcolumn_weight": {
        "type": "_decimalNumber"
      }
    }
  },
  "mapping": {
    "fields": [
      {"extract": "city", "generate": "billingAddress.city"},
      {"extract": "zip", "generate": "billingAddress.zip"},
      {"extract": "{first_name} {last_name}", "generate": "billingAddress.addressee"},
      {"generate": "isMultiShipTo", "hardCodedValue": false},
      {"generate": "custbody_source", "hardCodedValue": "webstore"},
      {"extract": "email", "generate": "entity", "lookupName": "customerLookup"},
      {"extract": "country", "generate": "currency", "lookupName": "currencyMap"}
    ],
    "lists": [
      {
        "generate": "SalesOrderItem",
        "fields": [
          {"extract": "order_lines[*].sku", "generate": "item", "lookupName": "itemLookup"},
          {"extract": "order_lines[*].price", "generate": "rate"},
          {"extract": "order_lines[*].quantity", "generate": "quantity"},
          {"extract": "shipments[*].ship_via", "generate": "shipMethod"},
          {"extract": "shipments[*].weight", "generate": "custcolumn_weight"},
          {"extract": "tax_percent", "generate": "taxRate1"},
          {"extract": "is_taxable", "generate": "isTaxable"}
        ]
      }
    ]
  },
  "hooks": {
    "_preMapId": "5587092fd78228000000000a",
    "_postMapId": "5561092fd78228000000010b",
    "_postSubmitId": "5521092fd78228000100011c"
  }
}
```

You should receive a response that includes the following fields.

```javascript
{
  "_id": "507f1f77bcf86cd799439030",
  "apiIdentifier": "i66ec312"
}
```

##### Important differences between NetSuite Distributed and Non-Distributed Imports

1.	field ids are all lowercase in distributed adaptor vs camel case in non-distributed adaptor.
2.	expressions are not yet supported in non-distributed adaptor.
3.	customFieldMetadata is required in non-distributed adaptor.
4.	hooks in distributed adaptor are implemented in SuiteScript vs node.js in non-distributed adaptor.

#### Relevant Schema Info

##### Import Resource Fields (/imports)

| Field                                     | Required | Description                                                                                        |
|:------------------------------------------|:---------|:---------------------------------------------------------------------------------------------------|
| **netsuite.recordType**                   | Yes      | Type of the record on NS (Contact, Employee, SaleOrder etc..)                                      |
| **netsuite.recordTypeId**                 | .        | Unique id associated with the recordType selected                                                  |
| **netsuite.operation**                    | Yes      | Operation to be perfomred on the Netsuite (add, update, addupdate, attach, detach, delete)         |
| **netsuite.retryUpdateAsAdd**             | No       | Boolean value if set, on failure of any record update on NS, it will be retried as a add operation |
| **netsuite.internalIdLookup.extract**     | Yes      | Field to be matched to update the respective record                                                |
| **netsuite.internalIdLookup.searchField** | .        | .                                                                                                  |
| **netsuite.lookups.name**                 | .        | Give the lookups an appropriate name which is easier to refer it in the code                       |
| **netsuite.lookups.map**                  | .        | .                                                                                                  |
| **netsuite.lookups.recordType**           | .        | .                                                                                                  |
| **netsuite.lookups.searchField**          | .        | .                                                                                                  |
| **netsuite.lookups.resultField**          | .        | .                                                                                                  |
| **netsuite.lookups.includeInactive**      | .        | .                                                                                                  |
| **netsuite.lookups.allowFailures**        | .        | .                                                                                                  |
| **netsuite.customFieldMetadata**          | .        | .                                                                                                  |
| **mapping.fields.extract**                | .        | .                                                                                                  |
| **mapping.fields.generate**               | .        | .                                                                                                  |
| **mapping.fields.hardCodedValue**         | .        | .                                                                                                  |
| **mapping.fields.lookupName**             | .        | .                                                                                                  |
| **mapping.fields.extractDateFormat**      | .        | .                                                                                                  |
| **mapping.fields.extractDateTimezone**    | .        | .                                                                                                  |
| **mapping.fields.generateDateFormat**     | .        | .                                                                                                  |
| **mapping.lists.generate**                | .        | .                                                                                                  |
| **mapping.lists.fields**                  | .        | .                                                                                                  |

### REST API Adaptor Imports

The Integrator supports the ability to define imports for any RESTful JSON API. Following are two examples, one simple, the other more complex.

##### POST /imports

The following simple import will send a POST request to the /customers relative URI to create new customers.

```javascript
{
  "name": "REST API Sample Post",
  "_connectionId": "5587092ed7822800000d0c0a",
  "rest": {
    "relativeURI": "/customers",
    "method": "POST",
  },
  "mapping": {
    "fields": [
      {"extract": "street1", "generate": "billingAddress.addr1"},
      {"extract": "street2", "generate": "billingAddress.addr2"},
      {"extract": "city", "generate": "billingAddress.city"},
      {"extract": "state", "generate": "billingAddress.state"},
      {"extract": "zip", "generate": "billingAddress.zip"},
      {"extract": "{first_name} {last_name}", "generate": "billingAddress.addressee"},
    ]
  }
}
```

You should receive a response that includes the following fields.

```javascript
{
  "_id": "507f1f77bcf86cd799439030",
  "apiIdentifier": "i66ec312"
}
```

Here is a more complex import. This import will check to see if a customer resource already exists (by performing the lookup defined by 'idLookup'). If the customer does exists the import will submit a PUT request to update the existing customer. If the customer does not exist the import will submit a POST request to a different URI to create a new customer.

```javascript
{
  "name": "Composite Add or Update Import",
  "_connectionId": "5587092ed78228000000000a",
  "rest" : {
    "relativeURI": [
      "/customers/{idLookup}",
      "/customers"
    ],
    "method": [
      "PUT",
      "POST"
    ],
    "lookups" : [
      {
        "name": "idLookup",
        "relativeURI": "/customers?ns_id={internalId}",
        "method": "GET",
        "extract": "0.id",
      },
      {
        "name": "currencyMap",
        "map": {
          "USA": "USD",
          "JAPAN": "JPY",
          "UK": "GDP"
        },
        "allowFailures": false
      }
    ]
  },
  "mapping": {
    "fields": [
      {"extract": "billingAddress.city", "generate": "city"},
      {"extract": "billingAddress.zip", "generate": "zip"},
      {"extract": "{firstName} {lastName}", "generate": "addressee"},
      {"generate": "opt_out", "hardCodedValue": false},
      {"generate": "source", "hardCodedValue": "phone"},
      {"extract": "billingAddress.country", "generate": "currency", "lookupName": "currencyMap"}
    ]
  },
  "hooks": {
    "_preMapId": "5587092fd78228000000000a",
    "_postMapId": "5561092fd78228000000010b",
    "_postSubmitId": "5521092fd78228000100011c"
  }
}
```

#### Relevant Schema Info

##### Import Resource Fields (/imports)

| Field                                  | Required | Description                                                  |
|:---------------------------------------|:---------|:-------------------------------------------------------------|
| **rest.relativeURI**                   | Yes      | Relative URI to fetch the resource in context of baseURI     |
| **rest.method**                        | Yes      | Method for the operation to be performed (PUT, POST, DELETE) |
| **rest.headers**                       | .        | Header values to be set for the request                      |
| **rest.responseIdPath**                | .        | .                                                            |
| **rest.successPath**                   | .        | .                                                            |
| **rest.resourceId.lookupName**         | .        | .                                                            |
| **rest.resourceId.extract**            | .        | .                                                            |
| **rest.lookups.name**                  | .        | .                                                            |
| **rest.lookups.relativeURI**           | .        | .                                                            |
| **rest.lookups.method**                | .        | .                                                            |
| **rest.lookups.postData**              | .        | .                                                            |
| **rest.lookups.extract**               | .        | .                                                            |
| **rest.lookups.map**                   | .        | .                                                            |
| **rest.lookups.default**               | .        | .                                                            |
| **rest.lookups.allowFailures**         | .        | .                                                            |
| **mapping.fields.extract**             | .        | .                                                            |
| **mapping.fields.generate**            | .        | .                                                            |
| **mapping.fields.dataType**            | .        | .                                                            |
| **mapping.fields.hardCodedValue**      | .        | .                                                            |
| **mapping.fields.lookupName**          | .        | .                                                            |
| **mapping.fields.extractDateFormat**   | .        | .                                                            |
| **mapping.fields.extractDateTimezone** | .        | .                                                            |
| **mapping.fields.generateDateFormat**  | .        | .                                                            |
| **mapping.lists.generate**             | .        | .                                                            |
| **mapping.lists.fields**               | .        | .                                                            |

### FTP Adaptor Imports

The Integrator supports the ability to define imports for any CSV or JSON based file systems on FTP. Following is the example.

##### POST /imports

The following simple import will send a POST request with the postData to create a file on FTP and import the csv data in it.

```javascript
{
  "name": "FTP import example",
  "_connectionId": "5587092ed7822800000d0c0a",
  "file": {
    "skipAggregation": false,
    "type": "csv",
    "hasHeaderRow": true
  },
  "ftp": {
    "directoryPath": "/testUploadDir/upload",
    "fileExtension": "csv"
  },
  "mapping": {
    "fields": [
      {"extract": "docId", "generate": "document id"},
      {"extract": "name", "generate": "document name"},
      {"extract": "content", "generate": "document content"}
    ]
  }
}
```

You should receive a response that includes the following fields.

```javascript
{
  "_id": "507f1f77bcf86cd799439030",
  "apiIdentifier": "i66ec312"
}
```

#### Relevant Schema Info

##### Import Resource Fields (/imports)

| Field                                | Required | Description                                                                                                                      |
|:-------------------------------------|:---------|:---------------------------------------------------------------------------------------------------------------------------------|
| **file.skipAggregation**             | No       | Boolean value, when set to true records are aggregated at worker end. (Default is false)                                         |
| **file.type**                        | No       | Type of the file to be imported Ex: csv                                                                                          |
| **file.compressionFormat**           | No       | Compression format to be used to compress the data imported. (Default no compression)                                            |
| **file.csv.columnDelimiter**         | No       | Column delimiter to be used in the file. Ex: ',', '                                                                              |
| **file.csv.rowDelimiter**            | No       | Row delimiter to be used in the file. Ex: '\n', '\r\n', '\r' (Default is crlf)                                                   |
| **file.csv.includeHeader**           | No       | Boolean value, when set property names are put in the first row of the file (Default is true)                                    |
| **file.csv.wrapWithQuotes**          | No       | Boolean value, when set all the values in the file are wrapped with quotes (Default is false)                                    |
| **file.csv.replaceTabWithSpace**     | No       | Boolean value, when set tabs in the content of the data (except columnDelimiters) are replaced with a space (Default is false)   |
| **file.csv.replaceNewlineWithSpace** | No       | Boolean value, when set new lines in the content of the data (except rowDelimiters) are replaced with a space (Default is false) |
| **ftp.directoryPath**                | Yes      | ftp directory path where the file to be saved                                                                                    |
| **ftp.fileExtension**                | No       | Extension of the file to be stored on the FTP                                                                                    |
| **ftp.fileName**                     | No       | Name of the file                                                                                                                 |

### S3 Adaptor Imports

The Integrator supports the ability to define imports for any CSV or JSON based file systems on S3. Following is the example.

##### POST /imports

The following simple import will send a POST request with the postData to create a file on S3 and import the csv data in it.

```javascript
{
  "name": "S3 import example",
  "_connectionId": "5587092ed7822800000d0c0a",
  "file": {
    "skipAggregation": false,
    "type": "csv",
    "hasHeaderRow": true
  },
  "s3": {
    "region": "us-east-1",
    "bucket": "destinationBucket"
    "fileKey": "testImport{{timestamp}}"
  },
  "mapping": {
    "fields": [
      {"extract": "docId", "generate": "document id"},
      {"extract": "name", "generate": "document name"},
      {"extract": "content", "generate": "document content"}
    ]
  }
}
```

You should receive a response that includes the following fields.

```javascript
{
  "_id": "507f1f77bcf86cd799439030",
  "apiIdentifier": "i66ec312"
}
```

#### Relevant Schema Info

##### Import Resource Fields (/imports)

| Field                                | Required | Description                                                                                                                               |
|:-------------------------------------|:---------|:------------------------------------------------------------------------------------------------------------------------------------------|
| **file.skipAggregation**             | No       | Boolean value, when set to true records are aggregated at worker end. (Default is false)                                                  |
| **file.type**                        | No       | Type of the file to be imported Ex: csv                                                                                                   |
| **file.compressionFormat**           | No       | Compression format to be used to compress the data imported. (Default no compression)                                                     |
| **file.csv.columnDelimiter**         | No       | Column delimiter to be used in the file. Ex: ',', '                                                                                       |
| **file.csv.rowDelimiter**            | No       | Row delimiter to be used in the file. Ex: '\n', '\r\n', '\r' (Default is crlf)                                                            |
| **file.csv.includeHeader**           | No       | Boolean value, when set property names are put in the first row of the file (Default is true)                                             |
| **file.csv.wrapWithQuotes**          | No       | Boolean value, when set all the values in the file are wrapped with quotes (Default is false)                                             |
| **file.csv.replaceTabWithSpace**     | No       | Boolean value, when set tabs in the content of the data (except columnDelimiters) are replaced with a space (Default is false)            |
| **file.csv.replaceNewlineWithSpace** | No       | Boolean value, when set new lines in the content of the data (except rowDelimiters) are replaced with a space (Default is false)          |
| **s3.region**                        | Yes      | Name of the nearest amazon s3 region to the location from where the request is being made. If not set, by default 'us-east-1' is selected |
| **s3.bucket**                        | Yes      | Name of the bucket in S3, where you want file to be saved                                                                                 |
| **s3.fileKey**                       | No       | Name of the file                                                                                                                          |
