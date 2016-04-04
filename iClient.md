iClient
=======

##### What is an iClient?

iClient provides the mode of authentication for the connectors to authenticate the resource access from integrator. oAuth 2.0 is the authorization protocol used for iClients.

#### Relevant Schema Info

##### iClient

| Field              | Required | Description                                                                       |
|:-------------------|:---------|:----------------------------------------------------------------------------------|
| **provider**       | Yes      | Service for which the connection to be established.                               |
| **_userId**        | Yes      | Unique identifier of the user creating the iClient.                               |
| **lastModified**   | No       | Read only field tracking last modified date/time.                                 |
| **clientId**       | Yes      | Unique identifier created for the iClient                                         |
| **clientSecret**   | Yes      | Secret key for accessing the service used at the authorization alongwith clientId |
| **scope**          | No       | An array providing the access permissions with respect to each resource.          |
| **scopeDelimiter** | No       | Delimiter in the scope array.                                                     |
