# Introduction
The Integrator is an API first platform.  Every new feature gets released here first, and then shortly after in the UI.  The Integrator API is RESTful, uses JSON, and is secured by Bearer Tokens.  The target audience for the API is developers who wish to build integration based apps.  Complementing the API, the Integrator supports a rich customization framework.  The integrations that you build can include an installer, settings pages, along with any number of hooks (hooks give you the ability to write your own custom code, and are useful for requirements that cannot be implemented via configuration alone).  Any integrations that you build can be listed in the Integrator Marketplace for easy install by any other Integrator user.  Enjoy!

## Quick Start
## Authentication
### Bearer Tokens
Every Integrator account is
### One Time Tokens
## Rate Limiting
## Headers


# Resources
## Connection
## Export
##### What is an Export?
Exports are used to extract data from external applications in a standardized, canonical way.  Exports can run standalone, or in the context of a [Flow](#Flow).  

Standalone Exports are useful for applications where user actions (like clicking a button) need to invoke an external application's API in real-time and return the results immediately.  Standalone exports are especially useful for applications that need to integrate with more than one external applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive.  Celigo uses standalone Exports extensively in its spreadsheet and email sync product lines.  

Exports running in the context of a Flow will execute asynchronously and automatically break exported data down into one or more pages and then stream those pages to one or more [Imports](#Import).  

##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | :----: | :---- |
| /exports | POST | 201 | Create new export. |
| /exports/{_id} | PUT | 200 | Update existing export. |
| /exports/{_id} | GET | 200 | Retrieve existing export.  |
| /exports/{_id} | DELETE | 204 | Delete existing export. |

##### Sample Export Request
```javascript
{
  "name": "My Export",
  "_connectionId": "5587092ed78228000000000a",
  "rest": {
    "relativeURI": "/customers",
    "method": "GET"
  },
  "pageSize": 20,
  "hooks": {
    "_preSavePageId": "5587092fd78228000000000b"
  }
}
```
##### Sample Export Response
```javascript
{
  "_id": "507f1f77bcf86cd799439011",
  "apiIdentifier": "e53ec313",
  "name": "My Export",
  "_connectionId": "5587092ed78228000000000a",
  "rest": {
    "relativeURI": "/customers",
    "method": "GET"
  },
  "pageSize": 20,
  "hooks": {
    "_preSavePageId": "5587092fd78228000000000b"
  }
}
```
##### Fields
| Field | Description |
| :---- | :---- |
| **name** | Give your export an intuitive name to stay organized. |
| **_connectionId** | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **lastModified** | Read only field tracking last modified date/time. |
| **asynchronous** | . |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |
| **type** | . |
| **pageSize** | . |
| **sampleData** | . |
| **test.limit** | . |
| **delta.dateField** | . |
| **delta.dateFormat** | . |
| **once.booleanField** | . |
| **valueDelta.exportedField** | . |
| **valueDelta.pendingField** | . |
| **webhook.verify** | . |
| **webhook.token** | . |
| **webhook.path** | . |
| **webhook.algorithm** | . |
| **webhook.encoding** | . |
| **webhook.key** | . |
| **webhook.header** | . |
| **distributed.bearerToken** | . |
| **hooks._preSavePageId** | . |
| **rest.relativeURI** | . |
| **rest.method** | . |
| **rest.headers** | . |
| **rest.query** | . |
| **rest.resourcePath** | . |
| **rest.postBody** | . |
| **rest.pagingMethod** | . |
| **rest.nextPagePath** | . |
| **rest.pageArgument** | . |
| **ftp.directoryPath** | . |
| **netsuite.type** | . |
| **netsuite.searches** | . |
| **netsuite.metadata** | . |
| **netsuite.selectoption** | . |
| **netsuite.customFieldMetadata** | . |
| **netsuite.sortedByInternalId** | . |
| **netsuite.groupByInternalId** | . |


### Distributed Export
##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | :----: | :---- |
| /exports/{_id}/distributed | PUT | 200 | Create or update a distributed component that is linked to an existing export. |
| /exports/{_id}/distributed | GET | 200 | Retrieve a distributed component that is linked to an existing export. |

#### NetSuite Realtime Export
##### Sample Export JSON
```javascript
{
  "name": "Realtime Item Export",
  "_connectionId": "5587092ed78128000000000a",
  "type": "distributed",
  "distributed": {
    "bearerToken": "********",
  }
}
```
##### Sample Distributed Request
```javascript
{
  "type": "realtime",
  "recordType": "salesorder",
  "executionContext": ["userinterface", "webservices", "webstore"],
  "qualifier": "['total', '>=', '1000']",
  "hooks": {
    "preSend": {
      "fileInternalId": "1234",
      "functionName": "myPreSendLogic"
    }
  }
}
```
##### Sample Distributed Response
```javascript
```
##### Fields
| Field | Description |
| :---- | :---- |
| **type** | . |
| **recordType** | . |
| **executionContext** | Read only field tracking last modified date/time. |
| **qualifier** | . |
| **hooks.preSend.fileInternalId** | . |
| **hooks.preSend.functionName** | . |

#### NetSuite Batch Export
##### Sample Export JSON
```javascript
{
  "name": "Delta Customer Export",
  "_connectionId": "5587092ed78128000000000a",
  "type": "delta",
  "delta": {
    "dateField": "lastmodifieddate"
  },
  "netsuite": {
    "type": "restlet"
  },
  "pageSize": 50
}
```
##### Sample Distributed Request
```javascript
{
  "type": "batch",
  "recordType": "customer",
  "searchId": "69422",
  "hooks": {
    "preSend": {
      "fileInternalId": "1234",
      "functionName": "myPreSendLogic"
    }
  }
}
```
##### Fields
| Field | Description |
| :---- | :---- |
| **type** | . |
| **recordType** | . |
| **searchId** | . |
| **hooks.preSend.fileInternalId** | . |
| **hooks.preSend.functionName** | . |

##### Related HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | :----: | :---- |
| /{apiIdentifier} | POST | 200 | Invoke an export. |
| /exports/preview | POST | 200 | Invoke an export in test mode before saving it to preview the results.  |


## Import
##### Import Related HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | :----: | :---- |
| /imports | POST | 201 | Create new import. |
| /imports/{_id} | PUT | 200 | Update existing import. |
| /imports/{_id} | GET | 200 | Retrieve existing import.  |
| /imports/{_id} | DELETE | 204 | Delete existing import. |
| /imports/{_id}/distributed | PUT | 200 | Create or update a distributed component that is linked to an existing import. |
| /imports/{_id}/distributed | GET | 200 | Retrieve a distributed component that is linked to an existing import. |
| /{apiIdentifier} | POST | 200 | Invoke an import on the req body (should be JSON array). |

The following sections are organized by application, and popularity.  

### NetSuite Distributed Adaptor Imports
If you are using the Integrator to build NetSuite based integrations, we highly recommend you install our Distributed Adaptor (DA for short).  It's a SuiteApp that gets installed as a Bundle directly in your NetSuite account.  Once installed, it enables a super rich import engine that can be used to map and load NetSuite data in very creative (and efficient) ways.  Once installed, here are the steps to create an import using the API.  Please note that the samples below were generated to illustrate capabilities, not to provide a fully functional NetSuite import.

##### First, create your import resource.  POST /imports
```javascript
{
  "name": "NetSuite Order Import",
  "_connectionId": "5587092ed78228000000000a",
  "_integrationId": "5560092ed7922800000f000e",
  "distributed": "true"
}
```
You should receive a response that includes the following fields.  Note that we will use the \_id in the next step.
```javascript
{
  "_id": "507f1f77bcf86cd799439032",
  "apiIdentifier": "i66ec314"
}  
```
##### Second, create your distributed import resource.  PUT /imports/507f1f77bcf86cd799439032/distributed
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
      "allowFailures": "false",
      "includeInactive": "false"
    },
    {
      "name": "currencyMap",
      "map": {
        "USA": "USD",
        "JAPAN": "JPY",
        "UK": "GDP"
      },
      "allowFailures": "false"
    },
    {
      "name": "itemLookup",
      "recordType": "item",
      "searchField": "itemid",
      "resultField": "internalid",
      "allowFailures": "false",
      "includeInactive": "false"
    },
    {
      "name": "orderLookup",
      "recordType": "salesorder",
      "expression": "[['tranid','is','{{last_order}}'],'OR',['custbody_web_id','is','{{last_order}}']]",
      "allowFailures": "true"
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
    "preMapping": {
      "fileInternalId": "1234",
      "functionName": "myPreMappingLogic"
    },
    "postMapping": {
      "fileInternalId": "1234",
      "functionName": "myPostMappingLogic"
    },
    "postSubmit": {
      "fileInternalId": "1234",
      "functionName": "myPostSubmitLogic"
    }
  }
}
```
#### Relevant Schema Info

##### Import Resource Fields (/imports)
| Field | Description |
| :---- | :---- |
| **name** | . |
| **_connectionId** | . |
| **lastModified** | . |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |  
| **distributed** | . |

##### Distributed Import Resource Fields (/imports/{_id}/distributed)
| Field | Description |
| :---- | :---- |
| **recordType** | . |
| **operation** | . |
| **internalIdLookup.extract** | . |
| **internalIdLookup.searchField** | . |
| **internalIdLookup.expression** | . |
| **maxAttempts** | . |
| **ignoreExisting** | . |
| **ignoreMissing** | . |
| **lookups.name** | . |
| **lookups.map** | . |
| **lookups.recordType** | . |
| **lookups.searchField** | . |
| **lookups.resultField** | . |
| **lookups.expression** | . |
| **lookups.includeInactive** | . |
| **lookups.allowFailures** | . |
| **mapping.fields.extract** | . |
| **mapping.fields.generate** | . |
| **mapping.fields.hardCodedValue** | . |
| **mapping.fields.lookup** | . |
| **mapping.lists.generate** | . |
| **mapping.lists.fields** | . |
| **hooks.preMapping.fileInternalId** | . |
| **hooks.preMapping.functionName** | . |
| **hooks.postMapping.fileInternalId** | . |
| **hooks.postMapping.functionName** | . |
| **hooks.postSubmit.fileInternalId** | . |
| **hooks.postSubmit.functionName** | . |


### NetSuite (Non-Distributed) Adaptor Imports
If installing the Distributed Adaptor in your NetSuite account is not an option, you can still create NetSuite imports that use NetSuite's web services API.  The capabilities are slightly more limited and the performance can also be slower depending on the complexity of your imports, especially related to the number of dynamic internal id lookups records in NetSuite will require before import.  But, the really nice thing about using the web services API is that you can get started right away, and you do not need to involve a NetSuite admin to install anything first.  With that, here is a sample NetSuite web services based import.  Please note that this sample was generated to illustrate capabilities, not to provide a fully functional NetSuite import.

##### POST /imports
```javascript
{
  "name": "NetSuite Order Import",
  "_connectionId": "5587092ed78228000000000a",
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
        "allowFailures": "false",
        "includeInactive": "false"
      },
      {
        "name": "currencyMap",
        "map": {
          "USA": "USD",
          "JAPAN": "JPY",
          "UK": "GDP"
        },
        "allowFailures": "false"
      },
      {
        "name": "itemLookup",
        "recordType": "Item",
        "searchField": "itemId",
        "resultField": "internalId",
        "allowFailures": "false",
        "includeInactive": "false"
      }
    ],
    "customFieldMetadata": [
      {
        "field": "custbody_source",
        "type": "_freeFormText"
      },
      {
        "field": "custcolumn_weight",
        "type": "_decimalNumber"
      }
    ]
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
    "_preMappingId": "5587092fd78228000000000a",
    "_postMappingId": "5561092fd78228000000010b",
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
1. field ids are all lowercase in distributed adaptor vs camel case in non-distributed adaptor.
2. expressions are not yet supported in non-distributed adaptor.
3. customFieldMetadata is required in non-distributed adaptor.
4. hooks in distributed adaptor are implemented in SuiteScript vs node.js in non-distributed adaptor.

