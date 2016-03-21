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

| Field                       | Description                                                                                                                                |
|:----------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| **_id**                     | .                                                                                                                                          |
| **name**                    | Give your export an intuitive name to stay organized.                                                                                      |
| **_connectionId**           | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **type**                    | .                                                                                                                                          |
| **lastModified**            | Read only field tracking last modified date/time.                                                                                          |
| **apiIdentifier**           | .                                                                                                                                          |
| **_integrationId**          | .                                                                                                                                          |
| **_connectorId**            | .                                                                                                                                          |
| **distributed.bearerToken** | .                                                                                                                                          |

##### Distributed Export Resource Fields (/exports/{_id}/distributed)

| Field                            | Description |
|:---------------------------------|:------------|
| **_id**                          | .           |
| **recordType**                   | .           |
| **sublists**                     | .           |
| **executionContext**             | .           |
| **qualifier**                    | .           |
| **hooks.preSend.fileInternalId** | .           |
| **hooks.preSend.function**       | .           |
| **settings**                     | .           |
| **disabled**                     | .           |

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

| Field                                             | Description                                                                                                                                |
|:--------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| **_id**                                           | .                                                                                                                                          |
| **name**                                          | Give your export an intuitive name to stay organized.                                                                                      |
| **_connectionId**                                 | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **asynchronous**                                  | .                                                                                                                                          |
| **type**                                          | .                                                                                                                                          |
| **lastModified**                                  | Read only field tracking last modified date/time.                                                                                          |
| **apiIdentifier**                                 | .                                                                                                                                          |
| **_integrationId**                                | .                                                                                                                                          |
| **_connectorId**                                  | .                                                                                                                                          |
| **test.limit**                                    | .                                                                                                                                          |
| **delta.dateField**                               | .                                                                                                                                          |
| **delta.dateFormat**                              | .                                                                                                                                          |
| **once.booleanField**                             | .                                                                                                                                          |
| **netsuite.type**                                 | .                                                                                                                                          |
| **netsuite.restlet.recordType**                   | .                                                                                                                                          |
| **netsuite.restlet.searchId**                     | .                                                                                                                                          |
| **netsuite.restlet.hooks.preSend.fileInternalId** | .                                                                                                                                          |
| **netsuite.restlet.hooks.preSend.function**       | .                                                                                                                                          |
| **netsuite.skipGrouping**                         | .                                                                                                                                          |
| **pageSize**                                      | .                                                                                                                                          |

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

| Field                            | Description                                                                                                                                |
|:---------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| **_id**                          | .                                                                                                                                          |
| **name**                         | Give your export an intuitive name to stay organized.                                                                                      |
| **_connectionId**                | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **asynchronous**                 | .                                                                                                                                          |
| **type**                         | .                                                                                                                                          |
| **lastModified**                 | Read only field tracking last modified date/time.                                                                                          |
| **apiIdentifier**                | .                                                                                                                                          |
| **_integrationId**               | .                                                                                                                                          |
| **_connectorId**                 | .                                                                                                                                          |
| **test.limit**                   | .                                                                                                                                          |
| **delta.dateField**              | .                                                                                                                                          |
| **delta.dateFormat**             | .                                                                                                                                          |
| **once.booleanField**            | .                                                                                                                                          |
| **netsuite.type**                | .                                                                                                                                          |
| **netsuite.searches.recordType** | .                                                                                                                                          |
| **netsuite.searches.searchId**   | .                                                                                                                                          |
| **netsuite.skipGrouping**        | .                                                                                                                                          |
| **pageSize**                     | .                                                                                                                                          |
| **hooks._preSavePageId**         | .                                                                                                                                          |

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

| Field                    | Description                                                                                                                                |
|:-------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| **_id**                  | .                                                                                                                                          |
| **name**                 | Give your export an intuitive name to stay organized.                                                                                      |
| **_connectionId**        | The _id of the [Connection](#Connection) resource that should be used to access the system or application hosting the data being exported. |
| **asynchronous**         | .                                                                                                                                          |
| **type**                 | .                                                                                                                                          |
| **lastModified**         | Read only field tracking last modified date/time.                                                                                          |
| **apiIdentifier**        | .                                                                                                                                          |
| **_integrationId**       | .                                                                                                                                          |
| **_connectorId**         | .                                                                                                                                          |
| **test.limit**           | .                                                                                                                                          |
| **rest.relativeURI**     | .                                                                                                                                          |
| **rest.method**          | .                                                                                                                                          |
| **rest.headers**         | .                                                                                                                                          |
| **rest.query**           | .                                                                                                                                          |
| **rest.resourcePath**    | .                                                                                                                                          |
| **rest.postBody**        | .                                                                                                                                          |
| **rest.pagingMethod**    | .                                                                                                                                          |
| **rest.nextPagePath**    | .                                                                                                                                          |
| **rest.pageArgument**    | .                                                                                                                                          |
| **pageSize**             | .                                                                                                                                          |
| **hooks._preSavePageId** | .                                                                                                                                          |

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

| Field                    | Description                                           |
|:-------------------------|:------------------------------------------------------|
| **_id**                  | .                                                     |
| **name**                 | Give your export an intuitive name to stay organized. |
| **_connectionId**        | .                                                     |
| **type**                 | .                                                     |
| **lastModified**         | Read only field tracking last modified date/time.     |
| **apiIdentifier**        | .                                                     |
| **_integrationId**       | .                                                     |
| **_connectorId**         | .                                                     |
| **webhook.verify**       | .                                                     |
| **webhook.token**        | .                                                     |
| **webhook.path**         | .                                                     |
| **webhook.algorithm**    | .                                                     |
| **webhook.encoding**     | .                                                     |
| **webhook.key**          | .                                                     |
| **webhook.header**       | .                                                     |
| **hooks._preSavePageId** | .                                                     |