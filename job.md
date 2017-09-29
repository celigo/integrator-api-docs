Job API
===

##### What is a Job?

Job represents execution state of a flow in integrator. When a flow is run, a job is created and it has a unique identifier associated with it. Job contains status property that that holds the current state of the job. These states are: queued, running, completed, failed, cancelling and cancelled. For example, when a flow is run, new job is created with the status 'queued'. When the worker thread picks up that particular job and start executing, job status is updated as 'running' till it completes its execution. Once the job is completed, its status could be 'completed' or 'failed' Along with the status, job contains information about number of success and number of errors. A job can go to 'completed' state even though there are errors as some of the records in the flow might have failed. In that event, failed records can be retried. Jobs can be referred using the unique Id associated with them.

On retry of the job, new job is created but the results of retried job will be still associated with the original parent job. At any given time, only one job will be in running state for a flow.

##### Job Related HTTP Endpoints

| Relative URI                         | Method | Success Code | Description                      |
|:-------------------------------------|:-------|:------------:|:---------------------------------|
| /jobs                                | GET    |     200      | List all jobs.                   |
| /jobs                                | POST   |     201      | Create new job.                  |
| /jobs/{_id}                          | PUT    |     200      | Update existing job.             |
| /jobs/{_id}                          | GET    |     200      | Retrieve existing job.           |
| /jobs/{_id}                          | DELETE |     204      | Delete existing job.             |
| /jobs/{_id}/retry                    | POST   |     200      | Retry the failed job.            |
| /jobs/{_id}/retries/{_retryId}/retry | POST   |     200      | Retrying the failed retry.       |
| /jobs/{_id}/retries/retry            | PUT    |     200      | Retrying all the failed retries. |

#### Relevant Schema Info

##### Job

| Field              | Required | Description                                                                                                     |
|:-------------------|:---------|:----------------------------------------------------------------------------------------------------------------|
| **_id**            | Yes      | System generated unique identifier for this job.                                                                |
| **type**           | Yes      | Type of the execution job is associated with (export, import, flow, retry).                                     |
| **_integrationId** | No       | If this flow is part of an integration, this value will hold the id of that integration.                        |
| **_exportId**      | Yes      | Unique identifier of the export created from where the data is exported                                         |
| **_importId**      | Yes      | Unique identifier of the import created where the data is going to be imported                                  |
| **_flowId**        | Yes      | If the job is associated with a flow, this contains the id of the flow                                          |
| **_retryOfJobId**  | .        | .                                                                                                               |
| **startedAt**      | No       | Starting time of the job                                                                                        |
| **endedAt**        | No       | Ending time of the job                                                                                          |
| **status**         | No       | This states the current status of the job ('queued', 'running', 'completed', 'failed', 'canceled', 'canceling') |
| **numError**       | No       | It contains number of errors if any after the job execution is done                                             |
| **numSuccess**     | No       | It contains the number of success once the job execution is done                                                |
| **numIgnore**      | No       | Number of records that are ignored.                                                                             |
| **retriable**      | No       | Boolean value.                                                                                                  |
| **createdAt**      | Yes      | Present date and the expiry date will set 30 days from now.                                                     |
| **queueTime**      | No       | Time for which the job was in queue.                                                                            |
| **files.id**       | .        | .                                                                                                               |
| **files.host**     | .        | .                                                                                                               |
| **files.name**     | .        | .                                                                                                               |
| **logs.id**        | .        | .                                                                                                               |
| **files.host**     | .        | .                                                                                                               |
