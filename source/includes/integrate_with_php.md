##PHP

### Integrating Panda with your PHP application

The following is a guide to integrating Panda into your PHP application. 

Before continuing you must have a Panda account. If you haven't got one yet, you can [sign up here](https://www.pandastream.com/pricing-signup). Free accounts are available, great for playing around.

The easiest way to integrate Panda into your application is by using the open source libraries that we provide. On this example, our PHP API library, [panda\_client\_php](https://github.com/pandastream/panda_client_php).

:TOC:

<aside class="notice">
  <strong>Note:</strong>
  Guide using the uploader 1.x can be found <a href="/docs/deprecated/integrate_with_php">here</a>
</aside>

### Configuration

First you must [log in to your account](https://app.pandastream.com/login), where you can find your credentials as follows:

 * Under [Account](https://app.pandastream.com/credentials): Access key, Secret key, API URL
 * Under [Clouds](https://app.pandastream.com/clouds): Cloud ID

If you haven't yet created an encoding cloud you should [do so](https://app.pandastream.com/clouds#new) before continuing.

**PRO TIP**: you may want to create two different clouds, one for development and one for production. Note that each cloud will have a different Cloud ID, but the other credentials will be shared across all clouds.

Once equipped with this information, you can create a Panda client using the PHP library:

```php
<?php
$panda = new Panda(array(
    'cloud_id'   => 'my-cloud-id',
    'access_key' => 'my-access-key',
    'secret_key' => 'my-secret-key',
    'api_host'   => 'api.pandastream.com', 
    // Use api-eu.pandastream.com if your panda account is in the EU
));
```
<div style="clear: both;"></div>

This will give you a `$panda` object that you can use to interact with Panda. For more info, see the [documentation of the library on GitHub](https://github.com/pandastream/panda_client_php).

### Playing with the library

Let's write a simple script that uses the above configuration. Copy the following after the initialisation code:
You should be able to list all your encoding profiles.

```php
<?php
$profiles = json_decode($panda->get('/profiles.json'));
echo "<p>Profiles :</p>\n";
print_r($profiles);
```
<div style="clear: both;"></div>

The code above should print out an array containing your list of profiles. That means everything is working correctly so far.

Now, upload a sample video. You can use any URL; this is the URL of a sample we've made available:

```php
<?php
$video = json_decode($panda->post('/videos.json', array(
    'source_url' => 'http://panda-test-harness-videos.s3.amazonaws.com/panda.mp4'
)));
echo "<p>Video :</p>\n";
print_r($video);
```
<div style="clear: both;"></div>

Wait until the video has finished encoding (which could be several minutes). You can check the status by doing:

```php
<?php
$encodings = json_decode($panda->get('/videos/' . $video->id . '/encodings.json'));
echo "<p>Encodings :</p>\n";
print_r($encodings);
``` 
<div style="clear: both;"></div>

Everything's working great - time to write some application code! If you'd like to play more with the library some more, the full set of resources
are listed in the [API Documentation](#api) or the [PHP client](https://github.com/pandastream/panda_client_php).

### Implementing video uploads

![Javascript Uploader](/images/docs/uploader_preview.png)

The simplest way to begin uploading videos directly to Panda is using our [Resumable Javascript Uploader](#uploader)

#### Create an upload page

Somewhere in your application you can create the form where the upload will take place. Copy the following code into your PHP page:

```html

<form action="/player.php" id="new_video">
  
  <!-- field where the video ID will be stored after the upload -->
  <input type="hidden" name="panda_video_id"/>

  <!-- upload progress bar (optional) -->
  <div class='progress'><span id="progress-bar" class='bar'></span></div>

  <!-- file selector -->
  <div id="browse">Choose file</div>

</form>

<script>
  panda.uploader.init({
    'buttonId': 'browse',
    'progressBarId': 'progress-bar',
    'authorizeUrl': "authorize_upload.php",
    'onSuccess': function(file, data) {
      $("#panda_video_id").val(data.id)
    },
    'onComplete': function(){
      $("#new_video").submit();
    }
  });
</script>
```
<div style="clear: both;"></div>

Additionally, you need to include the uploader library:

```html
<script src="//cdn.pandastream.com/u/2.3/panda-uploader.min.js"></script>
```
<div style="clear: both;"></div>

The code above assumes after posting the form, you'll go to the page `/player.php`
When the upload is success, we fill out the hidden field `panda_video_id` and submit the form to the `player.php` page so you can process those information.

#### Create an upload session

Before you can upload a video you’ll need to authenticate and reserve an upload url for each file.

The authentication process of your new upload occurs via an HTTP Request to a configurable authentication url when the file is ready to be uploaded.
The HTTP Request is executed via AJAX POST request. The destination of the authentication request can be configured by setting the authorizeUrl.

```php
<?php
// authorize_upload.php

include('panda.php');
include('config.inc.php');

$payload = json_decode($_POST['payload']);

$filename = $payload->filename;
$filesize = $payload->filesize;

$upload = json_decode(@$panda->post("/videos/upload.json",
  array('file_name' => $filename, 'file_size' => $filesize, 'profiles' => 'h264')));

$response = array('upload_url' => $upload->location);

header('Content-Type: application/json');
echo json_encode($response);
?>
```
<div style="clear: both;"></div>

#### Create a player page
There are a few options for video playback. Read the [Video Players documentation](#player) for detailed information. The example below should be enough to get you started though using Html5.

If you don't have to support older browsers, the simplest is to use the new HTML5 `<video>` tag. To support Firefox you'll also need to add in an OGG source.


```php
<?php
$video_id = $_GET['panda_video_id']; // Coming from the previous form
$all_encodings = json_decode(@$panda->get("/videos/$video_id/encodings.json"));
$vid = $all_encodings[0];
$vid->url = "http://YOUR-S3-BUCKET-NAME.s3.amazonaws.com/{$vid->path}{$vid->extname}";
?>
<div>
    <video id="movie" width="<?php echo $vid->width ?>" height="<?php echo $vid->height ?>" preload="none" controls>
      <source src="<?php echo $vid->url ?>" type="video/mp4">
    </video>
</div>
```
<div style="clear: both;"></div>

That simple. This code assumes that you come from the form described above, and the ID of the video will therefore be in the `$_GET` variable, set by the form.
Note that you'll also have to put the name of your S3 bucket in place of the `YOUR-S3-BUCKET-NAME` text, so the URL to the encoded video can be correctly constructed.

If you refresh the show page you'll see the encoded video!

### Advanced topics

#### Encoding Profiles

You can change the formats that Panda encodes videos into by logging in to your [Panda account](https://app.pandastream.com/login). Each Cloud has its own set of Profiles. To learn more, see the sections on [Encoding Profiles](#presets).

It's possible to define several profiles and then decide on a per-video basis which ones to use. 
You can specify the list of profiles you would like your video to be encoded with while creating your upload session.

```php
<?php
$upload = json_decode(@$panda->post("/videos/upload.json",
  array('file_name' => $filename, 'file_size' => $filesize, 'profiles' => 'profile1, profile2')));
```
<div style="clear: both;"></div>

### Learn more

Now that you have a basic implementation, you might want to check out the [PHP examples](https://github.com/pandastream/panda_example_php), the [PHP client](https://github.com/pandastream/panda_client_php) and the [API documentation](#api)

### Feedback

Whether you feel that this article is incorrect, outdated or missing information, don't hesitate to contact the [support](http:// https://support.pandastream.com.io/collection/37-panda).
