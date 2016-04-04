Retry
=====

##### What is retry?

This is the feature provided in the integrator to retry any flow, export or import in the event of failure. This will create new job for the retry execution but the results of the retry will be associated with the parent job.

##### Retry Related HTTP Endpoints

| Relative URI             | Method | Success Code | Description                 |
|:-------------------------|:-------|:------------:|:----------------------------|
| /retries                 | GET    |     200      | List all retry jobs.        |
| /retries                 | POST   |     201      | Create new retry.           |
| /retries/{_id}/data      | GET    |     200      | Get the data to be retried. |
| /retries/{_id}/signedURL | GET    |     200      |                             |
| /retries/{_id}/data      | PUT    |     200      |                             |

#### Relevant Schema Info

##### Flow

| Field         | Required | Description                                              |
|:--------------|:---------|:---------------------------------------------------------|
| **_id**       | Yes      | System generated unique identifier for this flow.        |
| **type**      | Yes      | Retry type. It can be of type page, object, path or file |
| **_jobId**    | Yes      | Job identifier to be retried                             |
| **createdAt** | Yes      | Date field having the info on job created.               |
| **data.id**   | .        | .                                                        |
| **data.host** | .        | .                                                        |
