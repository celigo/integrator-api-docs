Integration API
===========
>**Guidance**: this API is typically only needed for SmartConnector development where you need to work with the SmartConnector only fields listed below, or Embedded Integration development where you want to automate deploying your integration solution (either via cloning or dynamically creating everything from scratch).


### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/integrations|GET|200|Get all integrations.|
|/integrations/{_id}|GET|200|Get a specific integration.|
|/integrations|POST|201|Create a new integration.|
|/integrations/{_id}|PUT|200|Update a specific integration.|
|/integrations/{_id}|DELETE|204|Delete a specific integration.|
|/integrations/{_id}/clone|POST|201|Clone a specific integration.|

### SmartConnector Only Fields

| Field| Description|
|:------------|:------------|
| **mode**|For the most part this is a read-only field used by the integrator.io UI.  Valid values are 'install', 'settings', and 'uninstall'.  The value 'install' is used to indicate that the integration is still being installed by the user.  The value 'settings' is used to indicate that the integration is done installing, and that the user has access to the settings page for the integration.  The value 'uninstall' is used to indicate that the integration is being uninstalled.|
| **settings**|This field is used to store the settings (i.e. the configuration) for the SmartConnector integration.  For example, when a user opens the settings page for a SmartConnector integration running in their account, and they change a setting, this field is updated, and then also propagated to the SmartConnector's backend to adjust the integration accordingly.|
| **version**|This field is used to store the version of an integration running in the user's integrator.io account.  Whenever you push any managed updates to a user's integrator.io account you should always increment the version field as the final step.|
| **updateInProgress**|This field is automatically set to 'true' whenever you push a managed update to a user's integrator.io account.  You must explicitly set this field back to 'false' when your update is done.  While updateInProgress is 'true' the user will not be allowed to change any settings or mappings in their integration.|
| **install**|This array field is used to specifcy all of the steps that are needed to install the SmartConnector integration in a user's integrator.io account, and is also used to track the state of each step so that the integrator.io UI knows which ones are done vs which one the user should do next, etc...|

## Examples

#### 1.  Get a specific DIY integration.

```
GET /v1/integrations/554155c853bb53af2900000b HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "_id": "554155c853bb53af2900000b",
    "lastModified": "2017-08-07T18:34:07.786Z",
    "name": "Licensing and Usage",
    "readme": "This integration is mission critical and keeps the licensing data..."
}
```


#### 2.  Get a specific SmartConnector integration (notice all the extra fields related to SmartConnectors).

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
