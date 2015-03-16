## Clouds

> Example request

```
GET /clouds/e122090f4e506ae9ee266c3eb78a8b67.json
```

> Example response

```
status: 200
```
```json
{
  "id": "e122090f4e506ae9ee266c3eb78a8b67",
  "name": "my_first_cloud",
  "s3_videos_bucket": "my-example-bucket",
  "s3_private_access":false,
  "url": "http://my-example-bucket.s3.amazonaws.com/",
  "created_at": "2010/03/18 12:56:04 +0000",
  "updated_at": "2010/03/18 12:59:06 +0000"
}
```

<p class="red-text">GET /clouds/:id.json</p>

Returns a Cloud object.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Cloud object.

<div style="clear: both;"></div>

> Example request

```
PUT /clouds/e122090f4e506ae9ee266c3eb78a8b67.json

name: "my_first_cloud"
s3_videos_bucket: "my_own_bucket"
aws_access_key: XQwEwFR
aws_secret_key: XoSV2fV
priority: 150
```

> Example response

```
status: 200
```
```json
{
  "id": "e122090f4e506ae9ee266c3eb78a8b67",
  "name": "my_first_cloud_updated",
  "s3_videos_bucket": "my-example-bucket",
  "s3_private_access": false
  "created_at":"2010/03/18 12:56:04 +0000",
  "url": "http://my-example-bucket.s3.amazonaws.com/",
  "updated_at":"2010/03/18 12:59:06 +0000"
}
```

<p class="red-text">PUT /clouds/:id.json</p>

Edits cloud settings. Returns Cloud object.

#### Required parameters

parameters | atributes
------------ | -------------
id | ID of the Video object.

#### Optional parameters

parameters | atributes
------------ | -------------
name | Name of the cloud.
s3_videos_bucket | Name of your S3 bucket.
aws_access_key | AWS access key.
aws_secret_key | AWS secret key.
priority | Value between `2` and `200` to prioritize jobs on your account. Cloud with lower number is first in processing queue. Default is `99``.

<div style="clear: both;"></div>
