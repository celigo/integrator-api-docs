Connector API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-----------|:-------|:------------:|:-------------|
|/connectors|GET|200|Get all connectors.|
|/connectors/{_id}|GET|200|Get a specific connector.|
|/connectors|POST|201|Create a new connector.|
|/connectors/{_id}|PUT|200|Update a specific connector.|
|/connectors/{_id}|DELETE|204|Delete a specific connector.|
|/connectors/{_id}/installBase|GET|200|Get the install base for a specific connector.|
|/connectors/{_id}/update|PUT|200|Push update to _integrationIds[].|
|/connectors/{_id}/licenses|GET|200|Get all licenses for a specific connector.|
|/connectors/{_id}/licenses|POST|201|Create new license for a specific connector.|
|/connectors/{_id}/licenses/{_id_of_license}|GET|200|Get a specific license.|
|/connectors/{_id}/licenses/{_id_of_license}|PUT|200|Update a specific license.|
|/connectors/{_id}/licenses/{_id_of_license}|DELETE|204|Delete a specific license.|

## Examples

#### 1.  Get a specific connector.

```
GET /v1/connectors/54fa0b38a7044f9252000036 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "_id": "54fa0b38a7044f9252000036",
    "name": "Shopify - NetSuite Connector",
    "description": "Shopify - NetSuite Connector helps retailers combine the powerful Shopify eCommerce platform with the proven back-office features of NetSuite and keep the orders, customers, fulfillments, billings, items & inventory levels in sync.",
    "imageURL": "/images/company-logos/shopify-netsuite.png",
    "websiteURL": "http://www.celigo.com/products/netsuite-shopify-connector/",
    "contactEmail": "sales@celigo.com",
    "handle": "sc4n",
    "published": true,
    "managed": true,
    "_integrationId": "551c7be9accca83b3e00000c",
    "_stackId": "5593228187085d362c000009",
    "installerFunction": "installConnector",
    "updateFunction": "updateConnector",
    "preUninstallFunction": "preUninstallFunction",
    "uninstallerFunction": "uninstallConnector",
    "externalInstallerFunction": "installConnectorFromExternalApp",
    "lastModified": "2017-09-12T11:47:25.957Z",
    "_sharedImportIds": [],
    "_sharedExportIds": [],
    "_iClientIdMap": [
        {
            "_id": false,
            "_iClientIds": [
                "58f9dfc4a7d2ca3238e19c89"
            ],
            "connection": {
                "type": "netsuite"
            }
        }
    ],
    "applications": [
        "netsuite",
        "shopify"
    ],
    "oAuthServerFlow": {
        "_iClientId": "54fa0c93a7044f9252000038"
    }
}
```

#### 2.  Get all licenses for a specific connector.

```
GET /v1/connectors/54fa0b38a7044f9252000036/licenses HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
[
    {
        "_id": "59ef03a31ce1f53606837667",
        "expires": "2018-11-16T18:29:59.999Z",
        "created": "2017-10-24T09:10:59.408Z",
        "opts": {
            "addonLicenses": [
                {
                    "licenses": [
                        {
                            "addOnEdition": "standard"
                        }
                    ],
                    "type": "store"
                }
            ],
            "connectorEdition": "standard"
        },
        "user": {
            "email": "sathvika.dogiparthi@celigo.com",
            "_id": "585222eb73409e646b7375ed",
            "name": "Sathvika Dogiparthi"
        },
        "_integrationId": "59ef03c4b62d305b3c4534d5"
    },
    {
        "_id": "59dc541a93d54c44a7840220",
        "expires": "2025-07-01T00:00:00.000Z",
        "created": "2017-10-10T05:01:14.178Z",
        "opts": {
            "addonLicenses": [
                {
                    "licenses": [
                        {
                            "addOnEdition": "enterprise"
                        }
                    ],
                    "type": "store"
                }
            ],
            "connectorEdition": "enterprise"
        },
        "user": {
            "email": "ron.esguerra@celigo.com"
        }
    }
]
```
#### 3.  Create a new license for a specific connector.
Important: the API to create a new license (vs update an existing license) is somewhat unique in that you need to supply the email address of the integrator.io user in the post body, and then the integrator.io backend will use this email to dynamically find the correct user.  Assuming the email is a valid user, you will then see a read-only 'user' property returned, and then once the user clicks the install button for your connector in their integrator.io account you will see their '\_id' and 'name' values as well.

```
POST /v1/connectors/54fa0b38a7044f9252000036/licenses HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "email": "test@celigo.com",
    "expires": "2017-11-01T18:29:59.999Z",
    "opts": {
        "addonLicenses": [
            {
                "licenses": [
                    {
                        "addOnEdition": "standard"
                    }
                ],
                "type": "store"
            }
        ],
        "connectorEdition": "standard"
    }
}

```

Sample Response:

```
{
    "_id": "59ef68b384b38c7986d54549",
    "expires": "2017-11-01T18:29:59.999Z",
    "created": "2017-10-24T16:22:11.448Z",
    "opts": {
        "connectorEdition": "standard",
        "addonLicenses": [
            {
                "type": "store",
                "licenses": [
                    {
                        "addOnEdition": "standard"
                    }
                ]
            }
        ]
    },
    "user": {
        "email": "test@celigo.com"
    }
}
```

#### 4.  Update the license created above to change the expires date and also provision the enterprise version of the connector.

```
PUT /v1/connectors/54fa0b38a7044f9252000036/licenses/59ef68b384b38c7986d54549 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "expires": "2020-11-01T18:29:59.999Z",
    "opts": {
        "addonLicenses": [
            {
                "licenses": [
                    {
                        "addOnEdition": "enterprise"
                    }
                ],
                "type": "store"
            }
        ],
        "connectorEdition": "enterprise"
    } 
}
```

Sample Response:

```
{
    "_id": "59ef68b384b38c7986d54549",
    "expires": "2020-11-01T18:29:59.999Z",
    "created": "2017-10-24T16:22:11.448Z",
    "opts": {
        "connectorEdition": "enterprise",
        "addonLicenses": [
            {
                "type": "store",
                "licenses": [
                    {
                        "addOnEdition": "enterprise"
                    }
                ]
            }
        ]
    },
    "user": {
        "email": "test@celigo.com",
        "_id": "585222eb73409e646b7375ff",
        "name": "Celigo Tester"
    },
    "_integrationId": "59ef03c4b62d305b3c45f4ee"
}
```

#### 5.  Create a license for someone that has not signed up for integrator.io yet.

```
POST /v1/connectors/54fa0b38a7044f9252000036/licenses HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{
    "email": "does_not_exist@celigo.com",
    "expires": "2017-11-01T18:29:59.999Z",
    "opts": {
        "addonLicenses": [
            {
                "licenses": [
                    {
                        "addOnEdition": "standard"
                    }
                ],
                "type": "store"
            }
        ],
        "connectorEdition": "standard"
    }
}
```

Sample Response:

```
{
    "errors": [
        {
            "code": "invalid_user",
            "message": "We were not able to find any user with the provided email address.  Please ask the user to first create an integrator.io account."
        }
    ]
}
```
