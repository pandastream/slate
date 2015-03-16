## Profiles

> Example request

```
GET /profiles.json
```

> Example response

```
status: 200
```
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

<p class="red-text">GET /profiles.json</p>

Returns a collection of Profile objects.

#### Optional parameters

parameters | atributes
------------ | -------------
page | Default: 1.
per_page | Default: 100.

<div style="clear: both;"></div>

> Example request

```
GET /profiles/40d9f8711d64aaa74f88462e9274f39a.json
```

> Example response

```
status: 200
```
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

<p class="red-text">GET /profiles/:id_or_name.json</p>

Returns a Profile object.

#### Required Parameters

parameters | atributes
------------ | -------------
id_or_name | ID or name of the Profile object.

<div style="clear: both;"></div>

<p class="red-text">POST /profiles.json</p>

Adds new profile to cloud. It can be used later to encode videos.

### Using presets

> Example request

```
POST /profiles.json

preset_name: 'h264'
```

> Example response

```
status: 200
```
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

#### Required parameters

parameters | atributes
------------ | -------------
preset_name | Name of preset to use.

#### Optional parameters

parameters | atributes
------------ | -------------
name                 | Unique Machine-readable name that will identify the profile. Helpful later on for filtering encodings by profile.
title                | Human-readable name.
extname              | File extension. Example: `".mp4"`.
width                | Width in pixels. Example: `1080`.
height               | Height in pixels. Example: `720`.
upscale              | Upscale the video resolution to match your profile. Default is `true`.
aspect_mode          | Default is `"letterbox"`.
two_pass             | Default is `false`.
video_bitrate        | Example: `3000`.
fps                  | Null value copy the original fps. By default it is not set. Example: `29.97`.
keyframe_interval    | Adds a key frame every 250 frames. Default is `250`, adds a key frame every 250 frames.
keyframe_rate        | Example: `0.5`, adds a key frame every 2 seconds. 
audio_bitrate        | Example: `128`.
audio_sample_rate    | Default is `44100`.
audio_channels       | By default it is not set.
clip_length          | Sets the clip's duration. Example: `"00:20:00"`.
clip_offset          | Clip starts at a specific offset. Example: `"00:00:10"`.
watermark_url        | Url of the watermark image.
watermark_top        | Distance from the top of the video frame. Works like CSS. Default is `0`.
watermark_bottom     | Distance from the bottom of the video frame. Works like CSS. Default is `0`.
watermark_left       | Distance from the left of the video frame. Works like CSS. Default is `0`.
watermark_right      | Distance from the right of the video frame. Works like CSS. Default is `0`.
watermark_width      | Width of the watermark image. Default is `no resizing`.
watermark_height     | Height of the watermark image. Default is `no resizing`.
frame_count          | Evenly spaced number of generated screenshots. Default is `7`.

#### Aspect Modes

parameters | atributes
------------ | -------------
preserve             | Original size and ratio is preserved.
constrain            | Aspect ratio is maintained.</br>No black bars is added to your output.
letterbox            | Aspect ratio is maintained.</br>Adds black bars to your output to match your profile frame height (above and below only).
pad                  | Aspect ratio is maintained.</br>Adds black bars to your output to match your profile frame size.
crop                 | Aspect ratio is maintained.</br>fills your profile frame size and crops the rest.

#### H264 preset

parameters | atributes
------------ | -------------
h264_crf             | Example: `23`.
h264_profile         | Example: `"baseline"`.
h264_level           | Example: `"3.1"`.

#### JPEG preset

**One** of the following parameters can be set.

parameters | atributes
------------ | -------------
frame_count          | Evenly spaced number of thumbnails. Example: `7`.
frame_offsets        | Array of offset (Frames or seconds). Example: `"2s, 10s, 250f, 400f"`
frame_interval       | Thumbnail interval (Frames or seconds). Example: `"1000f"`.

#### HLS.Variant and HLS.Variant.Audio presets

parameters | atributes
------------ | -------------
encryption           | Example: `true`.
encryption_key_url   | Example: `"http://example.com/my.key"`.
encryption_key       | Base64 encoded AES-128 key. Example: `"MPE2KWuqHKjk0XNR3Ehluw=="`
encryption_iv        | Base64 encoded AES-128 iv. Example: `"13ErUsaoGZ4N53pO6RrXsQ=="`.

<div style="clear: both;"></div>

### Using custom settings

