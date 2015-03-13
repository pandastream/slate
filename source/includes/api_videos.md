## Videos

Video is a restfull resource representing the original media.
It describes all major metadata of a media file, it's size and it's original filename.
It contains a status attribute representing the uploading process of media to S3.
`status` is set to success when the media has been successfully uploaded to S3.
Careful: It does not mean that it's encodings have been encoded.

> Example request

```
GET /videos.json
```

> Example response

```
status: 200
```
```json
[{
  "id":"d891d9a45c698d587831466f236c6c6c",
  "original_filename":"test.mp4",
  "extname":".mp4",
  "path":"d891d9a45c698d587831466f236c6c6c",
  "video_codec":"h264",
  "audio_codec":"aac",
  "height":240,
  "width":300,
  "fps":29,
  "duration":14000,
  "file_size": 39458349,
  "created_at":"2009/10/13 19:11:26 +0100",
  "updated_at":"2009/10/13 19:11:26 +0100"
}, ... ]
```

<p class="red-text">GET /videos.json</p>

Returns a collection of Video objects.

#### Optional parameters

parameters | atributes
------------ | -------------
status | One of `success`, `fail`, `processing`. Filter by status.
page | Default: 1.
per_page | Default: 100.

<div style="clear: both;"></div>

> Example request

```
GET /videos/d891d9a45c698d587831466f236c6c6c.json
```

> Example response

```
status: 200
```
```json
{
  "id":"d891d9a45c698d587831466f236c6c6c",
  "original_filename":"test.mp4",
  "extname":".mp4",
  "path":"d891d9a45c698d587831466f236c6c6c",
  "video_codec":"h264",
  "audio_codec":"aac",
  "height":240,
  "width":300,
  "fps":29,
  "duration":14000,
  "file_size":39458349,
  "created_at":"2009/10/13 19:11:26 +0100",
  "updated_at":"2009/10/13 19:11:26 +0100"
}
```

<p class="red-text">GET /videos/:id.json</p>

Returns a Video object.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Video object.

<div style="clear: both;"></div>

> Example request

```
GET /videos/d891d9a45c698d587831466f236c6c6c/encodings.json
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
  "profile_id":"40d9f8711d64aaa74f88462e9274f39a",
  "profile_name":"h264",
  "status":"success",
  "encoding_progress":99,
  "height":240,
  "width":300,
  "file_size":29458349,
  "started_encoding_at":"2009/10/13 21:28:45 +0000",
  "encoding_time":9000,
  "files":["2f8760b7e0d4c7dbe609b5872be9bc3b.mp4"],
  "created_at":"2009/10/13 20:58:29 +0000",
  "updated_at":"2009/10/13 21:30:34 +0000"
}, ... ]
```

<p class="red-text">GET /videos/:id/encodings.json</p>

Returns a collection of Encoding objects connected to specified Video object.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Video object.

#### Optional Parameters

parameters | atributes
------------ | -------------
status | One of `success`, `fail`, `processing`. Filter by status.
profile_id | Filter by profile_id.
profile_name | Filter by profile_name.

<div style="clear: both;"></div>

> Example request

```
GET /videos/d891d9a45c698d587831466f236c6c6c/metadata.json
```

> Example response

```
status: 200
```
```json
{
  "image_height":208,
  "audio_format":"mp4a",
  "selection_time":"0 s",
  "track_layer":0,
  "poster_time":"0 s",
  "video_frame_rate":30.0,
  "duration":"19.35 s",
  "media_create_date":"Tue Jan 28 20:59:44 +0000 1913",
  "audio_sample_rate":48000,
  "compressor_id":"avc1",
  "graphics_mode":"srcCopy",
  "audio_channels":2,
  "media_header_version":0,
  "track_modify_date":"Tue Jan 28 20:59:39 +0000 1913",
  "preferred_volume":"100.00%",
  "mime_type":"video/mp4",
  "file_size":"1435 kB",
  "create_date":"Tue Jan 28 20:59:39 +0000 1913",
  "rotation":0
  ...
}
```

<p class="red-text">GET /videos/:id/metadata.json</p>

Just after the media is uploaded, we analyze and store the metadata that we can extract from it.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Video object.