#### Relevant Schema Info

##### Import Resource Fields (/imports)
| Field | Description |
| :---- | :---- |
| **name** | . |
| **_connectionId** | . |
| **lastModified** | . |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |
| **maxAttempts** | . |
| **ignoreExisting** | . |
| **ignoreMissing** | . |
| **netsuite.recordType** | . |
| **netsuite.operation** | . |
| **netsuite.internalIdLookup.extract** | . |
| **netsuite.internalIdLookup.searchField** | . |
| **netsuite.lookups.name** | . |
| **netsuite.lookups.map** | . |
| **netsuite.lookups.recordType** | . |
| **netsuite.lookups.searchField** | . |
| **netsuite.lookups.resultField** | . |
| **netsuite.lookups.includeInactive** | . |
| **netsuite.lookups.allowFailures** | . |
| **netsuite.customFieldMetadata.field** | . |
| **netsuite.customFieldMetadata.type** | . |
| **mapping.fields.extract** | . |
| **mapping.fields.generate** | . |
| **mapping.fields.hardCodedValue** | . |
| **mapping.fields.lookup** | . |
| **mapping.lists.generate** | . |
| **mapping.lists.fields** | . |

### REST API Adaptor Imports


##### POST /imports
```javascript
{
  "name": "REST API Sample Post",
  "_connectionId": "5587092ed78228000000000a",
  // todo, need rest section
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
    "_preMappingId": "5587092fd78228000000000a",
    "_postMappingId": "5561092fd78228000000010b",
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

#### Relevant Schema Info

##### Import Resource Fields (/imports)
| Field | Description |
| :---- | :---- |
| **name** | . |
| **_connectionId** | . |
| **lastModified** | . |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |
| **maxAttempts** | . |
| **ignoreExisting** | . |
| **ignoreMissing** | . |
| **rest.relativeURI** | . |
| **rest.method** | . |
| **rest.headers** | . |
| **rest.responseIdPath** | . |
| **rest.successPath** | . |
| **rest.resourceId.lookupName** | . |
| **rest.resourceId.extract** | . |
| **rest.lookups.name** | . |
| **rest.lookups.relativeURI** | . |
| **rest.lookups.method** | . |
| **rest.lookups.postData** | . |
| **rest.lookups.extract** | . |
| **rest.lookups.allowFailures** | . |
| **mapping.fields.extract** | . |
| **mapping.fields.generate** | . |
| **mapping.fields.hardCodedValue** | . |
| **mapping.fields.lookup** | . |
| **mapping.lists.generate** | . |
| **mapping.lists.fields** | . |


## Flow
## Integration
## Connector
## IClient
## Job
## Job Error
## Retry
