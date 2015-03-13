# API

This document outlines the __V2__ of the HTTP interface that Panda exposes to other applications.

### Profiles

<p class="red-text">GET /profiles.json</p>

##### Example request

`GET /profiles.json`

##### Example response

```json
[{
   "id":"40d9f8711d64aaa74f88462e9274f39a",
   "title":"MP4 (H.264)",
   "name": "h264",
   "extname":".mp4",
   "width":320,
   "height":240,
   "audio_bitrate": 128,
   "video_bitrate": 500,
   "aspect_mode": "letterbox",
   "command":"ffmpeg -i $input_file$ -c:a libfaac $audio_bitrate$ -c:v libx264 $video_bitrate$ -preset medium $filters$ -y $output_file$",
   "created_at":"2009/10/14 18:36:30 +0000",
   "updated_at":"2009/10/14 19:38:42 +0000"
}, ... ]
```
<div style="clear: both;"></div>
<p class="red-text">GET /profiles/:id.json</p>

##### Example request

`GET /profiles/40d9f8711d64aaa74f88462e9274f39a.json`

##### Example response

```json
{
  "id":"40d9f8711d64aaa74f88462e9274f39a",
  "title":"MP4 (H.264)",
  "name": "h264",
  "extname":".mp4",
  "width":320,
  "height":240,
  "audio_bitrate": 128,
  "video_bitrate": 500,
  "aspect_mode": "letterbox",
  "command":"ffmpeg -i $input_file$ -c:a libfaac $audio_bitrate$ -c:v libx264 $video_bitrate$ -preset medium $filters$ -y $output_file$",
  "created_at":"2009/10/14 18:36:30 +0000",
  "updated_at":"2009/10/14 19:38:42 +0000"
}
```
<div style="clear: both;"></div>
<p class="red-text">POST /profiles.json</p>

##### Optional parameters

parameters | atributes
------------ | -------------
name:                 |Unique Machine-readable name that will identify the profile.</br>Helpful later on for filtering encodings by profile
title:                |Human-readable name
extname:              |(example: '.mp4') File extension
width:                |(example: 1080) Width in pixels
height:               |(example: 720) Height in pixels
upscale:              |(default: true) Upscale the video resolution to match your profile
aspect_mode:          |(default: letterbox)
two_pass:             |(default: false)
video_bitrate:        |(example: 3000)
fps:                  |(example: 29.97, default: not set) Null value copy the original fps
keyframe_interval:    |(default: 250) Adds a key frame every 250 frames
keyframe_rate:        |(example: 0.5) Adds a key frame every 2 seconds
audio_bitrate:        |(example: 128)
audio_sample_rate:    |(default: 44100)
audio_channels:       |(default: not set)
clip_length:          |(example: '00:20:00') Sets the clip's duration
clip_offset:          |(example: '00:00:10') Clip starts at a specific offset
watermark_url:        |Url of the watermark image
watermark_top,</br>watermark_bottom,</br>watermark_left,</br>watermark_right:      |(default: 0) Distance from the top/bottom/right/left of the video frame</br>Works like CSS
watermark_width,</br>watermark_height:     |(default: no resizing) Size of the watermark image
frame_count:          |(default: 7) Evenly spaced number of generated screenshots

</br>
**Aspect Modes:**

parameters | atributes
------------ | -------------
preserve:             |Original size and ratio is preserved.
constrain:            |Aspect ratio is maintained.</br>No black bars is added to your output.
letterbox:            |Aspect ratio is maintained.</br>Adds black bars to your output to match your profile frame height (above and below only).
pad:                  |Aspect ratio is maintained.</br>Adds black bars to your output to match your profile frame size.
crop:                 |Aspect ratio is maintained.</br>fills your profile frame size and crops the rest.


</br>
**H264 preset:**

parameters | atributes
------------ | -------------
h264_crf:             |(example: 23)
h264_profile:         |(example: 'baseline')
h264_level:           |(example: '3.1')


</br>
**JPEG preset:**

Only one of the following attributes can be set.

parameters | atributes
------------ | -------------
frame_count:          |(example: 7) Evenly spaced number of thumbnails
frame_offsets:        |(example: '2s, 10s, 250f, 400f') Array of offset (Frames or seconds),
frame_interval:       |(example: '1000f') Thumbnail interval (Frames or seconds)


</br>
**HLS.Variant and HLS.Variant.Audio presets:**

