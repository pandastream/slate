## Notifications

> Example request

```
GET /notifications.json`
```

> Example response

```
status: 200
```
```json
{
  "url": "null",
  "events": {
    "video_created": false,
    "video_encoded": false,
    "encoding_progress": false,
    "encoding_completed": false
  }
}
```

<p class="red-text">GET /notifications.json</p>

Returns a Notifiactions object.

<div style="clear: both;"></div>

> Example request

```
PUT /notifications.json

url: "http://example.com/panda_notification"
events['video_encoded'] = true
```

> Example response

```json
{
  "url": "http://example.com/panda_notification",
  "events": {
    "video_created": false,
    "video_encoded": true,
    "encoding_progress": false,
    "encoding_completed": false
  }
}
```

<p class="red-text">PUT /notifications.json</p>

Edits your notifications settings. Return Notifications object.

<div style="clear: both;"></div>
