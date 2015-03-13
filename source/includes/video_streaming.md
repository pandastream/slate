##Streaming

### Video streaming with Amazon CloudFront

Assuming you are building a high-traffic website, you will want to have your videos streamed efficiently to your users. After encoding your videos with Panda, you can stream them through a content delivery network.

By choosing to streaming your videos, users can skip to any point in the video without having to first wait for it to be downloaded as is the case with progressive download delivery.

You will already have an [Amazon Web Services](http://aws.amazon.com/) account account if you've signed up the S3 for storing videos. We recommend using [Amazon's CloudFront CDN](http://aws.amazon.com/cloudfront/).

CloudFront gives you two ways to deliver video content:

* Progressive Download
* Streaming (using RTMP)

The main difference between Streaming and Progressive Download is in how the data is received and stored by the end user.

You can find some documentation on [Wikipedia](http://en.wikipedia.org/wiki/Progressive_download#HTTP_Progressive_Download_versus_Streaming_Media)

The document will cover setting up a video streaming. Progressive download is covered by the standard [video player integration documentation](#player).

### Create a Streaming distribution

Adding Streaming is very easy. Let's go through all the steps right now.

* First you need to sign in to Amazon and go to the [CloudFront console](https://console.aws.amazon.com/cloudfront)

![Amazon CloudFront signin](/images/docs/howto_aws_signin.png)

* Click on button `Create Distribution` on the top-left corner

![Amazon CloudFront signin](/images/docs/howto_acf_create_dist_button.png)

A new form will appear. 

![Amazon CloudFront signin](/images/docs/howto_acf_new_form.png)

* Select Streaming as the delivery method
* Select your bucket in the list
* Adding CNAMEs will make the distribution URL nicer (optional)
* Select `enabled`

It should look similar to this:

![Amazon CloudFront signin](/images/docs/howto_acf_new_dist_form.png)

* Click on `create`

Congrats, you now have a streaming distribution!

### Play your videos

Before you begin streaming video, you will need to make sure you have a compatible encoding profile setup in your Panda encoding cloud. CloudFront supports both FLV and MP4 streaming. We highly recommend using MP4 as the quality is far superior.

Panda has a handy MP4 preset you can use. Simply head to your [Panda account](http://pandastream.com/clouds) and then edit one of your encoding clouds. At the bottom of the page you will see the 'Add Profile' button. Click this and choose 'MP4 (H.264)' (use Hi if you would like extra good quality video), feel free the change the dimensions if desired.

Once you've setup this profile, you will want to use the generated encodings with the embed code example below.

To take advantage of true streaming you will need to use a Flash player. [JW Player](http://www.longtailvideo.com/players/jw-flv-player/) or [Flowplayer](http://flowplayer.org/) are both great options.

The following code as an example embed using RTMP streaming with JW Player:

```html
<script type="text/javascript" src="swfobject.js"></script>
<script type="text/javascript"> swfobject.registerObject("player", "9.0.0", "/player.swf"); </script>
<object width='320' height='240' id='player'>
   <param name='movie' value='/player.swf'>
   <param name='allowfullscreen' value='true'>
   <param name='allowscriptaccess' value='always'>
   <param name='wmode' value='opaque'>
   <param name='flashvars' value='file=3ff851d2-d467-11de-87a8-00254bb33798.mp4&streamer=rtmp://s2zw4551q289e3.cloudfront.net/cfx/st&provider=rtmp'>
   <embed id='player'
           src='/player.swf'
           width='320'
           height='240'
           bgcolor='#ffffff'
           allowscriptaccess='always'
           allowfullscreen='true'
           flashvars='file=3ff851d2-d467-11de-87a8-00254bb33798.mp4&streamer=rtmp://s2zw4551q289e3.cloudfront.net/cfx/st&provider=rtmp'
    />
</object>
```
<div style="clear: both;"></div>
Before this will work, you will need to change some parameters.

**file**=the filename of the video in your S3 Bucket. Panda saves files to your bucket with the following convention: `$path$$extname$`. Please check the [API docs](#api), [Rails](#rails) or [PHP](#php) tutorials for more information about encodings.

**streamer**=value where value is the the streaming url: $domain_name$/cfx/st.
You can find the Domain Name by logging into the Amazon [CloudFront console](https://console.aws.amazon.com/cloudfront).

Once you've set these parameters correctly you'll have video being streamed to your users!
