## Encodings

Each encoding represents a version of a video encoded with the settings defined in the profile used. When an encoding is created (either automatically when a video is uploaded, or using the POST method below) it is immediately placed on your encoding queue.

If the original video has been successfully uploaded to your s3 bucket, the video status will be set to `success`.
If the video has failed, the encoding will fail as well.

If the encoding was successful, you can download it or play it using the following convention `$path$$extname$`. Both values can be retrieved using the API.
As well, 7 screenshots will then be generated and uploaded to the S3 bucket with the name using the following convention: `$path$_$screenshot_number$.jpg` e.g. (`my-path/2f8760b7e0d4c7dbe609b5872be9bc3b_1.jpg`).

If the encoding fails, this is usually because there was either an invalid profile being used, or the origin video is corrupted or not recognised. In this case, a logfile will be uploaded to S3 (with the filename `$path$.log`) containing some debug information which can help in correcting issues caused by an invalid profile.

> Example request

```
GET /encoding.json

status: success
```

> Example response

```
status: 200
```
```json
[{
  "id":"2f8760b7e0d4c7dbe609b5872be9bc3b",
  "video_id":"d891d9a45c698d587831466f236c6c6c",
  "extname":".mp4",
  "path":"2f8760b7e0d4c7dbe609b5872be9bc3b",
  "profile_id":"40d9f8711d64aaa74f88462e9274f39a",.
  "profile_name":"h264",
  "status":"success",
  "encoding_progress":99,
  "height":240,
  "width":300,
  "started_encoding_at":"2009/10/13 21:28:45 +0000",
  "encoding_time":9000,
  "files":["2f8760b7e0d4c7dbe609b5872be9bc3b.mp4"],
  "created_at":"2009/10/13 20:58:29 +0000",
  "updated_at":"2009/10/13 21:30:34 +0000"
}, ... ]
```

<p class="red-text">GET /encodings.json</p>

Returns a collection of Encoding objects.

#### Optional parameters

parameters | atributes
------------ | -------------
status | One of `success`, `fail`, `processing`. Filter by status.
profile_id | Filter by profile_id.
profile_name | Filter by profile_name.
video_id | Filter by video_id.

<div style="clear: both;"></div>

> Example request

```
GET /encodings/2f8760b7e0d4c7dbe609b5872be9bc3b.json
```

> Example response

```
status: 200
```
```json
{
  "id":"2f8760b7e0d4c7dbe609b5872be9bc3b",
  "video_id":"d891d9a45c698d587831466f236c6c6c",
  "extname":".mp4",
  "path":"2f8760b7e0d4c7dbe609b5872be9bc3b",
  "profile_id":"40d9f8711d64aaa74f88462e9274f39a",
  "profile_name":"h264",
  "status":"success",
  "encoding_progress":99,
  "height":240,
  "width":300,
  "started_encoding_at":"2009/10/13 21:28:45 +0000",
  "encoding_time":9000,
  "files":["2f8760b7e0d4c7dbe609b5872be9bc3b.mp4"],
  "created_at":"2009/10/13 20:58:29 +0000",
  "updated_at":"2009/10/13 21:30:34 +0000"
}
```

<p class="red-text">GET /encodings/:id.json</p>

Returns a Encoding object.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Encoding object.

<div style="clear: both;"></div>

> Example request

```
POST /encodings.json

video_id: d891d9a45c698d587831466f236c6c6c
profile_name: h264
```

> Example response

```json
{
  "id":"2f8760b7e0d4c7dbe609b5872be9bc3b",
  "video_id":"d891d9a45c698d587831466f236c6c6c",
  "extname":".mp4",
  "path":"2f8760b7e0d4c7dbe609b5872be9bc3b",
  "profile_id":"40d9f8711d64aaa74f88462e9274f39a",
  "profile_name":"h264",
  "status":"processing",
  "encoding_progress":0,
  "height":240,
  "width":300,
  "started_encoding_at":"",
  "encoding_time":0,
  "files":[],
  "created_at":"2009/10/13 20:58:29 +0000",
  "updated_at":"2009/10/13 21:30:34 +0000"
}
```

<p class="red-text">POST /encodings.json</p>

Create a new encoding for a video that already exists.

#### Required parameters

**One** of the `profile_id` or `profile_name` parameter is required:

parameters | atributes
------------ | -------------
video_id | ID of existing video.
profile_id | ID of existing profile.
profile_name | Name of existing profile.

<div style="clear: both;"></div>

> Example request

```
POST /encodings/2f8760b7e0d4c7dbe609b5872be9bc3b/cancel.json
```

> Example response

```
status: 200
```

<p class="red-text">POST /encodings/:id/cancel.json</p>

Cancel an encoding. Returns nothing.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Encoding object.

<div style="clear: both;"></div>

> Example request

```
POST /encodings/2f8760b7e0d4c7dbe609b5872be9bc3b/retry.json
```

> Example response

```
status: 200
```

<p class="red-text">POST /encodings/:id/retry.json</p>

Retry a failed encoding. Returns nothing.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Encoding object.

<div style="clear: both;"></div>

> Example request

```
DELETE /encodings/2f8760b7e0d4c7dbe609b5872be9bc3b.json
```

> Example response

```
status: 200
```

<p class="red-text">DELETE /encodings/:id.json</p>

Deletes requested encoding from panda and your storage. Returns nothing.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Encoding object.

<div style="clear: both;"></div>
