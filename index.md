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
    "_preSavePage": "5587092fd78228000000000b"
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
    "_preSavePage": "5587092fd78228000000000b"
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
| **hooks.preSavePage** | . |
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
    "preSendData": {
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
| **hooks.preSendData.fileInternalId** | . |
| **hooks.preSendData.functionName** | . |

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
    "preSendData": {
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
| **hooks.preSendData.fileInternalId** | . |
| **hooks.preSendData.functionName** | . |

##### Related HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | :----: | :---- |
| /{apiIdentifier} | POST | 200 | Invoke an export. |
| /exports/preview | POST | 200 | Invoke an export in test mode before saving it to preview the results.  |


## Import
##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | :----: | :---- |
| /imports | POST | 201 | Create new import. |
| /imports/{_id} | PUT | 200 | Update existing import. |
| /imports/{_id} | GET | 200 | Retrieve existing import.  |
| /imports/{_id} | DELETE | 204 | Delete existing import. |

##### Sample Import Request
```javascript
{
  "name": "NetSuite Order Import",
  "_connectionId": "5587092ed78228000000000a",
  "netsuite": {
    "recordType": "salesorder",
    "operation": "addupdate",
    // TODO: need to know how to configure lookup to determine add vs update
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
      }
    ]
  },
  "mapping": {
    "fields": [
      {"extract": "city", "generate": "billcity"},
      {"extract": "zip", "generate": "billzip"},
      {"extract": "{first_name} {last_name}", "generate": "billaddressee"},
      {"generate": "ismultishipto", "hardCodedValue": false},
      {"generate": "custbody_source", "hardCodedValue": "webstore"},
      {"extract": "email", "generate": "customer", "lookup": "customerLookup"},
      {"extract": "country", "generate": "currency", "lookup": "currencyMap"}
    ],
    "lists": [
      {
        "generate": "item",
        "fields": [
          {"extract": "order_lines[*].sku", "generate": "item", "lookup": "itemLookup"},
          {"extract": "order_lines[*].price", "generate": "rate"},
          {"extract": "order_lines[*].quantity", "generate": "qty"},
          {"extract": "shipments[*].ship_via", "generate": "shipmethod"},
          {"extract": "shipments[*].weight", "generate": "custcolumn_weight"},
          {"extract": "tax_percent", "generate": "taxrate1"},
          {"extract": "is_taxable", "generate": "istaxable"}
        ]
      }
    ]
  },
  "hooks": {
    "_preMapping": "5587092fd78228000000000a",
    "_postMapping": "5561092fd78228000000010b"
  }
}
```
##### Sample Import Response
```javascript
{
  "_id": "507f1f77bcf86cd799439032",
  "apiIdentifier": "i66ec314"
}
```
##### Fields
| Field | Description |
| :---- | :---- |
| **name** | . |
| **_connectionId** | . |
| **lastModified** | . |
| **asynchronous** | . |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |  
| **sampleData** | . |
| **distributed** | . |
| **maxAttempts** | . |
| **hooks._preMapping** | . |
| **hooks._postMapping** | . |
| **hooks._postSubmit** | . |
| **rest.relativeURI** | . |
| **rest.method** | . |
| **rest.headers** | . |
| **rest.responseIdPath** | . |
| **rest.successPath** | . |
| **rest.resourceId.lookupName** | . |
| **rest.resourceId.extract** | . |
| **rest.ignoreExisting** | . |
| **rest.ignoreMissing** | . |
| **rest.lookups.name** | . |
| **rest.lookups.relativeURI** | . |
| **rest.lookups.method** | . |
| **rest.lookups.postData** | . |
| **rest.lookups.extract** | . |
| **rest.lookups.allowFailures** | . |
| **ftp.directoryPath** | . |
| **ftp.fileType** | . |
| **netsuite.recordType** | . |
| **netsuite.operation** | . |
| **netsuite.lookups.name** | . |
| **netsuite.lookups.map** | . |
| **netsuite.lookups.recordType** | . |
| **netsuite.lookups.searchField** | . |
| **netsuite.lookups.resultField** | . |
| **netsuite.lookups.expression** | . |
| **netsuite.lookups.includeInactive** | . |
| **netsuite.lookups.allowFailures** | . |
| **mapping.fields.extract** | . |
| **mapping.fields.generate** | . |
| **mapping.fields.hardCodedValue** | . |
| **mapping.fields.lookup** | . |
| **mapping.fields.dataType** | . |
| **mapping.lists.generate** | . |
| **mapping.lists.fields** | . |

### Distributed Imports
##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | :----: | :---- |
| /imports/{_id}/distributed | PUT | 200 | Create or update a distributed component that is linked to an existing import. |
| /imports/{_id}/distributed | GET | 200 | Retrieve a distributed component that is linked to an existing import. |

#### NetSuite Distributed Imports

If you are using the Integrator to integrate NetSuite in any way, we highly recommend that you install our Distributed Adaptor (DA for short).  It's a SuiteApp that gets installed as a Bundle directly in your NetSuite account.  Once installed, it enables a super rich import engine that can be used to map and load NetSuite data in very creative (and efficient) ways.  Here is what a mapping for the NetSuite DA looks like.  Please keep in mind that this sample was created for documentation purposes only, to illustrate different mapping capabilities, not to represent a working order import into NetSuite.

##### Sample Import Request
```javascript
{
  "name": "NetSuite Order Import - Distributed Version",
  "_connectionId": "5587092ed78228000000000a",
  "distributed": "true"
}
```

##### Sample Distributed Request
```javascript
{
  "recordType": "salesorder",
  "operation": "addupdate",
  // TODO: need to know how to configure lookup to determine add vs update
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
      "recordType": "transaction",
      "expression": "[['tranid', 'is', '{last_order}']]",
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
      {"extract": "email", "generate": "customer", "lookup": "customerLookup"},
      {"extract": "country", "generate": "currency", "lookup": "currencyMap"},
      {"generate": "custbody_previous", "lookup": "orderLookup"}
    ],
    "lists": [
      {
        "generate": "item",
        "fields": [
          {"extract": "order_lines[*].sku", "generate": "item", "lookup": "itemLookup"},
          {"extract": "order_lines[*].price", "generate": "rate"},
          {"extract": "order_lines[*].quantity", "generate": "qty"},
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
    }
  }
}
```
#### Sample Response
```javascript
```
## Flow
## Integration
## Connector
## IClient
## Job
## Job Error
## Retry
