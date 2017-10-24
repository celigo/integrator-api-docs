Template API
===========

### Endpoints
| Relative URI| Method | Success Code | Description|
|:-----------|:-------|:------------:|:-------------|
|/templates|GET|200|Get all templates.|
|/templates/{_id}|GET|200|Get a specific template.|
|/templates|POST|201|Create a new template.|
|/templates/{_id}|PUT|200|Update a specific template.|
|/templates/{_id}|DELETE|204|Delete a specific template.|


## Examples

#### 1.  Get all templates in my account.

```
GET /v1/template HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
```

Sample Response:

```
[
    {
        "_id": "58f109e8927af74d60801fff",
        "name": "Send GitHub Notifications to Slack",
        "description": "This is a very simple integration that listens for commit events in GitHub, and then posts messages to Slack so that people can use Slack to monitor developer activity.  The benefit of using this integration vs the free integration provided by Slack is that you can customize how the messages post to Slack.  For example, you might want messages to post as if they were sent by the user that triggered the event in GitHub (vs a generic bot type user).",
        "imageURL": "https://secure.gravatar.com/avatar/33895cc016e520a2a07131c3753d8d4f?d=mm&s=55",
        "websiteURL": "http://www.celigo.com/",
        "contactEmail": "template@celigo.com",
        "published": true,
        "lastModified": "2017-04-24T16:54:25.186Z",
        "applications": [
            "github",
            "slack"
        ]
    },
    {
        "_id": "58f10e49927af74d608022ac",
        "name": "Send Travis Build Notifications to Slack",
        "description": "This integration listens for build notifications in Travis CI and posts messages to Slack. Very similar to the native integration that Slack has in their integration marketplace, except now you can completely customize how the messages appear in Slack. For example, you might want to minimize the message content that gets sent to Slack to only show the repository, user, and build status.",
        "imageURL": "https://secure.gravatar.com/avatar/33895cc016e520a2a07131c3753d8d4f?d=mm&s=55",
        "websiteURL": "http://www.celigo.com/",
        "contactEmail": "template@celigo.com",
        "published": true,
        "lastModified": "2017-04-24T16:57:55.663Z",
        "applications": [
            "slack",
            "travis"
        ]
    },
    {
        "_id": "58f12659c468a47a9489242c",
        "name": "Send Zendesk Tickets to Slack",
        "description": "This integration routes Zendesk tickets into Slack.  This integration is used by Celigo's engineering and QA department to keep an eye on all support tickets being logged throughout the day (i.e. without having to login to Zendesk).",
        "imageURL": "https://secure.gravatar.com/avatar/33895cc016e520a2a07131c3753d8d4f?d=mm&s=55",
        "websiteURL": "http://www.celigo.com/",
        "contactEmail": "template@celigo.com",
        "published": true,
        "lastModified": "2017-04-24T16:58:00.199Z",
        "applications": [
            "slack",
            "zendesk"
        ]
    }
]
```
#### 2.  Update a specific template in my account.

```
PUT /v1/template/58f12659c468a47a9489242c HTTP/1.1
Host: api.integrator.io
Authorization: Bearer my_api_token
{
    "name": "Give your template a really great name!",
    "description": "A good description helps a lot too.",
    "imageURL": "https://secure.gravatar.com/avatar/33895cc016e520a2a07131c3753d8d4f?d=mm&s=55",
    "websiteURL": "http://www.celigo.com/",
    "contactEmail": "template@celigo.com",
    "published": true,
    "applications": [
      "slack",
      "zendesk"
    ]
}
```

Sample Response:

```
{
    "_id": "58f12659c468a47a9489242c",
    "name": "Give your template a really great name!",
    "description": "A good description helps a lot too.",
    "imageURL": "https://secure.gravatar.com/avatar/33895cc016e520a2a07131c3753d8d4f?d=mm&s=55",
    "websiteURL": "http://www.celigo.com/",
    "contactEmail": "template@celigo.com",
    "published": true,
    "lastModified": "2017-10-24T15:03:45.140Z",
    "applications": [
        "slack",
        "zendesk"
    ]
}
```
