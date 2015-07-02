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

Mapping data from one application to another is never as easy as it sounds.  For example, often the media types will not match and you need to transform one data format into another (e.g. CSV to JSON).  Then sometimes media types will match but field ids will not (e.g. first_name to firstName).  Even then, very rarely,  both media types and field ids will match but semantics will still vary (e.g. 'name' in one app might be different than 'name' in another).  

Very important #1, every Export that you define will always generate JSON data when you invoke it.  Regardless of what media type an application uses natively to store and transmit data, when you use the Integrator to export data you will always get JSON.  JSON is the canonical format.

Very important #2, mapping happens at time of import, and your mapping objects will always be tightly coupled to one or more Import objects.  One of the fundamental design principles of the Integrator is to get export data into a canonical format, and then move the export data as close as possible to the import application (ideally the canonical export data is transferred raw into the import application), and then invoke the mapping and semantical transformation logic.

Note that if a mapping is not defined in your Import the export data will simply be passed along as-is (in JSON format).

### Mapping Schema

Before we look at any sample mappings here are the important properties that you should understand before building anything.  

| Field | Description |
| :---- | :---- |
| **extract** | Used to specify the JSON path of the export data that you want to map. Regardless of the mediaType used by the application or system you are exporting data from the Integrator will always transform that data into a canonical JSON format. |  
| **generate** | Used to specific the path (not always JSON in this case) that you want to construct for your import. |
| **dataType** | Used to tell the mapper what data type you would like to generate for your import.  Possible values include string, number, and boolean. |
| **hardCodedValue** | Used to specify that a specific value should always be used for the generate data. |
| **lookup** | Used to reference a lookup object in the lookups section of our your mapping. The different types of lookups and how to configure them will be defined in depth below. |
| **fields** | An array of field mappings. |
| **lists** | An array of list mappings. |

Alright, let's dive right into some examples so we can see how these things all piece together.  

#### NetSuite Distributed Adaptor Import Example

If you are using the Integrator to integrate NetSuite in any way, we highly recommend that you install our Distributed Adaptor (DA for short).  It's a SuiteApp that gets installed as a Bundle directly in your NetSuite account.  Once installed, it enables a super rich import engine that can be used to map and load NetSuite data in very creative (and efficient) ways.  Here is what a mapping for the NetSuite DA looks like.  Please keep in mind that this sample was created for documentation purposes only, to illustrate different mapping capabilities, not to represent a working order import into NetSuite.

```javascript
{
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
Please see [NetSuite Distributed Imports](#NetSuite Distributed Imports) for instructions on how to include a mapping object in a distributed import.

#### REST API Import Example
TODO: everythign below still WIP
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
