Job Error
=========

##### What is a jobError?

Any error that have caused the executing job to fail. There can be scenarios where job can be completed but it may have errors as some records involved in the job might have failed.

##### Job Related HTTP Endpoints

| Relative URI                                | Method | Success Code | Description                                                 |
|:--------------------------------------------|:-------|:------------:|:------------------------------------------------------------|
| /jobs/{_id}/joberrors/resolve               | PUT    |     200      | Create the entry with resolved job errors.                  |
| /jobs/{_id}/joberrors/{_jobErrorId}/resolve | PUT    |     201      | Create the entry of particualr jab errors that are resolved |

#### Relevant Schema Info

##### jobErrors

| Field         | Required | Description                                                     |
|:--------------|:---------|:----------------------------------------------------------------|
| **_userId**   | Yes      | Unique identifier of the user creating the iClient.             |
| **_jobId**    | Yes      | Job identifier to be retried                                    |
| **_retryId**  | No       | Read only field tracking last modified date/time.               |
| **createdAt** | Yes      | Present date and the expiry date will set 30 days from now.     |
| **resolved**  | No       | Boolean value. If set, it means respective jobError is resolved |
| **code**      | .        | .                                                               |
| **extract**   | .        | .                                                               |
| **generate**  | .        | .                                                               |
| **extracted** | .        | .                                                               |
| **generated** | .        | .                                                               |
| **message**   | .        | .                                                               |
| **source**    | .        | .                                                               |
