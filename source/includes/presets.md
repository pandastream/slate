#Profiles

##Presets

### Encoding profile presets


Panda provides several encoding profile presets which make it easy to setup the correct output formats for your videos.

To manage your profiles

1. Log into your [Panda Dashboard](https://app.pandastream.com/)
2. Select your cloud
3. Click on the `Profiles` Tab
4. Click `Add profile` and you'll be able to select from one of the recommended presets.

<br/>
![Encoding profiles](/images/docs/howto_profiles.png)

### Available presets

Below you'll find details about the presets available:

##### MP4 \(H.264\)

H.264 video with AAC audio for playback on HTML5 browsers, desktop players, Flash, iPhone (3GS and above) and iPad. Use H264 baseline to support most of phones like first iPhone's and Blackberry's.

| Quality       | Standard | High      |
|---------------|----------|-----------|
| preset_name   |   h264   |  h264.hi  |
| h264 profile  |   high   |  high     |
| video bitrate | 500kbps  | 1000kbps  |
| audio bitrate | 128kbps  | 128kbps   |
| resolution    | 640x480  | 1280x720  |

##### WebM (VP8)

VP8 video with Ogg Vorbis audio for playback in HTML5 compatible browsers.

| Quality       | Standard | High      |
|---------------|----------|-----------|
| preset_name   |  webm    |  webm.hi  |
| video bitrate | 500kbps  | 1000kbps  |
| audio bitrate | 128kbps  | 128kbps   |
| resolution    | 640x480  | 1280x720  |

##### OGG (Theora)
Theora video with Ogg Vorbis audio for playback in HTML5 compatible browsers.

| Quality       | Standard | High      |
|---------------|----------|-----------|
| preset_name   |   ogg    |  ogg.hi   |
| video bitrate | 500kbps  | 1000kbps  |
| audio bitrate | 128kbps  | 128kbps   |
| resolution    | 640x480  | 1280x720  |

##### HTTP Live Streaming (iOS Adaptive Streaming)

Special multi-bitrate segmented video for streaming to Apple devices.

Create a Master playlist profile that will list all sub-streams into one file.

| Quality       | HLS Playlist         |
|---------------|----------------------|
| preset_name   | hls.variant.playlist |
| variants      | hls.*                |

Then create a profile for each sub-stream of your Master Playlist. Each profile sub-stream (EXT-X-STREAM-INF) contains RESOLUTION, BANDWIDTH tags to help the client to choose an appropriate stream.

iOS devices will streams the best variant depending on the quality of the phone signal. See [Apple's recommendation](http://developer.apple.com/library/ios/#technotes/tn2224/_index.html) for more details.


| Quality       | HLS              | HLS Audio Only      |
|---------------|------------------|---------------------|
| preset_name   | hls.variant      | hls.variant.audio   |
| video bitrate | 500kbps          | -                   |
| audio bitrate | 56kbps           | 56kbps              |
| resolution    | 640x480          | -                   |

You can create different playlists (one for iPhone one for iPad).
Only the profiles with their names matching the variant attribute will be added to the playlist.

```ruby
# Sub streams
Panda::Profile.create!({
  :name => "hls.iphone.400k"
  :preset_name => "hls.variant",
  :video_bitrate => 400
})
Panda::Profile.create!({
  :name => "hls.iphone.600k"
  :preset_name => "hls.variant",
  :video_bitrate => 600
})
Panda::Profile.create!({
  :name => "hls.ipad.1200k"
  :preset_name => "hls.variant",
  :video_bitrate => 1200
})
Panda::Profile.create!({
  :name => "hls.audio"
  :preset_name => "hls.variant.audio",
})

# iPhone and iPad Playlist
Panda::Profile.create!({
  :name => "iphone",
  :preset_name => "hls.variant.playlist",
  :variants => "hls.iphone*,hls.audio"
})
Panda::Profile.create!({
  :name => "ipad",
  :preset_name => "hls.variant.playlist",
  :variants => "hls.iphone*,hls.ipad*,hls.audio"
})
```
<div style="clear: both;"></div>
##### HSS (Microsoft Smooth Streaming)

| preset_name   |   hss   |
|---------------|---------|
| video bitrate | 500kbps |
| audio bitrate | 128kbps |
| resolution    | 640x480 |

Microsoft Smooth Streaming preset.

The default extension name setting, ".tar", causes packing output files (.ismv, .ismc, .ism) into a single tar archive. This value can also be changed into a ".ism" one in order to disable this feature. In that case all files will be uploaded separately into a cloud and only .ism manifest file will be available for download through the API.

The default video bitrate setting produces segments with bitrates of 2400k, 600k, 300k and 120k. If you insert custom value, your output will consist of segments having bitrate equal to provided one, 1/2, 1/4 and 1/8 of it.

##### MP3

MP3 audio preset.

| preset_name   |   mp3   |
|---------------|---------|
| audio bitrate | 192kbps |

##### M4A (AAC)

MP4 audio preset.

| preset_name   |   aac   |
|---------------|---------|
| audio bitrate | 192kbps |

##### OGA (Vorbis)

Ogg Vorbis audio preset.

| preset_name   |  vorbis |
|---------------|---------|
| audio bitrate | 192kbps |

##### Add an H264 profile

Panda gives you complete control of presets using the API and the available libraries. For this, simply pass the `preset_name` attribute instead of the `command` attribute.

In ruby it would look like this

```ruby
Panda::Profile.create!({
  :preset_name => "h264", # A valid preset name, as per the tables above
  :name => "my-h264", # Unique name for this profile
  :h264_crf => 23,
  :h264_profile => "baseline",
  :h264_level => "3.0",
  :width => "480",
  :height => "320"
})
```
<div style="clear: both;"></div>
### Add Watermarking

You may want to add watermarking to your encodings. Use the api to set the url and the position of a remote image to use as a watermark.

```ruby
Panda::Profile.create!({
  :name => "h264",
  :preset_name => "h264",
  :width => 480,
  :height => 320,
  :watermark_url => "http://www.pandastream.com/images/panda_logo.png",
  :watermark_bottom => 5,
  :watermark_right => 5
})
```
<div style="clear: both;"></div>
### Create Thumbnails

You have two ways of creating thumbnails.

* Each encodings create 7 screenshots by default. They are evenly spaced, captured directly from the encoding output, which means they have exactly the same dimensions and quality as your encoding.
You can update the size of the capture set using the `frame_count` attribute.

```ruby
Panda::Profile.create!({
  :preset_name => "h264",
  ....
  :frame_count => 5
})
```
<div style="clear: both;"></div>
Screenshots will be generated and uploaded to the S3 bucket with the name using the following convention: `$path$_$screenshot_number$.jpg` e.g. (`my-path/2f8760b7e0d4c7dbe609b5872be9bc3b_4.jpg`).


* You can either create a Thumbnail profile. Thumbnails are performed directly on the original video.
You can modify the format, size, the aspect mode and the rate.

```ruby
Panda::Profile.create!({
  :preset_name => "thumbnail",
  ....
  :extname => '.jpg', # or .png
  :frame_interval => "300f",
  :width => 90,
  :height => 60,
})
```
<div style="clear: both;"></div>
The resulting encoding will have all the thumbnails listed inside the `files` attribute

### Complete settings

You can set numerous of options to make your encoding just right.

```ruby
Panda::Profile.create!({
  # h264 profile
  :preset_name => "h264",

  # sets your profile name to h264
  :name => "h264",

  # 2 pass encoding
  :two_pass => true,

  # 30 fps
  :fps => 30,

  # mp4 format and .mp4 extname
  :extname => ".mp4"

  # 48k sample rate
  :audio_sample_rate => 48000,

  # Resolution 1080x720
  :height => 720,
  :width => 1080,

  # set the s3 destination
  :path_format => "my/path/:id",

  # don't upscale the video if the input is smaller than your profile resolution
  :upscale => false,

  # starts the clip after 10 seconds
  :clip_offset => "00:00:10",

  # Cut the clip after 20 minutes
  :clip_length => "00:20:00",

  # Keep aspect ratio
  :aspect_mode => 'letterbox',

  # Bitrates
  :video_bitrate => 2200,
  :audio_bitrate => 128,
})
```
<div style="clear: both;"></div>