parameters | atributes
------------ | -------------
encryption:           |(example: true)
encryption_key_url:   |(example: http://example.com/my.key)
encryption_key:       |(example: MPE2KWuqHKjk0XNR3Ehluw==) Base64 encoded AES-128 key
encryption_iv:        |(example: 13ErUsaoGZ4N53pO6RrXsQ==) Base64 encoded AES-128 iv


###### Using presets

##### Required parameters

`preset_name`

##### Example request

```
POST /profiles.json
preset_name: 'h264'
```
<div style="clear: both;"></div>
##### Example response

```json
{
  "id":"40d9f8711d64aaa74f88462e9274f39a",
  "title": "H264 (MP4)",
  "name": "h264",
  "extname":".mp4",
  "width":480,
  "height":320,
  "audio_bitrate": 128,
  "video_bitrate": 500,
  "preset_name": "h264",
  "created_at":"2009/10/14 18:36:30 +0000",
  "updated_at":"2009/10/14 19:38:42 +0000"
}
```
<div style="clear: both;"></div>
###### Using custom settings

##### Required parameters

parameters | atributes
------------ | -------------
command: |line break separated list of encoding commands to run.
extname: |file extension (example: '.mp4')

##### Example request
```
POST /profiles.json

title: H264 normal quality
name: h264
extname: .mp4
width: 320
height: 240
audio_bitrate: 128
video_bitrate: 500
aspect_mode: pad
command: ffmpeg -i $input_file$ -c:a libfaac $audio_bitrate$ -c:v libx264 $video_bitrate$ -preset medium $filters$ -y $output_file$
```
<div style="clear: both;"></div>
##### Example response

```json
{
  "id":"40d9f8711d64aaa74f88462e9274f39a",
  "title":"H264 normal quality",
  "extname":".mp4",
  "width":320,
  "height":240,
  "audio_bitrate":128,
  "video_bitrate":500,
  "aspect_mode" "pad",
  "command":"ffmpeg -i $input_file$ -c:a libfaac $audio_bitrate$ -c:v libx264 $video_bitrate$ -preset medium $filters$ -y $output_file$",
  "created_at":"2009/10/14 18:36:30 +0000",
  "updated_at":"2009/10/14 19:38:42 +0000"
}
```
<div style="clear: both;"></div>
<p class="red-text">PUT /profiles/:id.json</p>

##### Optional parameters

Same as POST /profiles.json but `preset_name`

##### Example request
```
    PUT /profiles/40d9f8711d64aaa74f88462e9274f39a.json

    title: The best custom profile
```
<div style="clear: both;"></div>
##### Example response

```json
{
  "id":"40d9f8711d64aaa74f88462e9274f39a",
  "title":"The best custom profile",
  "extname":".mp4",
  "width":320,
  "height":240,
  "audio_bitrate":128,
  "video_bitrate":500,
  "command":"ffmpeg -i $input_file$ -c:a libfaac $audio_bitrate$ -c:v libx264 $video_bitrate$ -preset medium $filters$ -y $output_file$",
  "created_at":"2009/10/14 18:36:30 +0000",
  "updated_at":"2009/10/14 19:38:42 +0000"
}
```
<div style="clear: both;"></div>
<p class="red-text">DELETE /profiles/:id.json</p>

##### Example request

    `DELETE /profiles/40d9f8711d64aaa74f88462e9274f39a.json`

##### Example response

    `status: 200`

### Clouds

<p class="red-text">GET /clouds/:id.json</p>

##### Example request

    `GET /clouds/e122090f4e506ae9ee266c3eb78a8b67.json`

##### Example response

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
<div style="clear: both;"></div>
<p class="red-text">PUT /clouds/:id.json</p>

##### Optional parameters

`name`</br>
`s3_videos_bucket`</br>
`aws_access_key`</br>
`aws_secret_key`</br>
`priority (value between 2 and 200, default is 99)`

##### Example request

```
PUT /clouds/e122090f4e506ae9ee266c3eb78a8b67.json

name: "my_first_cloud"
s3_videos_bucket: "my_own_bucket"
aws_access_key: XQwEwFR
aws_secret_key: XoSV2fV
priority: 150
```
<div style="clear: both;"></div>
##### Example response

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
<div style="clear: both;"></div>
### Notifications

<p class="red-text">GET /notifications.json</p>

##### Example request

    `GET /notifications.json`

##### Example response

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
<div style="clear: both;"></div>

<p class="red-text">PUT /notifications.json</p>

##### Example request

```
PUT /notifications.json

url: "http://example.com/panda_notification"
events['video_encoded'] = true
```
<div style="clear: both;"></div>

##### Example response

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
<div style="clear: both;"></div>

### Error classes and Error messages

Encoding and Video have 2 special attributes when their status is set to 'fail'

`error_class`: Helps you figure out what happened and what to do next.

`error_message`: Gives you a more descriptive message of the error

The following table list all possible error classes:

- `S3Error`

    Whenever the system try to access your bucket and gets an bad response from S3.

- `VideoStatusInvalid`

    The original video is in error state and can not be processed. Check the videos error to know the reason (only applied for encodings).

- `CommandInvalid`

    The command of your custom profile is not correct. You should take a look at your encoding logfile.

- `EncodingError`

    The encoding has failed. You should take a look at your encoding logfile.

- `HLSVariantsNotFound`

    No HLS variants were found when creating HLS variants playlist.

- `FileNotFound`

    Your original video file doesn't exist on S3.

- `DownloadFailed`

    Panda was not able to download the file from source_url.

- `UnexpectedError`

    Error is not expected from the system we will look at it.

- `FormatNotRecognised`

    Your file is not a video or audio file valid.



### API Errors

When there is an issue with a request, Panda will return the appropriate HTTP status code, along with a JSON object containing the `error` name and a `message`. (500 status returns an empty body). For more information on HTTP status codes, see `https://en.wikipedia.org/wiki/List_of_HTTP_status_codes`

#### 500 (InternalError)

When there is an internal error a 500 status will be returned along with additional information in the message. Whenever a 500 error occurs our technical team is notified of the issue and will investigate the root of the problem immediately. If your experience a recurring issue, please submit a support ticket to [support.pandastream.com](https://support.pandastream.com)

#### 401 NotAuthorized

When the `signature` parameter of a request is not correct, the server returns a status code 401 and a descriptive message. If you receive this error please ensure that you are constructing and encoding the `signature` parameter as described in the API Authentication section below.

If you receive a **Signatures expired** message, that means your clock is out of sync. You can maintain the system time in synchronization with time servers using `ntpd`.

##### Example response

    {"error" : "NotAuthorized", "message" : "Signatures do not match"}

    {"error" : "NotAuthorized", "message" : "Signatures expired"}

#### 404 RecordNotFound

A record with the `id` supplied could not be found.

##### Example response (example)

    {"error" : "RecordNotFound", "message" : "Couldn't find Video with ID=X"}

#### 400 BadRequest

This error will be returned in two cases. Either because you have requested a response format that is not supported (currently only JSON is supported, so all urls must end in `.json`), or you have not submitted all of the required parameters to a method.

##### Example response (2 examples)

    {"error" : "BadRequest", "message" : "Currently only .json is supported as a format"}

    {"error" : "BadRequest", "message" : "All required parameters were not supplied: access_key, signature, timestamp"}

#### 413 FileSizeLimitExceeded

If you attempt to upload a file > 10MB with a sandbox account, the following error will be returned.

##### Example response

    {"error" : "BadRequest", "message" : "File size limit for this account is set to 10485760 bytes"}

### API Authentication

For examples of authentication implementation please refer to the [Ruby gem](https://github.com/pandastream/panda_gem) and example [PHP client](https://github.com/pandastream/panda_client_php).

The Panda API requires all requests must also be signed to ensure they are valid and authenticated. For GET and DELETE requests the additional parameters must be url encoded and added to the parameters in the url. When making a POST or PUT request they should be included in the usual parameters payload submitted.

The `access_key` and `secret_key` used to authenticate the request are provided when you sign up for your Panda account. Your keys can always be found by logging in to your account by visiting [pandastream.com](https://www.pandastream.com)

A correctly signed request contains the following additional parameters:

    access_key: Provided when you sign up for Panda
    cloud_id:   Provided when you sign up for Panda
    timestamp:  Current UTC time in iso8601 format
    signature:  HMAC signature generated as described below

#### Build the signature

The `signature` is generated using the following method:

1. Create a `canonical_querystring` by url encoding all of the parameters and the values, and joining them into one stringusing the `=` character to separate keys and their values, and the `&` character to separate the key value pairs.

    All parameters and values have to be joined in the alphabetical order.

    Make sure it encodes the given string according to » RFC 3986. (spaces are escaped using `%20` and not a `+`)

    A typical `canonical_querystring` might look as follows: `access_key=85f8dbe6-b998-11de-82e1-001ec2b5c0e1&cloud_id=bd54547d152e91104d82c0a81e7d5ff2&timestamp=2009-10-15T15%3A38%3A42%2B01%3A00` ... other parameters such as those in the POST request would also be added to this string.

2. Construct the `string_to_sign` by concatenating the uppercase HTTP request method (GET, POST, PUT or DELETE), hostname (api.pandastream.com or api-eu.pandastream.com), request uri (e.g. /videos.json) and `canonical_querystring` with newlines (\\n).

    The api version `/v2` should not be part of the request uri

3. To generate the `signature`, first **HMAC SHA256** encode the complete `string_to_sign` using your `secret_key` as the key. For example, using Ruby: `hmac = HMAC::SHA256.new(secret_key)` then `hmac.update(string_to_sign)`

4. Then take the **binary digest** of the hmac output and **base 64** encode it. Make sure to remove any newline characters at the end. In Ruby you would do `Base64.encode64(hmac.digest).chomp`

5. The signature could be rejected by the server for the following reasons:
    the request is a POST request and the signature has already been used
    the request is POST /videos.json and the timestamp is expired for 30 minutes
    the request is not POST and the timestamp is expired for 5 minutes

#### Worked example

Assume that we wish to get all videos we have uploaded to our cloud (US account)

`cloud_id = '123456789'`</br>
`access_key = 'abcdefgh'`</br>
`secret_key = 'ijklmnop'`
  
We first construct the request uri

    api.pandastream.com/v2/videos.json

We send the following query parameters

`access_key  abcdefgh`</br>
`cloud_id    123456789`</br>
`timestamp   2011-03-01T15:39:10.260762Z`

The signature is generated by signing the following string

```
GET\napi.pandastream.com\n/videos.json\naccess_key=abcdefgh&cloud_id=123456789&timestamp=2011-03-01T15%3A39%3A10.260762Z
```
<div style="clear: both;"></div>
This should be signed by generating the HMAC SHA256 hex digest with secret key `ijklmnop`

    `kVnZs%2FNX13ldKPdhFYoVnoclr8075DwiZF0TGgIbMsc%3D`


The api request then becomes

```
GET /videos.json
QUERY
access_key=abcdefgh&cloud_id=123456789&timestamp=2011-03-01T15%3A39%3A10.260762Z&signature=kVnZs%2FNX13ldKPdhFYoVnoclr8075DwiZF0TGgIbMsc%3D
```
<div style="clear: both;"></div>
Here is the request and response made with curl

```
$ curl http://api.pandastream.com/v2/videos.json?access_key=abcdefgh&cloud_id=123456789&timestamp=2011-03-01T15:39:10.260762Z&signature=kVnZs%2FNX13ldKPdhFYoVnoclr8075DwiZF0TGgIbMsc%3D
status: "200"
body: "[]"
```
<div style="clear: both;"></div>
#### Things to look into if you're having signature issues

* Ensure that the timestamp is uppercase and strict iso8601 format.
* Check the url in the `string_to_sign` does **not** include `/v2`, even though `/v2` is required as part of the actual url you will request when accessing the api.
* Only url-encode the parameter values of the `canonical_querystring` and not the whole `string_to_sign`.
* The signature should end in an `=` sign and not contain any more characters afterwards. For example, if your HMAC library is adding the characters `3D` after the `=` you must remove these.
* Make sure you use the **binary digest** of the HMAC and not any other outputs such as the hex digest.
* Make sure that URL encoded characters are uppercase (%3A and not %3a).
* Make sure inside the string_to_sign you use `GET` for GET request, `POST` for POST request, etc...

### HLS Encryption

To use the encryption, Panda will require a 128 bit key and a 128 bit initialization vector. If you set up your HLS profile without them, then we will generate them for you during the first encoding.
If you are willing to use your own key or initialization vector (iv), you can create them yourself.

For example, using Ruby :

```ruby
    cipher = OpenSSL::Cipher::AES.new(128, :CBC)
    cipher.encrypt
    key = cipher.random_key
    iv = cipher.random_iv
```
<div style="clear: both;"></div>
In order to use your generated key or initialization vector in the profile settings, you will have to first **base 64** encode them. Make sure to remove any newline characters at the end.
Example, using Ruby :

```ruby
    encoded_key = Base64.encode64(key).chomp
    encoded_iv = Base64.encode64(iv).chomp
```
<div style="clear: both;"></div>
