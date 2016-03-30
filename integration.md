Integration
===========

##### What is an Integration?

Integrations are used group one or more [Imports](#Import), [Exports](#Export), or [Flows](#Flow). Integrations will show as tiles in the Integrator dashboard.

##### Integration Related HTTP Endpoints

| Relative URI                               | Method | Success Code | Description                                          |
|:-------------------------------------------|:-------|:------------:|:-----------------------------------------------------|
| /integrations                              | GET    |     200      | List all integrations.                               |
| /integrations                              | POST   |     201      | Create new integration.                              |
| /integrations/{_id}                        | PUT    |     200      | Update existing integration.                         |
| /integrations/{_id}                        | GET    |     200      | Retrieve existing integration.                       |
| /integrations/{_id}                        | DELETE |     204      | Delete existing integration.                         |
| /integrations/{_connectorId}/install       | POST   |     200      | Install connector.                                   |
| /integrations/{_id}/install                | DELETE |     200      | Uninstall connector.                                 |
| /integrations/{_id}/installer/{function}   | PUT    |     200      | Invoke 'function' (belonging to installer module).   |
| /integrations/{_id}/uninstaller/{function} | PUT    |     200      | Invoke 'function' (belonging to uninstaller module). |
| /integrations/{_id}/settings/{function}    | PUT    |     200      | Invoke 'function' (belonging to settings module).    |

#### Relevant Schema Info

##### Integration

| Field                | Required | Description                                                                            |
|:---------------------|:---------|:---------------------------------------------------------------------------------------|
| **_id**              | Yes      | System generated unique identifier for this integration.                               |
| **name**             | Yes      | Give your integration an intuitive name to stay organized.                             |
| **description**      | No       | Brief description on the integration group created                                     |
| **lastModified**     | No       | Read only field tracking last modified date/time.                                      |
| **_connectorId**     | Yes      | If this flow belongs to a connector, this value will be hold the id of that connector. |
| **mode**             | No       | Field that determines the mode of the integration i.e. install, settings, uninstall    |
| **settings**         | No       | Configuration of the integration that is persisted in the database                     |
| **version**          | No       | If this flow belongs to a connector, this value will be hold the id of that connector. |
| **tag**              | No       | .                                                                                      |
| **updateInProgress** | No       | Boolean value.                                                                         |
| **install**          | No       | If this flow belongs to a connector, this value will be hold the id of that connector. |
