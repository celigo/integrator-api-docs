# Introduction
The Integrator is an API first platform.  Every new feature gets released here first, and then shortly later in the UI.  The Integrator API is RESTful, uses JSON, and is secured by Bearer Tokens.  The target audience for the API is developers who wish to build integration based apps.  Complementing the API, the Integrator supports a rich customization framework.  The integrations that you build can include an installer, settings pages, along with any number of hooks (hooks give you the ability to write your own custom code, and are useful for requirements that cannot be implemented via configuration alone).  Any integrations that you build can be listed in the Integrator Marketplace for easy install by any other Integrator user.  Enjoy!

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
##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | ----: | :---- |
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
    "preSavePage": "5587092fd78228000000000b"
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
    "preSavePage": "5587092fd78228000000000b"
  }
}
```
### Distributed Export
##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | ----: | :---- |
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
## Import
##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | ----: | :---- |
| /imports | POST | 201 | Create new imports. |
| /imports/{_id} | PUT | 200 | Update existing import. |
| /imports/{_id} | GET | 200 | Retrieve existing import.  |
| /imports/{_id} | DELETE | 204 | Delete existing import. |


##### Sample Import Request
```javascript
{
  "name": "My Import",
  "_connectionId": "5587092ed78228000000000a",
  "_mappingId": "5587092ed21228003300000a",
  "rest": {
    "relativeURI": "/customers",
    "method": "POST"
  },
  "hooks": {
    "preMapping": "5587092fd78228000000000a",
    "postMapping": "5561092fd78228000000010b"
  }
}
```
##### Sample Import Response
```javascript
{
  "_id": "507f1f77bcf86cd799439032",
  "apiIdentifier": "i66ec314",
  "name": "My Import",
  "_connectionId": "5587092ed78228000000000a",
  "_mappingId": "5587092ed21228003300000a",
  "rest": {
    "relativeURI": "/customers",
    "method": "POST"
  },
  "hooks": {
    "preMapping": "5587092fd78228000000000a",
    "postMapping": "5561092fd78228000000010b"
  }
}
```
### Distributed Imports
##### Sample Import Request
```javascript
{
  "name": "My Distributed Import",
  "_connectionId": "5587092ed78228000000000a",
  "distributed": "true"
}
```
##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | ----: | :---- |
| /imports/{_id}/distributed | PUT | 200 | Create or update a distributed component that is linked to an existing import. |
| /imports/{_id}/distributed | GET | 200 | Retrieve a distributed component that is linked to an existing import. |

#### NetSuite Distributed Imports
##### Sample Distributed Request
```javascript
{
  "recordType": "customer",
  "mapping": {
    "fields": [],
    "lists": [],
    "lookups": []
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
## Mapping

Mappings are tightly coupled to [Imports](#import).  For non-distributed imports mappings are their own resource.  For distributed imports mappings must be submitted via the [Distributed Imports](#Distributed Imports) endpoint and are tightly coupled to an import resource.

Note that a mapping is an optional feature of an [Import](#import), and if no explicit mapping is defined data will be passed along to the import application in Integrator's canonical JSON format.  

### Mapping Schema

Here are the important fields that you should understand before constructing your first mapping (for an import).

| Field | Description |
| :---- | :---- |
| **extract** | Used to specify the JSON path of the export data that you want to map. Regardless of the mediaType used by the application or system you are exporting data from the Integrator will always transform that data into a canonical JSON format. |  
| **generate** | Used to specific the path (not always JSON in this case) that you want to construct for your import. |
| **dataType** | Used to tell the mapper what data type you would like to generate for your import.  Possible values include string, number, and boolean. |
| **hardCodedValue** | Used to specify that a specific value should always be used for the generate data. |
| **lookup** | Used to reference a lookup object in the lookups section of our your mapping. The different types of lookups and how to configure them will be defined in depth below. |
| **fields** | An array of field mappings. |
| **lists** | An array of list mappings. |

Alright, now that we have some basic definitions let's dive right into some examples so we can see how these things piece together.  
#### NetSuite Distributed Adaptor Import Example
```javascript
{
  "name": "Webstore to NetSuite Sales Order",
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
  ],
  "lookups": [
    "customerLookup": {
     "recordType": "customer",
     "searchField": "email",
     "resultField": "internalid",
     "allowFailures": "false",
     "includeInactive": "false"
    },
    "currencyMap": {
      "map": {
        "USA": "USD",
        "JAPAN": "JPY",
        "UK": "GDP"
      },
      "allowFailures": "false"
    },
    "orderLookup": {
      "recordType": "transaction",
      "expression": "[['tranid', 'is', '{last_order}']]",
      "allowFailures": "true"
    },
    "itemLookup": {
     "recordType": "item",
     "searchField": "itemid",
     "resultField": "internalid",
     "allowFailures": "false",
     "includeInactive": "false"
    }
  ]
}
```
#### REST API Import Example
TODO: include dataType example
```javascript
{
  "name": "REST API Adaptor Customer Import",
  "fields": [
    {"extract": "city", "generate": "billcity"},
    {"extract": "zip", "generate": "billzip"},
    {"extract": "{first_name} {last_name}", "generate": "billaddressee"},
    {"extract": "charge_approved", "generate": "ccapproved"},
    {"extract": "memo", "generate": "message", "dataType": "string"},
    {"generate": "ismultishipto", "hardCodedValue": false},
    {"generate": "custbody_source", "hardCodedValue": "webstore"},
    {"extract": "email", "generate": "customer", "lookup": "customerLookup"},
    {"extract": "country", "generate": "currency", "lookup": "currencyMap"},
    {"extract": "last_order", "generate": "custbody_previous", "lookup": "orderLookup"}
  ],
  "lists": [
    {
      "generate": "items",
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
  ],
  "lookups": [
    "customerLookup": {
     "recordType": "customer",
     "searchField": "email",
     "resultField": "internalid",
     "allowFailures": "false",
     "includeInactive": "false"
    },
    "currencyMap": {
      "map": {
        "USA": "USD",
        "JAPAN": "JPY",
        "UK": "GDP"
      },
      "allowFailures": "false"
    },
    "itemLookup": {
     "recordType": "item",
     "searchField": "itemid",
     "resultField": "internalid",
     "allowFailures": "false",
     "includeInactive": "false"
    },
    "orderLookup": {
      "recordType": "salesorder",
      "expression": "[['tranid', 'is', '{OrderNumber}']]",
      "allowFailures": "true"
    }
  ]
}
```

Regardless of where a mapping lives the same schema is used to define how data should be transformed.  Please note that some mapping capabilities (and schema elements) are only relevant for specific applications.  

| Property | Type | Possible Values | Description |
| :----: | :----: | :----: | :---- |
| **name** | String | NetSuite Customer Import, Shopify Order Update, etc... | Give your mapping an intuitive name to help describe and/or distinguish it.  This value will show in the UI, and if you are building a Connector this value can be used as an external key field to facilitate finding and updating your mapping later. |
| **fields** | Array |  |  |
| **lists** | Array |  |  |
| **lookups** | Array |  |  |

### Field Mapping
The most basic transformation possible is mapping fields from one name to another.

### Field Mapping Schema
| Property | Type | Possible Values | Description |
| :----: | :----: | :----: | :---- |
| **extract** | String |  |  |
| **generate** | String |  |  |
| **lookup** | Ref |  |  |
| **dataType** | Enum |  |  |
| **hardCodedValue** | Mixed |  |  |

#### Field Mapping Examples
Consider this sample source document:
```javascript
var contact = {
  first: "John",
  last: "Hancock",
  age: "43",
  isLocal: "true"
}
```
If the destination system required only the first and last names, and used the properties firstName and lastName, the mapping below would accomplish this transformation:

```javascript
{
  name: "example-mapping-1",
  fields: [
    {extract: "first", generate: "firstName"},
    {extract: "last", generate: "lastName"},
  ]
}
```
The mapping above would generate the document below:

```javascript
{
  firstName: "John",
  lastName: "Hancock"
}
```
Note that if an [Import](#import) has a mapping defined, only the fields explicitly defined in the mapping will be included in the document passed to the destination system.

The simple example above can be enhanced in several ways. First, the values of the _extract_ and _generate_ fields respect JSON dot-notation. This allows the mapping to traverse the source document when extracting values, and likewise generate a complex document.  It is also possible to add hardcoded fields to the generated document.  Finally, simple data type conversions are available. Supported types are _string, number, boolean_. The example below demonstrates these additional features:

```javascript
var mapping = {
  name: "example-mapping-2",
  fields: [
    {extract: "first", generate: "contact.firstName"},
    {extract: "last", generate: "contact.lastName"},
    {extract: "age", generate: "contact.age", dataType: "number"},
    {extract: "isLocal", generate: "isLocal", dataType: "boolean"},
    {generate: "isNew", hardCodedValue: true}
  ]
}

var generatedDocument = {
  isNew: true,
  isLocal: true,
  contact: {
    firstName: "John",
    lastName: "Hancock",
    age: 43
  }
}
```
Note that when type conversion is specified, the following rules apply:
* boolean: null, undefined, false, NaN, "false", "off", and "no" resolve to false. All other values resolve to true.
* number: null, undefined, NaN, and any non-numeric string value resolve to 0.
* string: simply the string equivalent of the value.

There is one final field mapping feature. The use of simple expressions as the _extract_ value. Using an expression as an "extract" value is a way to combine any number of source fields into a single field in the generated document.

The example below will demonstrate the use and syntax of an _extract_ expression:
```javascript
var issue = {
  id: 10002,
  name: "unable to login",
  message: "User forgot his password. Their password has been reset.",
  userId: 12345
}

var mapping = {
  name: "example-mapping-3",
  fields: [
    {extract: "Issue-{id}: *{name}*  {message}", generate: "message"},
    {extract: "first", generate: "contact.firstName"},
  ]
}

var generatedDocument = {
  message: "Issue-10002: *unable to login*  User forgot his password. Their password has been reset."
}
```

### List Mapping Schema
| Property | Type | Possible Values | Description |
| :----: | :----: | :----: | :---- |
| **generate** | JSON Path |  |  |
| **fields** | Array |  |  |

### Lookup Schemas
#### Static Lookup Schemas

| Property | Type | Possible Values | Description |
| :----: | :----: | :----: | :---- |
| **map** | String |  |  |


"lookup1": {
  "map": {
    "key1": "value1",
    "key2": "value2"
  },
  "allowFailures": "false"
},
"lookup2": {
 "recordType": "contact",
 "searchField": "email",
 "resultField": "internalid",
 "allowFailures": "true",
 "includeInactive": "true"
},
"lookup3": {
  "recordType": "salesorder",
  "expression": "[['tranid', 'is', '{OrderNumber}']]",
  "allowFailures": "true"
}


## Flow
## Integration
## Connector
## IClient
## Job
## Job Error
## Retry
