Connection
==========

Connections are used to store credentials, along with other access information for an application or system. Currently the Integrator supports the following connection types.

-	[NetSuite](#NetSuite Connection)
-	[REST API](#REST Connection)
-	[SFTP/FTP](#FTP Connection)
-	[S3](#S3)

##### Connection Related HTTP Endpoints

| Relative URI                       | Method | Success Code | Description                                                             |
|:-----------------------------------|:-------|:------------:|:------------------------------------------------------------------------|
| /connections                       | GET    |     200      | List all connections.                                                   |
| /connections                       | POST   |     201      | Create new connection.                                                  |
| /connections/{_id}                 | PUT    |     200      | Update existing connection.                                             |
| /connections/{_id}                 | GET    |     200      | Retrieve existing connection.                                           |
| /connections/{_id}                 | DELETE |     204      | Delete existing connection.                                             |
| /connections/{_id}/debug/{_typeId} | GET    |     201      | Retrieve the debug logs for the connection Id and the type Id mentioned |

TODO here! lots of routes to add still. :( GET /connections/:\_id/ping POST /connections/ping etc...

#### Relevant Schema Info

Below are the common properties on all connections:

| Field              | Description                                                                                                                                                                                                     |
|:-------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **_id**            | System generated unique identifier for this connection.                                                                                                                                                         |
| **name**           | Optional name that can be given to this connection.                                                                                                                                                             |
| **type**           | This lowercase string field identifies what application or system type the connection is being used for. The possible values are: ['netsuite', 'salesforce', 'ftp', 'sftp', 's3', 'magento', 'rest', 'wrapper'] |
| **lastModified**   | System generated date used to track the last point in time this resource was modified.                                                                                                                          |
| **offline**        | A flag to identify if the connection is in offline-mode. If this is true than no flows with imports or exports using this connection will be ran. Connections can be reset to online my re-authentication.      |
| **_connectorId**   | If this connection belongs to a connector, this value will be hold the id of that connector.                                                                                                                    |
| **_integrationId** | If this connection is part of an integration, this value will hold the id of that integration.                                                                                                                  |
| **debugDate**      | Mention duration in date and time, to start debug logging for specified duration. Maximum duration of 1 hour from the current date and time can be provided                                                     |

##### NetSuite Connection

| Field                                    | Required | Description                                                                                                                                                                                                                                                                                                 |
|:-----------------------------------------|:---------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **netsuite.account**                     | Yes      | This holds the Netsuite account number                                                                                                                                                                                                                                                                      |
| **netsuite.roleId**                      | Yes      | The NetSuite user that is configured must be configured with a role that has sufficient permissions                                                                                                                                                                                                         |
| **netsuite.email**                       | Yes      | Registered email id for the Netsuite account                                                                                                                                                                                                                                                                |
| **netsuite.password**                    | Yes      | NS account password                                                                                                                                                                                                                                                                                         |
| **netsuite.companyId**                   | No       | Company identifier that the user belongs to (required if it is sso login).                                                                                                                                                                                                                                  |
| **netsuite.userId**                      | No       | User identifier required incase of sso login                                                                                                                                                                                                                                                                |
| **netsuite.useSsoLogin**                 | No       | Boolean value. If set the login method would be single sign in.                                                                                                                                                                                                                                             |
| **netsuite.environment**                 | Yes      | This field determines the type of NS account that the user holds (Ex: production, sandbox, beta)                                                                                                                                                                                                            |
| **netsuite.requestLevelCredentials**     | No       | Boolean value. If set, login happens differently i.e. rather than authenticating to NetSuite by invoking login, users have the option of sending their credentials in the SOAP header of each request. Sending credentials with each request eliminates the need for session management and separate logins |
| **netsuite.dataCenterURLs**              | No       | .                                                                                                                                                                                                                                                                                                           |
| **netsuite.accountName**                 | No       | User account name with the Netsuite                                                                                                                                                                                                                                                                         |
| **netsuite.roleName**                    | No       | Role name for the account held with Netsuite (Ex: Administrator)                                                                                                                                                                                                                                            |
| **netsuite.concurrencyLevelRESTlet**     | No       | .                                                                                                                                                                                                                                                                                                           |
| **netsuite.concurrencyLevelWebServices** | No       | .                                                                                                                                                                                                                                                                                                           |

##### REST Connection

| Field                       | Required | Description                                                                                                                                                                                                                                                                                                                        |
|:----------------------------|:---------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **rest.mediaType**          | Yes      | Describes the format of the request/response body. The two supported types are, 'json' and 'urlencoded'.                                                                                                                                                                                                                           |
| **rest.baseURI**            | Yes      | The base URI of all endpoints. All imports and exports using this connection will provide only the relativeURI of their specific endpoint                                                                                                                                                                                          |
| **rest.bearerToken**        | No       | If this connection is using token based authentication, this is the auth token to use. When connections are returned from GET requests, this field is masked with \*\*\*\*\*                                                                                                                                                       |
| **rest.tokenLocation**      | No       | For connections using token based auth, this field indicated where the token is located with respect to the http request. The supported options are 'header', 'url'                                                                                                                                                                |
| **rest.tokenParam**         | No       | For connections using token based auth and where tokenLocation = 'url', this field indicates what argument name should hold the token value.                                                                                                                                                                                       |
| **rest.scope**              | No       | If this is an OAuth2 connection, this field should hold an array of scopes to apply when authenticating this connection. Possible scope values are service specific and a list of available scopes should be found in the service documentation                                                                                    |
| **rest.scopeDelimiter**     | No       | If this is an OAuth2 connection, this field can be used to override the default delimiter used to separate scopes in the oauth2 requests.                                                                                                                                                                                          |
| **rest.refreshToken**       | No       | This field is set automatically during the oauth2 authentication process. If the service implementing oauth2 supports token refreshes, this field is used to hold said value.                                                                                                                                                      |
| **rest.authURI**            | No       | This field is used for the oauth2 authentication process. This URL identifies the endpoint to be used when making the initial oauth2 http authentication requests.                                                                                                                                                                 |
| **rest.authHeader**         | No       | If the authentication method being used has a custom auth header, this field can be used to override the RFC default                                                                                                                                                                                                               |
| **rest.authScheme**         | No       | Used to override the default authScheme name if a given authentication method does not follow the respective RFC spec.                                                                                                                                                                                                             |
| **rest.disableStrictSSL**   | No       | This optional flag that, if set, skips verifying the SSL certificate (allow self-signed or expired certs) Only set this value if you are having problems reported with the SSL connection, and you accept the risks of ignoring the validity of the SSL certificate.                                                               |
| **rest.retryHeader**        | No       | It is common for rest API's to return 429 errors if too many requests are made in a given amount of time. 429 responses are typically paired with a "retry-after" response header. It is however possible that an API may use a custom header. This field allows a user to override the default.                                   |
| **rest.basicAuth.username** | No       | If basic authentication is used, this is the username to be used when generating the Authentication header value.                                                                                                                                                                                                                  |
| **rest.basicAuth.password** | No       | If basic authentication is used, this is the password to be used when generating the Authentication header value.                                                                                                                                                                                                                  |
| **rest._iClientId**         | No       | A reference to an IClient resource. This can be ignored unless the connection is used int he context of a Connector.                                                                                                                                                                                                               |
| **rest.info**               | No       | This field is used internally to hold profile data returned from OAuth2 responses from the authentication authority.                                                                                                                                                                                                               |
| **rest.pingRelativeURI**    | No       | This is any URI that can be used to test connections. Integrator.io tests connections periodically by making GET requests to this endpoint in order to verify the authentication criteria. If ping requests fail then the connection is turned offline and any flow using the connection will not run, even if scheduled to do so. |
| **rest.pingSuccessPath**    | No       | Some API's send a 200 http status code in the ping response even if the request fails. Instead these services rely on data in the response body to identify success/fail. This field can be used to specific the json path of the field used to identify success.                                                                  |
| **rest.concurrencyLevel**   | No       | If the api represented by this connection has specific concurrency restrictions (simultaneous requests), its possible to specific them using this field. Otherwise a default "burst" mode will be used where multiple http requests will be made at the same time. Burst mode benefits imports most.                               |

##### FTP Connection

| Field                              | Required | Description                                                                                                                                                                                                                           |
|:-----------------------------------|:---------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ftp.hostURI**                    | Yes      | Uniform Resource Identifier (URI) of the FTP server to which you want to connect.                                                                                                                                                     |
| **ftp.username**                   | Yes      | Username of the account on FTP server.                                                                                                                                                                                                |
| **ftp.password**                   | Yes      | Password of the account on FTP server.                                                                                                                                                                                                |
| **ftp.port**                       | No       | Set this to override the default Port number on which the FTP server is running.                                                                                                                                                      |
| **ftp.usePassiveMode**             | No       | Use this to override the default FTP mode.                                                                                                                                                                                            |
| **ftp.entryParserFtpClientConfig** | No       | Only use this field if the FTP connection is failing due to specific implementation details of the FTP server being connected to. The possible values are: ['SYST_UNIX', 'SYST_NT', 'SYST_MVS', 'SYST_VMS', 'SYST_OS2', 'SYST_OS400'] |
| **ftp.userDirectoryIsRoot**        | No       | Default false. Set this to override the default.                                                                                                                                                                                      |

##### SFTP Connection

| Field            | Required | Description                                                                                                                                    |
|:-----------------|:---------|:-----------------------------------------------------------------------------------------------------------------------------------------------|
| **ftp.hostURI**  | Yes      | Uniform Resource Identifier (URI) of the FTP server to which you want to connect.                                                              |
| **ftp.username** | Yes      | Username of the SFTP account.                                                                                                                  |
| **ftp.password** | No       | Password of the SFTP account. Typically either password or authKey are used to validate the client requests. In some rare cases both are used. |
| **ftp.authKey**  | No       | Password of the SFTP account. Typically either password or authKey are used to validate the client requests. In some rare cases both are used. |

##### S3 Connection

| Field                        | Required | Description                                      |
|:-----------------------------|:---------|:-------------------------------------------------|
| **s3.region**                | Yes      | AWS region where the s3 account is being hosted. |
| **s3.accessKeyId**           | Yes      | AWS Access Key ID.                               |
| **s3.secretAccessKey_crypt** | Yes      | AWS secret Access Key.                           |

##### Debug Logging

**what are Debug Logs ?**

During execution of a flow, data useful for debugging is logged and these logs are called Debug Logs.

**What is Logged?**

The options and responses of requests made during the execution of flow are logged.

**How to start Debug Logging:**

In order to start logging 'debugDate' field should be set in the connection.

**How to get logs for a connection ?**

To get the logs "/connections/{_id}/debug/{_typeId}" relative end point should be pinged. Here the placeholder {_id} is the connection id and {_typeId} is the id of export or import for which you are trying to retrieve logs.