<div style="clear: both;"></div>

> Example request

```
POST /videos.json

profiles: "h264,webm"
path_format: "my-path/:id"
payload: "2456"
```

> Example response

```
status: 200
```
```json
{
  "id":"d891d9a45c698d587831466f236c6c6c",
  "original_filename":"test.mp4",
  "extname":".mp4",
  "path":"my-path/d891d9a45c698d587831466f236c6c6c",
  "video_codec":"h264",
  "audio_codec":"aac",
  "height":240,
  "width":300,
  "fps":29,
  "duration":14000,
  "file_size": 29458349,
  "created_at":"2009/10/13 19:11:26 +0100",
  "updated_at":"2009/10/13 19:11:26 +0100"
}
```

<p class="red-text">POST /videos.json</p>

If you use Panda in your web application, you'll probably want to use our [Panda Uploader](#uploader) to upload video files from a web page. Otherwise, you can use this API to upload videos.

<aside class="warning">
  When creating the [authorisation signature](#api-authentication) for this request **do not** include the file parameter.
</aside>

#### Required Parameters

**One** of the following parameters is required:

parameters | atributes
------------ | -------------
file | The file that is being uploaded.
source_url | Url of a remote video file.

<aside class="notice">
  If you use the `file` parameter, make sure that your HTTP request uses an appropriate `Content-Type`, such as `multipart/form-data`.
</aside>

#### Optional Parameters

parameters | atributes
------------ | -------------
profiles | Comma-separated list of profile names or IDs to be used during encoding. Alternatively, specify `none` so no encodings are created yet.
path_format | Represents the complete video path without the extension name. It can be constructed using some provided keywords.
payload | Arbitrary string stored along the Video object.

> Example path format

```
"my-path/:video_id/:profile/:id"
```

#### Path Format

The following keywords can be combined to create a path format.

parameters | atributes
------------ | -------------
id         | Id of the encoding. Required.
video_id   | Id of the video.
original   | Original filename of the uploaded video
date       | Date the video was uploaded (`2009-10-09`).
profile    | Profile name used by the encoding (`original` is used when the file is the original video).
type       | Video type, `original` or `encodings`.
resolution | Resolution of the video or the encoding (`480x340`).

By default `path_format` is set to `:id`.

<aside class="warning">
  Path format should be unique for each encoding, so `:id` is required.
</aside>

#### Payload

The payload is an arbitrary text of length 256 or shorter that you can store along the Video. It is typically used to retain an association with one of your own DB record ID.

<div style="clear: both;"></div>

> Example request

```
POST /videos/upload.json

file_size: 805301
file_name: panda.mp4
```

> Example response

```
status: 201
```
```json
{
  "id":"abcdefgh",
  "location":"http://vm-37gup6.upload.pandastream.com/upload/session?id=abcdefgh"
}
```

<p class="red-text">POST /videos/upload.json</p>

Before uploading a file to panda, you will need to create a session for each particular files. In return you get a unique location for this file.

Our upload API supports 2 ways of uploading a file: resumable (also known as streaming) and multipart upload.

#### Required parameters

parameters | atributes
------------ | -------------
file_size | Size in bytes of the video.
file_name | File name of the video.

#### Optional parameters

parameters | atributes
------------ | -------------
use_all_profiles | Default is `false`.
profiles | Comma-separated list of profile names or IDs to be used during encoding. Alternatively, specify `none` so no encodings are created yet.
path_format | Represents the complete video path without the extension name. It can be constructed using some provided keywords.
payload | Arbitrary string stored along the Video object.

<div style="clear: both;"></div>

> Example request

```
DELETE /videos/d891d9a45c698d587831466f236c6c6c.json
```

> Example response

```
status: 200
```

<p class="red-text">DELETE /videos/:id.json</p>

Deletes requested video from panda and your storage. Returns nothing.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Video object.

<div style="clear: both;"></div>

> Example request

```
DELETE /videos/2f8760b7e0d4c7dbe609b5872be9bc3b/source.json
```

> Example response

```
status: 200
```

<p class="red-text">DELETE /videos/:id/source.json</p>

Deletes only source file from storage.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Video object.

<div style="clear: both;"></div>
