##Player

### Setting up your video player

Panda gives you to flexibility to use the video player which suits you best. There are several great players available, below we explain the benefits of each and how to use them with Panda.

### HTML5 players

HTML5 is the new standard for embedding videos on the web. The New HTML5 video tag plays video natively in the user's browser or device. If the browser or device doesn't support the video tag, several HTML5 video players will automatically fallback to a Flash player.

There is an excellent section in [Dive Into HTML5](http://diveintohtml5.info/video.html) which explains the new `<video>` in great detail. You might want to skip to the [section about the markup](http://diveintohtml5.info/video.html#markup).

There are a few ways to use HTML5 video. In the most basic form you can use a simple `<video>` tag to embed video. Using this method the video will work with the following:

* Firefox 3.5+
* Chrome
* Safari 3+
* Opera
* IE 9
* iPhone and iPad

To support older browsers, you can use a HTML5 video player which supports Flash player fallback. After the next section you'll find details about several good players which support this.

For more details about embedding videos in your application please refer to the [Ruby on Rails](#rails) and [PHP](#php) guides.

### Basic video tag

To support HTML5 video in most browsers there must be **H.264** and **OGG Theora** versions of your videos (Firefox does not have support for H.264 and requires OGG Theora).

To enable H.264 (MP4) and Theora (OGG) in Panda, simply head to [your
account](https://app.pandastream.com/clouds), edit one of your encoding
clouds and add enable them as presets. Once this is done, all videos
uploaded will be encoded to these formats. See the [Encoding Presets
docs](#presets) for more details about managing presets.

The simplest way to then embed your videos is using a basic `<video>` tag:

```html
<video id="movie" width="320" height="240" preload="none"
  poster="http://s3.amazonaws.com/YOUR_S3_BUCKET/ENCODING_ID_4.jpg" controls>
  <source src="http://s3.amazonaws.com/YOUR_S3_BUCKET/ENCODING_ID.mp4" type="video/mp4">
  <source src="http://s3.amazonaws.com/YOUR_S3_BUCKET/ENCODING_ID.ogg" type="video/ogg">
</video>
```
<div style="clear: both;"></div>
In the above example you would need to replace `VIDEO_ID` with the ID of the video from Panda, and then the `YOUR_S3_BUCKET` with the name of your S3 bucket.

### JW Player for HTML5

There is now a HTML5 version of this popular player. [JW Player for HTML5](http://www.longtailvideo.com/support/jw-player/jw-player-for-html5) supports theming and automatically falls back to the Flash version the browser doesn't support HTML5 video. Full details about the player are [here](http://www.longtailvideo.com/support/jw-player/jw-player-for-html5). The best place to start is by downloading the player and then reading the [Embedding The Player](http://www.longtailvideo.com/support/jw-player/jw-player-for-html5/11890/embedding-the-player) instructions. Make sure you read the Multiple Sources section to see how to specify multiple formats.

Below is an example of embedding the player using Panda:

First, place this code in the `<head>` of your page:

```html
<script type="text/javascript" src="/scripts/jquery.js"></script>
<script type="text/javascript" src="/scripts/jquery.jwplayer.js"></script>
```
<div style="clear: both;"></div>
Second, place this code where you want the video to appear:

```html
<video width="320" height="240" id="player" poster="http://s3.amazonaws.com/YOUR_S3_BUCKET/ENCODING_ID_4.jpg">
  <source src="http://s3.amazonaws.com/YOUR_S3_BUCKET/ENCODING_ID.mp4" type="video/mp4">
  <source src="http://s3.amazonaws.com/YOUR_S3_BUCKET/ENCODING_ID.ogv" type="video/ogg">
</video>

<script type="text/javascript">
  $('#player').jwplayer({
    flashplayer:'/files/player.swf',
    skin:'/files/skins/five/five.xml'
  });
</script>
```
<div style="clear: both;"></div>
### FlareVideo

The [FlareVideo](http://flarevideo.com/) a great HTML5 video player which has theming support with a seamless fallback to Flash.

### Video JS

The [Video JS player](http://videojs.com/) is another option for displaying HTML5 video. The player is very lightweight and will fallback to the [Flash Flowplayer](http://flowplayer.org/).

### Flash Players

Using a Flash video player is currently the most reliable way to embed video, and also allows you to easily add in advertising and other interactive features.

There are several options including [JW Player](http://www.longtailvideo.com/players/jw-flv-player/) and [Flowplayer](http://flowplayer.org). The instructions below are for using [JW Player](http://www.longtailvideo.com/players/jw-flv-player).

First Download JW Player 5.1, unzip and copy `player.swf` `swfobject.js` and to you public directory.

You can keep the same controller code as above.

In your view insert your video player as the following:

```html
<div id='mediaspace'>This text will be replaced</div>

<script type='text/javascript'>
  var so = new SWFObject('/player.swf','mpl',"VIDEO_WIDTH","VIDEO_HEIGHT",'9');
  so.addParam('allowfullscreen','true');
  so.addParam('allowscriptaccess','always');
  so.addParam('wmode','opaque');
  so.addVariable('file',"http://s3.amazonaws.com/YOUR_S3_BUCKET/ENCODING_ID.mp4");
  so.addVariable('image',"http://s3.amazonaws.com/YOUR_S3_BUCKET/ENCODING_ID_4.jpg");
  so.write('mediaspace');
</script>
```
