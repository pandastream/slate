## FFmpeg Neckbeard

Panda provides complete control over the commands used to convert video. You can log in to your [Panda Dashboard](https://app.pandastream.com/) and [choose from our presets](#presets), or you can use the API to access the bare metal.



### Stacks

As ffmpeg API changes, we can't always update ffmeg without breaking your commands. That's where presets become handy.
To make sure your commands are backward compatible with the encoding API and ffmpeg's API, we are using separate stacks of tools.
The default stack is **corepack-1**. There is also **corepack-2** and **corepack-3**. **Corepack-1** will be deprecated soon and replaced by **corepack-2**. However, all profiles created with the previous stack will continue to work. 

If you use API to create your profiles and want to stay on the old stack, make sure you specify **'stack' => 'corepack-1'**. You can find the **'corepack-1'** docs [here](/docs/deprecated/encoding_profiles)

We highly recommend to switch to presets or to the newest stack (**corepack-3**). It improves encoding/decoding speed and formats/codecs support.

### API

All of the fields below are required when creating an advanced profile.

```
extname:          File extension, beginning with '.', of the output video.
command:          Video conversion command (in most cases an ffmpeg command).
name:             Which is a unique name inside your cloud to reference your profile.
```
<div style="clear: both;"></div>

Panda provides full access to a suite of powerful video conversion tools. These tools can be used together in one profile to output a variety of formats. 

To execute multiple commands in one profile, you must separate each with a `\n` (newline) or `;` (semicolon).

<aside class="notice">
  <strong>Note:</strong>
In php, if you are using double quotes in string, don't forget to escape `$` or profile variables will be considered as php variables. <code>command => " ...\$input\$ "</code>
</aside>



Panda provides a few `api tokens` you can use in your commands:

parameters | atributes
------------ | -------------
$input_file$:          |input filename
$output_file$:         |output filename
$width$:               |width defined in your profile
$height$:              |height defined in your profile
$record_id$:           |encoding ID
$filters$:             |scale your video considering the aspect mode and resolution attributes of your profile.
$audio_sample_rate$:   |audio sample rate
$audio_channels$:      |audio channels (default: copy)
$audio_bitrate$:       |audio_bitrate
$video_quality$:       |video bitrate or h264 crf
$video_bitrate$:       |video_bitrate only
$max_audio_bitrate$:   |audio bitrate threshold
$max_video_bitrate$:   |video bitrate threshold
$fps$:                 |frame rate (default: copy)
$clip$:                |subset of the video to be encoded


</br>
H264 specific tokens

parameters | atributes
------------ | -------------
$h264_profile$:   |h264 profile (baseline, main, high)
$h264_level$:     |h264 level (1.3, 3.0, 3.1,...)

### Examples

Let's create a custom h.264 profile.

```ruby
Panda::Profile.create!({
  :stack => "corepack-3",
  :name => "h264.SD",
  :width => 480,
  :height => 320,
  :h264_crf => 23,
  :audio_bitrate => 128,
  :extname => ".mp4",
  :audio_sample_rate => 44100,
  :keyframe_interval => 250,
  :command => "ffmpeg -i $input_file$ -threads 0 -c:a libfaac -c:v libx264 -preset medium $video_quality$ $audio_bitrate$ $audio_sample_rate$ $keyframes$ $fps$ -y video_tmp_noqt.mp4\nqt-faststart video_tmp_noqt.mp4 $output_file$"
})
```
<div style="clear: both;"></div>
```php
<?php
$panda->post('/profiles.json', array(
    'stack' => "corepack-3",
    'name' => "h264.SD",
    'width' => 480,
    'height' => 320,
    'h264_crf' => 23,
    'audio_bitrate' => 128,
    'extname' => ".mp4",
    'audio_sample_rate' => 44100,
    'keyframe_interval' => 250,
    'command' => 'ffmpeg -i $input_file$ -threads 0 -c:a libfaac -c:v libx264 -preset medium $video_quality$ $audio_bitrate$ $audio_sample_rate$ $keyframes$ $fps$ -y video_tmp_noqt.mp4\nqt-faststart video_tmp_noqt.mp4 $output_file$'
));
```
<div style="clear: both;"></div>
You may not find a preset for your exactly need but the following examples should help you to encode videos into some popular formats.

##### FLV

```ruby
Panda::Profile.create!({
  :stack => "corepack-3",
  :name => "flv",
  :width => 480,
  :height => 320,
  :video_bitrate => 500,
  :audio_bitrate => 128,
  :extname => ".flv",
  :command => "ffmpeg -i $input_file$ -threads 0 $audio_sample_rate$ $video_bitrate$ $audio_bitrate$ $fps$ $filters$ -y $output_file$"
})
```
<div style="clear: both;"></div>
##### AVI

```ruby
Panda::Profile.create!({
  :stack => "corepack-3",
  :name => "avi",
  :width => 480,
  :height => 320,
  :video_bitrate => 500,
  :audio_bitrate => 128,
  :extname => ".avi",
  :command => "ffmpeg -i $input_file$ -threads 0 -c:v msmpeg4 -c:a libmp3lame -tag:v MP43 $audio_sample_rate$ $audio_sample_rate$ $video_bitrate$ $audio_bitrate$ $fps$ $filters$ -y $output_file$"
})
```
<div style="clear: both;"></div>
##### WMV

```ruby
Panda::Profile.create!({
  :stack => "corepack-3",
  :name => "wmv",
  :width => 480,
  :height => 320,
  :video_bitrate => 500,
  :audio_bitrate => 128,
  :extname => ".wmv",
  :command => "ffmpeg -i $input_file$ -threads 0 -c:v wmv2 -c:a wmav2 $audio_sample_rate$ $video_bitrate$ $audio_bitrate$ $fps$ $filters$ -y $output_file$"
})
```
<div style="clear: both;"></div>

##### OGG - 2 pass

  Our presets support two pass encoding but if you need the command, here is the way to do it:

```ruby
Panda::Profile.create!({
    :stack => "corepack-3",
    :name => "ogg.2pass",
    :width => 480,
    :height => 320,
    :video_bitrate => 500,
    :audio_bitrate => 128,
    :extname => ".ogg",
    :command => "ffmpeg -i $input_file$ -f ogg -pass 1 -threads 0 -c:v libtheora -c:a libvorbis $audio_sample_rate$ $video_bitrate$ $audio_bitrate$ $fps$ $filters$ -y /dev/null\nffmpeg -i $input_file$ -f ogg -pass 2 -threads 0 -c:v libtheora -c:a libvorbis $audio_sample_rate$ $video_bitrate$ $audio_bitrate$ $fps$ $filters$ -y $output_file$"
  })
```
<div style="clear: both;"></div>
### Available conversion tools

Here is the list of conversion tools available in Panda.

`FFmpeg`

See [http://ffmpeg.org](http://ffmpeg.org)

> FFmpeg is a complete, cross-platform solution to record, convert and stream audio and video. It includes libavcodec - the leading audio/video codec library.

<div style="clear: both;"></div>
We provide a recent version of ffmpeg, compiled from the git repository with as many codecs as we could get our hands on ([See full list of Supported File Formats and Codecs](#formats)).

command name: ffmpeg</br>
command version: Ffmpeg 0.10

`Lame`

See [http://lame.sourceforge.net/](http://lame.sourceforge.net/)

LAME is a high quality MPEG Audio Layer III (MP3) encoder.

command name: lame</br>
command version: Lame 3.99

`MEncoder`

See [http://http://www.mplayerhq.hu/](http://www.mplayerhq.hu/)

An other video encoder built from the same code as MPlayer.

command name: mencoder

`Segmenter`

Segmenter divides input file into smaller parts for HLS playlists.

command name: segmenter

`Manifester`

Manifester is a tool to create m3u8 manifest file.

command name: manifester

`QtFaststart`

See [http://multimedia.cx/eggs/improving-qt-faststart/](http://multimedia.cx/eggs/improving-qt-faststart/)

Moves the moov atom in front of the data to improve network streaming.

command name: qt-faststart

`QtRotate`

See [https://github.com/danielgtaylor/qtrotate](https://github.com/danielgtaylor/qtrotate)

QtRotate is a tool to work with rotated files.

command name: qtrotate

`Yamdi`

See [http://yamdi.sourceforge.net/](http://yamdi.sourceforge.net/)

Yamdi is a metadata injector for FLV files.

command name: yamdi

`MP4Box`

See [http://gpac.wp.mines-telecom.fr/mp4box/](http://gpac.wp.mines-telecom.fr/mp4box/)

Mp4Box is a tool for manipulating multimedia files.

command name: MP4Box
