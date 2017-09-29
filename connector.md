Connector API
=========

##### What is a Connector?

Connectors represent fully functional pre-built integrations that any user can install into their Integrator account, directly from the Integrator Marketplace. Connectors include an installer, uninstaller, and a settings interface. Connector developers can push updates at any time for their Connector to their entire install base. Connectors are mostly made up of code (that subsequently uses the Integrator API to interact with a user's account). A Connector can create any number of components in a user's account. All components created in a user's account are tagged with the Connector's \_id (via the \_connectorId field). A Connector can only modify components tagged with its own \_id. The schema below mostly serves the purpose of listing a Connector in the Integrator Marketplace (i.e. description, imageURL, websiteURL, etc...).

##### Connector Related HTTP Endpoints

| Relative URI                  | Method | Success Code | Description                                                            |
|:------------------------------|:-------|:------------:|:-----------------------------------------------------------------------|
| /connectors                   | GET    |     200      | List all connectors.                                                   |
| /connectors                   | POST   |     201      | Create new connector.                                                  |
| /connectors/{_id}             | PUT    |     200      | Update existing connector.                                             |
| /connectors/{_id}             | GET    |     200      | Retrieve existing connector.                                           |
| /connectors/{_id}             | DELETE |     204      | Delete existing connector.                                             |
| /connectors/{_id}/installBase | GET    |     200      | Retrieve information related to install base for given connector \_id. |
| /connectors/{_id}/update      | PUT    |     200      | Push update to _integrationIds[] provided in the request body.         |

#### Relevant Schema Info

##### Connector

| Field                             | Required | Description                                                                              |
|:----------------------------------|:---------|:-----------------------------------------------------------------------------------------|
| **_id**                           | Yes      | System generated unique identifier for this connector.                                   |
| **name**                          | Yes      | Give your connector an intuitive name to stay organized.                                 |
| **handle**                        | .        | .                                                                                        |
| **legacyId**                      | .        | .                                                                                        |
| **lastModified**                  | No       | Read only field tracking last modified date/time.                                        |
| **published**                     | .        | .                                                                                        |
| **description**                   | No       | Brief description on the connector.                                                      |
| **imageURL**                      | .        | .                                                                                        |
| **_integrationId**                | Yes      | If this flow is part of an integration, this value will hold the id of that integration. |
| **websiteURL**                    | .        | .                                                                                        |
| **oauth2ResultsURL**              | .        | .                                                                                        |
| **managed**                       | .        | .                                                                                        |
| **_stackId**                      | .        | .                                                                                        |
| **installerFunction**             | .        | .                                                                                        |
| **updateFunction**                | .        | .                                                                                        |
| **uninstallerFunction**           | .        | .                                                                                        |
| **sharedImportIds**               | .        | .                                                                                        |
| **sharedExportIds**               | .        | .                                                                                        |
| **oAuthServerFlow._iClientId**    | Yes      | .                                                                                        |
| **oAuthServerFlow.putConnection** | Yes      | Boolean value.                                                                           |
| **repository.name**               | Yes      | Boolean value.                                                                           |
