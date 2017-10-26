State API
========
>**Guidance**: there are lots of use cases for using this API.  Basically, any time you need to persist custom data that relates to your integration then this very generic key value storage API can be used.

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-------------------|:-------|:------------:|:------------------------------|
|/state|GET|200|Get all global keys.|
|/state/{key}|GET|200|Get the value associated with the key.|
|/state/{key}|PUT|200|Create/update the value associated with the key.|
|/state/{key}|DELETE|204|Delete the key and value.|
|/state|DELETE|204|Delete all global keys and values.  Be careful with this API!|
|/{resource_type}/{_id}/state|GET|200|Get all keys stored underneath a specific resource in your account.|
|/{resource_type}/{_id}/state/{key}|GET|200|Get the resource specific value associated with the key.|
|/{resource_type}/{_id}/state/{key}|PUT|200|Create/update the resource specific value associated with the key.|
|/{resource_type}/{_id}/state/{key}|DELETE|200|Delete the resource specific key and value.|
|/{resource_type}/{_id}/state|DELETE|200|Delete all keys and values stored underneath the resource.  Be careful with this API!|

## Examples

#### 1.  Get all keys.

```
GET /v1/state HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "keys": [
        "apple",
        "banana"
    ]
}
```

#### 2.  Get the value for the key "banana".

```
GET /v1/state/banana HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "message": "Don't slip!"
}
```

#### 3.  Update the value for the key "banana".

```
GET /v1/state/banana HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{"message": "Don't slip!", "reply": "i didn't!"}
```

Sample Response:

```
OK
```

#### 4.  Get the value for the key "banana" (again).

```
GET /v1/state/banana HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "message": "Don't slip!",
    "reply": "i didn't!"
}
```

#### 5.  Create a value to track a sequence number for an EDI data import (that would be updated every time the import is run).  Note that this state value is stored underneath the related import resource.

```
PUT /v1/imports/55cca9d6f7dc37597700005d/state/sequenceNumber HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token

{"counter": 1}
```

Sample Response:

```
Created
```

#### 6.  Get the value just created above.

```
GET /v1/imports/55cca9d6f7dc37597700005d/state/sequenceNumber HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "counter": 1
}
```

#### 7.  Get all keys (again).
Note that the key stored underneath the import (that we just created above) was not returned.  Only global keys are returned by the top level GET /state API.  To return all keys underneath a specific resource you would need to use the more specific state API for that resource.  i.e. GET /imports/55cca9d6f7dc37597700005d/state

```
GET /v1/state HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "keys": [
        "apple",
        "banana"
    ]
}
```
```
GET /v1/imports/55cca9d6f7dc37597700005d/state HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
{
    "keys": [
        "sequenceNumber"
    ]
}
```
