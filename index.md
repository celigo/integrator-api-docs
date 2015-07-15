# Introduction
The Integrator is an API first platform.  Features are released here first, and then shortly after in the UI.  The Integrator API is RESTful, uses JSON, and is secured by Bearer Tokens.  The target audience for the API is developers who wish to build integration based apps.  Complementing the API, the Integrator supports a rich customization framework.  The integrations that you build can include an installer, settings pages, along with any number of hooks (hooks give you the ability to write your own custom code, and are useful for requirements that cannot be implemented via configuration alone).  Any integrations that you build can be listed in the Integrator Marketplace for easy install by any other Integrator user.  Enjoy!

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
Exports are used to extract data from an application.  Exports can run standalone, or in the context of a [Flow](#Flow).  

Standalone Exports are useful for integrations where user actions (like clicking a button) need to invoke an external application's API in real-time and return the results immediately.  Standalone exports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive.  Celigo uses standalone Exports extensively in its spreadsheet and email sync products.  

Exports running in the context of a Flow will execute asynchronously and automatically break exported data down into one or more pages and then stream those pages to one or more [Imports](#Import).  

##### Export Related HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | :----: | :---- |
| /exports | POST | 201 | Create new export. |
| /exports/{_id} | PUT | 200 | Update existing export. |
| /exports/{_id} | GET | 200 | Retrieve existing export.  |
| /exports/{_id} | DELETE | 204 | Delete existing export. |
| /exports/{_id}/distributed | PUT | 200 | Create or update a distributed component that is linked to an existing export. |
| /exports/{_id}/distributed | GET | 200 | Retrieve a distributed component that is linked to an existing export. |
| /{apiIdentifier} | POST | 200 | Invoke an export. |
| /exports/preview | POST | 200 | Invoke an export in test mode before saving it to preview the results.  |

The following sections are organized by application and adaptor, and ordered by popularity.

### NetSuite Distributed Adaptor Exports
If you are using the Integrator to build NetSuite based integrations, we highly recommend you install our Distributed Adaptor (DA for short).  It's a SuiteApp that gets installed as a Bundle directly in your NetSuite account.  Once installed, it enables a super powerful export engine that can both export NetSuite data in realtime, intercepting events in NetSuite as they happen, and also run batch based exports that utilize NetSuite's SuiteScript APIs for more advanced and efficient search and custom logic capabilities.  Two examples follow.  The first outlines the steps required to create a realtime export, and the second does the same for a batch based export.

#### NetSuite Realtime Export
##### First, create your export resource.  POST /exports
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
You should receive a response that includes the following fields.  Note that we will use the \_id in the next step.
```javascript
{
  "_id": "507f1f77bcf86cd799439044",
  "apiIdentifier": "e22ef316"
}  
```
##### Second, create your distributed export resource.  PUT /exports/507f1f77bcf86cd799439044/distributed
```javascript
{
  "recordType": "salesorder",
  "executionContext": ["userinterface", "webservices", "webstore"],
  "qualifier": "['total', '>=', '1000']",
  "hooks": {
    "preSend": {
      "fileInternalId": "1234",
      "function": "myPreSendLogic"
    }
  }
}
```

#### Relevant Schema Info
##### Export Resource Fields (/exports)
| Field | Description |
| :---- | :---- |
| **name** | Give your export an intuitive name to stay organized. |
| **_connectionId** | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **type** | . |
| **lastModified** | Read only field tracking last modified date/time. |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |
| **distributed.bearerToken** | . |

##### Distributed Export Resource Fields (/exports/{_id}/distributed)
| Field | Description |
| :---- | :---- |
| **recordType** | . |
| **executionContext** | . |
| **qualifier** | . |
| **hooks.preSend.fileInternalId** | . |
| **hooks.preSend.function** | . |

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
| Field | Description |
| :---- | :---- |
| **name** | Give your export an intuitive name to stay organized. |
| **_connectionId** | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **asynchronous** | . |
| **type** | . |
| **lastModified** | Read only field tracking last modified date/time. |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |
| **test.limit** | . |
| **delta.dateField** | . |
| **delta.dateFormat** | . |
| **once.booleanField** | . |
| **netsuite.type** | . |
| **netsuite.restlet.recordType** | . |
| **netsuite.restlet.searchId** | . |
| **netsuite.restlet.hooks.preSend.fileInternalId** | . |
| **netsuite.restlet.hooks.preSend.function** | . |
| **netsuite.skipGrouping** | . |
| **pageSize** | . |

### NetSuite (Non-Distributed) Adaptor Exports
If installing the Distributed Adaptor in your NetSuite account is not an option, you can still create NetSuite exports that use NetSuite's web services API.  The capabilities are slightly more limited and the performance can also be slower depending on the complexity of your exports, especially related to the number of dynamic searches each record will require before export.  But, the really nice thing about using the web services API is that you can get started right away, and you do not need to involve a NetSuite admin to install anything first.  With that, here is a sample NetSuite web services based export.  

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
| Field | Description |
| :---- | :---- |
| **name** | Give your export an intuitive name to stay organized. |
| **_connectionId** | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **asynchronous** | . |
| **type** | . |
| **lastModified** | Read only field tracking last modified date/time. |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |
| **test.limit** | . |
| **delta.dateField** | . |
| **delta.dateFormat** | . |
| **once.booleanField** | . |
| **netsuite.type** | . |
| **netsuite.searches.recordType** | . |
| **netsuite.searches.searchId** | . |
| **netsuite.skipGrouping** | . |
| **pageSize** | . |
| **hooks._preSavePageId** | . |

### REST API Adaptor Exports
The Integrator supports the ability to define exports for any RESTful JSON API.  Following is an example.

##### POST /exports
```javascript
{
  "name": "My Export",
  "_connectionId": "5587092ef78228000000000a",
  "asynchronous": true,
  "rest": {
    "relativeURI": "customers",
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
| Field | Description |
| :---- | :---- |
| **name** | Give your export an intuitive name to stay organized. |
| **_connectionId** | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **asynchronous** | . |
| **type** | . |
| **lastModified** | Read only field tracking last modified date/time. |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |
| **test.limit** | . |
| **rest.relativeURI** | . |
| **rest.method** | . |
| **rest.headers** | . |
| **rest.query** | . |
| **rest.resourcePath** | . |
| **rest.postBody** | . |
| **rest.pagingMethod** | . |
| **rest.nextPagePath** | . |
| **rest.pageArgument** | . |
| **pageSize** | . |
| **hooks._preSavePageId** | . |

### Webhook Adaptor Exports
The Integrator also supports the ability to integrate with any JSON based webhook.  An export record is used to register a listener that can then be posted to.  Following is a sample Webhook based export.

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
| Field | Description |
| :---- | :---- |
| **name** | Give your export an intuitive name to stay organized. |
| **_connectionId** | . |
| **type** | . |
| **lastModified** | Read only field tracking last modified date/time. |
| **apiIdentifier** | . |
| **_integrationId** | . |
| **_connectorId** | . |
| **webhook.verify** | . |
| **webhook.token** | . |
| **webhook.path** | . |
| **webhook.algorithm** | . |
| **webhook.encoding** | . |
| **webhook.key** | . |
| **webhook.header** | . |
| **hooks._preSavePageId** | . |


## Import

##### What is an Import?
Imports are used to insert data into an application.  Like [Exports](#Export), Imports can run standalone, or in the context of a [Flow](#Flow).  

Standalone Imports are useful for integrations where user actions (like clicking a button) need to invoke an application's API in real-time and return the results immediately.  Standalone imports are especially useful for applications that need to integrate with more than one other applications, and/or applications where the development environment is less than ideal and writing native API client code would be counter productive.  Celigo uses standalone Imports extensively in its spreadsheet and email sync products.  

Imports running in the context of a Flow will also execute synchronously but will channel results back to the flow so that stats can be reported in a job record.

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

The following sections are organized by application and adaptor, and ordered by popularity.  

### NetSuite Distributed Adaptor Imports
If you are using the Integrator to build NetSuite based integrations, we highly recommend you install our Distributed Adaptor (DA for short).  It's a SuiteApp that gets installed as a Bundle directly in your NetSuite account.  Once installed, it enables a super rich import engine that can be used to map and load NetSuite data in very creative (and efficient) ways.  Here are the steps required to create an import using the API.  Please note that the samples below were generated to illustrate capabilities, not to provide a fully functional NetSuite import.

##### First, create your import resource.  POST /imports
```javascript
{
  "name": "NetSuite Order Import",
  "_connectionId": "5587092ed7822800e040000a",
  "_integrationId": "5560092ed7922800000f000e",
  "distributed": true
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
| **lookups.lookupOperator** | . |
| **lookups.expression** | . |
| **lookups.includeInactive** | . |
| **lookups.allowFailures** | . |
| **mapping.fields.extract** | . |
| **mapping.fields.generate** | . |
| **mapping.fields.hardCodedValue** | . |
| **mapping.fields.lookup** | . |
| **mapping.lists.generate** | . |
| **mapping.lists.fields** | . |
| **hooks.preMap.fileInternalId** | . |
| **hooks.preMap.function** | . |
| **hooks.postMap.fileInternalId** | . |
| **hooks.postMap.function** | . |
| **hooks.postSubmit.fileInternalId** | . |
| **hooks.postSubmit.function** | . |

### NetSuite (Non-Distributed) Adaptor Imports
If installing the Distributed Adaptor in your NetSuite account is not an option, you can still create NetSuite imports that use NetSuite's web services API.  The capabilities are slightly more limited and the performance can also be slower depending on the complexity of your imports, especially related to the number of dynamic internal id lookups each record in NetSuite will require before import.  But, the really nice thing about using the web services API is that you can get started right away, and you do not need to involve a NetSuite admin to install anything first.  With that, here is a sample NetSuite web services based import.  Please note that this sample was generated to illustrate capabilities, not to provide a fully functional NetSuite import.

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
| **netsuite.customFieldMetadata** | . |
| **mapping.fields.extract** | . |
| **mapping.fields.generate** | . |
| **mapping.fields.hardCodedValue** | . |
| **mapping.fields.lookup** | . |
| **mapping.lists.generate** | . |
| **mapping.lists.fields** | . |

### REST API Adaptor Imports
The Integrator supports the ability to define imports for any RESTful JSON API.  Following are two examples, one simple, the other more complex.  

##### POST /imports
The following simple import will send a POST request to the /customers relative URI to create new customers.

```javascript
{
  "name": "REST API Sample Post",
  "_connectionId": "5587092ed7822800000d0c0a",
  "rest": {
    "relativeURI": "customers",
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

Here is a more complex import.  This import will check to see if a customer resource already exists (by performing the lookup defined by 'idLookup').  If the customer does exists the import will submit a PUT request to update the existing customer.  If the customer does not exist the import will submit a POST request to a different URI to create a new customer.

```javascript
{
  "name": "Composite Add or Update Import",
  "_connectionId": "5587092ed78228000000000a",
  "rest" : {
    "relativeURI": [
      "customers/{idLookup}",
      "customers"
    ],
    "method": [
      "PUT",
      "POST"
    ],
    "lookups" : [
      {
        "name": "idLookup",
        "relativeURI": "customers?ns_id={internalId}",
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
        "allowFailures": "false"
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
| **ignoreExistingLoE** | . |
| **ignoreMissing** | . |
| **ignoreMissingLoE** | . |
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
