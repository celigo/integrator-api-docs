Integration API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/integrations|GET|200|Get all integrations.|
|/integrations/{_id}|GET|200|Get a specific integration.|
|/integrations|POST|201|Create a new integration.|
|/integrations/{_id}|PUT|200|Update a specific integration.|
|/integrations/{_id}|DELETE|204|Delete a specific integration.|

### SmartConnector Only Fields

| Field| Description|
|:------------|:------------|
| **mode**|For the most part this is a read-only field used by the integrator.io UI.  Valid values are 'install', 'settings', and 'uninstall'.  The value 'install' is used to indicate that an integration is being installed by the user.  The value 'settings' is used to indicate that an integration is done installing, and the user now has access to the settings page for the integration.  The value 'uninstall' is used to indicate that an integration is being uninstalled.|
| **settings**|???|
| **version**|???|
| **updateInProgress**|???|
| **install**|???|

## Examples

#### 1.  Get a specific integration.

```
GET /v1/integrations/57974ed227a82a3475cecb15 HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "_id": "57974ed227a82a3475cecb15",
    "lastModified": "2017-09-15T10:55:15.675Z",
    "name": "BigCommerce - NetSuite Connector",
    "_connectorId": "57179182e0a908200c2781d9",
    "install": [
        {
            "name": "NetSuite Connection",
            "description": "Configure NetSuite account credentials",
            "imageURL": "/images/company-logos/netsuite.png",
            "completed": false,
            "installerFunction": "verifyNetSuiteConnection",
            "uninstallerFunction": "deleteNetSuiteConnection",
            "_connectionId": "57974ed21d2e4ab87ae46d99"
        },
        {
            "name": "BigCommerce Connection",
            "description": "Configure BigCommerce store credentials",
            "imageURL": "/images/company-logos/BigCommerce.png",
            "completed": false,
            "installerFunction": "verifyBigCommerceConnection",
            "uninstallerFunction": "deleteBigCommerceConnection",
            "_connectionId": "57974ed227a82a3475cecb17"
        },
        {
            "name": "Integrator Bundle",
            "description": "Install Integrator Bundle in NetSuite",
            "imageURL": "/images/company-logos/netsuite.png",
            "installURL": "",
            "completed": false,
            "installerFunction": "verifyIntegratorBundleInstallation",
            "uninstallerFunction": "uninstallVerifyIntegratorBundle"
        },
        {
            "name": "BigCommerce Bundle",
            "description": "Install BigCommerce Bundle in NetSuite",
            "imageURL": "/images/company-logos/netsuite.png",
            "installURL": "",
            "completed": false,
            "installerFunction": "verifyBigCommerceBundleInstallation",
            "uninstallerFunction": "uninstallVerifyBigCommerceBundle"
        }
    ],
    "mode": "install",
    "settings": {
        "sections": [
            {
                "temp": "remove_me"
            }
        ],
        "commonresources": {
            "genericExportApiIdentifier": "e7654358b7",
            "bigcommerceConnectionId": "57974ed227a82a3475cec222",
            "netsuiteConnectionId": "57464ed11d2e4ab87ae46d99"
        }
    },
    "version": "1.0.1"
}
```
