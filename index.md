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
  "hooks": {
    "preSavePage": "5587092fd78228000000000b"
  }
}
```
### Distributed Exports
##### Sample Export Request
```javascript
{
  "name": "Realtime Item Export",
  "_connectionId": "5587092ed78128000000000a",
  "type": "distributed",
  "distributed": {
    "bearerToken": "********",
  }
  "hooks": {
    "preDistributedSend": "5567a92fd7822800bb00000f",
    "preSavePage": "5587092fd78228000000000a"
  }
}
```
##### HTTP Endpoints
| Relative URI | Method | Success Code | Description |
| :---- | :---- | ----: | :---- |
| /exports/{_id}/distributed | PUT | 200 | Create or update a distributed component that is linked to an existing export. |
| /exports/{_id}/distributed | GET | 200 | Retrieve a distributed component that is linked to an existing export. |

#### NetSuite Realtime Exports
##### Sample Distributed Request
```javascript
{
  "recordType": "salesorder",
  "executionContext": ["userinterface", "webservices", "webstore"],
  "qualifier": "['total', '>=', '1000']"
}
```
##### Sample Distributed Response
```javascript
{
  "recordType": "salesorder",
  "executionContext": ["userinterface", "webservices", "webstore"],
  "qualifier": "['total', '>=', '1000']"
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
  "distributed": "true",
  "hooks": {
    "preMapping": "5587092fd78228000000000a",
    "postMapping": "5561092fd78228000000010b"
  }
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
    "fields": [
      {"extract": "campaign", "generate": "leadSource", "lookup": "lookup1" },
      {"extract": "company", "generate": "customer", "lookup": "lookup2" },
      {"generate": "transaction", "lookup": "lookup3" }
    ],
    "lookups": {
      "lookup1": {
        "map": {
          "key1": "value1",
          "key2": "value2"
        },
        "allowFailures": ""
      },
      "lookup2": {
        "recordType": "",
        "searchField": "",
        "resultField": "",
        "allowFailures": "",
        "pickFirstMatch": ""
      },
      "lookup3": {
        "recordType": "",
        "expression": "[['tranid', 'is', '{OrderNumber}']]",
        "allowFailures": "",
        "pickFirstMatch": ""
      }
    }
  }
}
```
#### Sample Response
```javascript

```
## Mapping
The mapping resource is used to describe the data transformation that should take place between a source document (perhaps from an export) and the document to be imported into a destination system.  

Note that a mapping resource is an optional feature of an [Import](#import). By default, if no explicit mapping is defined imports will simply pass along the source document to the destination system.

### Field Mapping
The most basic transformation possible is mapping fields from one name to another.

Consider this sample source document:
```javascript
var contact = {
  first: 'John',
  last: 'Hancock',
  age: '43',
  isLocal: 'true'
}
```
If the destination system required only the first and last names, and used the properties firstName and lastName, the mapping below would accomplish this transformation:

```javascript
{
  name: 'example-mapping-1',
  fields: [
    {extract: 'first', generate: 'firstName'},
    {extract: 'last', generate: 'lastName'},
  ]
}
```
The mapping above would generate the document below:

```javascript
{
  firstName: 'John',
  lastName: 'Hancock'
}
```
Note that if an [Import](#import) has a mapping defined, only the fields explicitly defined in the mapping will be included in the document passed to the destination system.

The simple example above can be enhanced in several ways. First, the values of the _extract_ and _generate_ fields respect JSON dot-notation. This allows the mapping to traverse the source document when extracting values, and likewise generate a complex document.  It is also possible to add hardcoded fields to the generated document.  Finally, simple data type conversions are available. Supported types are _string, number, boolean_. The example below demonstrates these additional features:

```javascript
var mapping = {
  name: 'example-mapping-2',
  fields: [
    {extract: 'first', generate: 'contact.firstName'},
    {extract: 'last', generate: 'contact.lastName'},
    {extract: 'age', generate: 'contact.age', dataType: 'number'},
    {extract: 'isLocal', generate: 'isLocal', dataType: 'boolean'},
    {generate: 'isNew', hardCodedValue: true}
  ]
}

var generatedDocument = {
  isNew: true,
  isLocal: true,
  contact: {
    firstName: 'John',
    lastName: 'Hancock',
    age: 43
  }
}
```
Note that when type conversion is specified, the following rules apply:
* boolean: null, undefined, false, NaN, 'false', 'off', and 'no' resolve to false. All other values resolve to true.
* number: null, undefined, NaN, and any non-numeric string value resolve to 0.
* string: simply the string equivalent of the value.

There is one final field mapping feature. The use of simple expressions as the _extract_ value. Using an expression as an 'extract' value is a way to combine any number of source fields into a single field in the generated document.

The example below will demonstrate the use and syntax of an _extract_ expression:
```javascript
var issue = {
  id: 10002,
  name: 'unable to login',
  message: 'User forgot his password. Their password has been reset.',
  userId: 12345
}

var mapping = {
  name: 'example-mapping-3',
  fields: [
    {extract: 'Issue-{id}: *{name}*  {message}', generate: 'message'},
    {extract: 'first', generate: 'contact.firstName'},
  ]
}

var generatedDocument = {
  message: 'Issue-10002: *unable to login*  User forgot his password. Their password has been reset.'
}
```
## Flow
## Integration
## Connector
## IClient
## Job
## Job Error
## Retry