> Example request

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

> Example response

```
status: 200
```
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

##### Required parameters

parameters | atributes
------------ | -------------
command | Line break separated list of encoding commands to run.
extname | File extension. Example: `".mp4"`.

#### Optional parameters

parameters | atributes
------------ | -------------
name                 | Unique Machine-readable name that will identify the profile. Helpful later on for filtering encodings by profile.
title                | Human-readable name.
width                | Width in pixels. Example: `1080`.
height               | Height in pixels. Example: `720`.
upscale              | Upscale the video resolution to match your profile. Default is `true`.
aspect_mode          | Default is `"letterbox"`.
two_pass             | Default is `false`.
video_bitrate        | Example: `3000`.
fps                  | Null value copy the original fps. By default it is not set. Example: `29.97`.
keyframe_interval    | Adds a key frame every 250 frames. Default is `250`, adds a key frame every 250 frames.
keyframe_rate        | Example: `0.5`, adds a key frame every 2 seconds. 
audio_bitrate        | Example: `128`.
audio_sample_rate    | Default is `44100`.
audio_channels       | By default it is not set.
clip_length          | Sets the clip's duration. Example: `"00:20:00"`.
clip_offset          | Clip starts at a specific offset. Example: `"00:00:10"`.
watermark_url        | Url of the watermark image.
watermark_top        | Distance from the top of the video frame. Works like CSS. Default is `0`.
watermark_bottom     | Distance from the bottom of the video frame. Works like CSS. Default is `0`.
watermark_left       | Distance from the left of the video frame. Works like CSS. Default is `0`.
watermark_right      | Distance from the right of the video frame. Works like CSS. Default is `0`.
watermark_width      | Width of the watermark image. Default is `no resizing`.
watermark_height     | Height of the watermark image. Default is `no resizing`.
frame_count          | Evenly spaced number of generated screenshots. Default is `7`.

<div style="clear: both;"></div>

> Example request

```
PUT /profiles/40d9f8711d64aaa74f88462e9274f39a.json

title: The best custom profile
```

> Example response

```
status: 200
```
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

<p class="red-text">PUT /profiles/:id.json</p>

Edit existing profile settings.

#### Required parameters

parameters | atributes
------------ | -------------
id | ID of the Profile object.

#### Optional parameters

parameters | atributes
------------ | -------------
name                 | Unique Machine-readable name that will identify the profile. Helpful later on for filtering encodings by profile.
title                | Human-readable name.
extname              | File extension. Example: `".mp4"`.
width                | Width in pixels. Example: `1080`.
height               | Height in pixels. Example: `720`.
upscale              | Upscale the video resolution to match your profile. Default is `true`.
aspect_mode          | Default is `"letterbox"`.
two_pass             | Default is `false`.
video_bitrate        | Example: `3000`.
fps                  | Null value copy the original fps. By default it is not set. Example: `29.97`.
keyframe_interval    | Adds a key frame every 250 frames. Default is `250`, adds a key frame every 250 frames.
keyframe_rate        | Example: `0.5`, adds a key frame every 2 seconds. 
audio_bitrate        | Example: `128`.
audio_sample_rate    | Default is `44100`.
audio_channels       | By default it is not set.
clip_length          | Sets the clip's duration. Example: `"00:20:00"`.
clip_offset          | Clip starts at a specific offset. Example: `"00:00:10"`.
watermark_url        | Url of the watermark image.
watermark_top        | Distance from the top of the video frame. Works like CSS. Default is `0`.
watermark_bottom     | Distance from the bottom of the video frame. Works like CSS. Default is `0`.
watermark_left       | Distance from the left of the video frame. Works like CSS. Default is `0`.
watermark_right      | Distance from the right of the video frame. Works like CSS. Default is `0`.
watermark_width      | Width of the watermark image. Default is `no resizing`.
watermark_height     | Height of the watermark image. Default is `no resizing`.
frame_count          | Evenly spaced number of generated screenshots. Default is `7`.

<div style="clear: both;"></div>

> Example request

```
DELETE /profiles/40d9f8711d64aaa74f88462e9274f39a.json
```

> Example response

```
status: 200
```

<p class="red-text">DELETE /profiles/:id.json</p>

Deletes requested profile from your cloud. Returns nothing.

#### Required Parameters

parameters | atributes
------------ | -------------
id | ID of the Profile object.

<div style="clear: both;"></div>
