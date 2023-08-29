iClient API
===========
>**Guidance**: currently the integrator.io UI does not support creating and/or editing iClient resources, and using the API is the only way to work with this resource type.

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
| **provider**|Supported providers include the following: google, salesforce, azureoauth, windowslive, shopify, integrator, zendesk, bigcommerce, netsuite, ebay, amazonmws, ebay-xml, asana, box, dropbox, clover, servicenow, jobvite, twilio, certify', dropbox, squareup, docusign, and woocommerce|
| **oauth2.clientId**||
| **oauth2.clientSecret**||
| **oauth2.scope**||
| **oauth2.scopeDelimiter**||
| **oauth2.redirectUri**||
| **netsuite.consumerKey**||
| **netsuite.consumerSecret**||
| **salesforce.clientId**||
| **salesforce.clientSecret**||
| **salesforce.apiToken**||
| **salesforce.privateKey**||
| **salesforce.publicKey**||
| **amazonmws.accessKeyId**||
| **amazonmws.secretKey**||
| **ebay.appId**||
| **ebay.devId**||
| **ebay.certId**||
| **ebay.ruName**||


## Examples

#### 1.  Get all iClients.

```
GET /v1/iclients HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
[
    {
        "_id": "123ab456c789de1011fg12h13i",
        "name": "Amazon - North America",
        "lastModified": "2017-03-15T10:07:54.070Z",
        "provider": "amazonmws",
        "amazonmws": {
            "accessKeyId": "abcdef12345",
            "secretKey": "******"
        }
    },
    {
        "_id": "123ab456c789de1011fg12h13i",
        "name": "eBay Sandbox",
        "lastModified": "2017-04-12T07:50:56.863Z",
        "provider": "ebay-xml",
        "ebay": {
            "appId": "Celigo-XXXX",
            "devId": "abcdef12345",
            "certId": "******",
            "ruName": "Celigo-ABC"
        }
    },
    {
        "_id": "123ab456c789de1011fg12h13i",
        "name": "eBay",
        "lastModified": "2017-04-12T07:51:17.839Z",
        "provider": "ebay-xml",
        "ebay": {
            "appId": "Celigo-YYYY",
            "devId": "zyxwvu12345",
            "certId": "******",
            "ruName": "Celigo-XYZ"
        }
    },
    {
        "_id": "123ab456c789de1011fg12h13i",
        "name": "NetSuite",
        "lastModified": "2017-04-21T10:32:37.100Z",
        "provider": "netsuite",
        "netsuite": {
            "consumerKey": "******",
            "consumerSecret": "******"
        }
    },
    {
        "_id": "123ab456c789de1011fg12h13i",
        "lastModified": "2015-12-09T11:17:23.235Z",
        "provider": "Shopify",
        "oauth2": {
            "clientId": "c12345",
            "clientSecret": "******",
            "scope": [
                "read_products",
                "write_products",
                "read_customers",
                "read_orders",
                "write_orders"
            ],
            "scopeDelimiter": ","
        }
    }
]
```
