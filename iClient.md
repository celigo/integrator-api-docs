iClient API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/iclients|GET|200|Get all iClients.|
|/iclients/{_id}|GET|200|Get a specific iClient.|
|/iclients|POST|201|Create a new iClient.|
|/iclients/{_id}|PUT|200|Update a specific iClient.|
|/iclients/{_id}|DELETE|204|Delete a specific iClient.|

### Fields

| Field| Description|
|:------------|:------------|
| **provider**|???|
| **oauth2.clientId**|???|
| **oauth2.clientSecret**|???|
| **oauth2.scope**|???|
| **oauth2.scopeDelimiter**|???|
| **oauth2.redirectUri**|???|
| **netsuite.consumerKey**|???|
| **netsuite.consumerSecret**|???|
| **salesforce.clientId**|???|
| **salesforce.clientSecret**|???|
| **salesforce.apiToken**|???|
| **salesforce.privateKey**|???|
| **salesforce.publicKey**|???|
| **amazonmws.accessKeyId**|???|
| **amazonmws.secretKey**|???|
| **ebay.appId**|???|
| **ebay.devId**|???|
| **ebay.certId**|???|
| **ebay.ruName**|???|


## Examples

#### 1.  Get a specific iClient.

```
GET /v1/iclients/{_id} HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "_userId": "5e03061cda20740022300f09",
  "scope":"*"
}
```
