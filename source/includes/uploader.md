#Integration

##Uploader


###Panda uploader

The Panda uploader allows you to upload videos from your web application to Panda. It's an HTML5 uploader solution with Flash fallback.

![Javascript Uploader](/images/docs/uploader_preview.png)

<aside class="notice">
  <strong>Note:</strong>
  All of the examples on this page use jQuery, however the uploader is library agnostic.
  Documentation about the uploader 1.x can be found <a href="/docs/deprecated/panda_uploader">here</a>
</aside>



### What's new

Here is a couple of features that forced us to rewrite the uploader from scratch.

1.   Library agnostic.
2.   Better code base and a better api.
3.   Resumable uploader. We built a new [upload backend](#upload-api) to handle the resume of your upload if you go offline.
      This is great for big files.
4.   Upload queueing. You can easily add multiple videos on a single page.
5.   Consistent UI between HTML5 and FLASH.

<br/>

### Install the library

We <strong>highly recommended</strong> that you use the CDN hosted minified version of the uploader library. You can keep up to date automatically with patch releases by linking to a minor version. The current stable version is 2.3.
To do so, simply include the following declaration in your page:

```html
<script src="//cdn.pandastream.com/u/2.3/panda-uploader.min.js"></script>
```
<div style="clear: both;"></div>
If for some reason you need non minified version, you can include it like that:

```html
<script src="//cdn.pandastream.com/u/2.3/panda-uploader.js"></script>
```
<div style="clear: both;"></div>

### Uploader API

By invoking `panda.uploader.init(options)` with a set of configuration options, you will initialize the Panda uploader

Here is the list of options available at the time of initialization:

parameters | atributes
------------ | -------------
buttonId:                         |Dom Id of the styled Browsing button.</br>( required!, it should be a `<div>` )
authorizeUrl:                     |Route of the authentication request.</br>( example: /panda/authorize_upload )
autoStart:                        |Upload starts right after the user select a file.</br>( true/false, default: true )
autoResume:                       |Automatically resume the upload after you went offline.</br>( true/false, default: true )
resumeInterval:                   |Interval in milliseconds before the upload gets resumed.</br>( default: 5000 )
allowSelectMultipleFiles:         |Allows you to add multiple files to the upload queue.</br>( true/false, default: false )
progressBarId:                    |Dom id of the progress bar.
fileDropId:                       |Dom id of the file drop zone.
fileUrlInputId:                   |Dom id of the text input where url will be placed.
fileUrlButtonId:                  |Dom id of submit button for fileUrlInputId.
maxFileSize:                      |Maximun file size.</br>( example: '10 MB', valid: [K|M|G]B, default: null )
confirmBeforeUnload:              |Alert the user that an upload is processing.</br>( true/false, default: true )
type:                             |Forces the uploader type.</br>( html/flash, default null )
onQueue (files):                  |List of `<files>` added to the Queue.
onStart (file):                   |`<file>` starts uploading.
onProgress (file, percent):       |`<file>` upload progress.
onSuccess (file, json):           |The uploader has successfully uploaded `<file>` and returned a `<json>` object.
onError (file, text):             |The uploader has failed uploading `<file>` and returned a response.
onComplete ():                    |The upload queue has been completed.
onCancel (file):                  |`<file>` upload has been canceled.
onPause (file):                   |`<file>` upload has been paused due to connectivity issues.

</br>

`<Uploader>` Object:
  
parameters | atributes
------------ | -------------
type:                             |Returns the uploader type (html5/flash).
setPayload (file, object):        |Add extra variables to the payload.
setEnabled (bool):                |Enable/Disable the uploader.
getQueuedFiles ():                |Returns the list of queued files.
start ():                         |Start uploading next queued file.
cancel (file):                    |cancel `<file>` upload.
</br>

`<File>` Objet:
  
parameters | atributes
------------ | -------------
name:                             |File name.
size:                             |File size in Bytes.

### Upload authentication


The authentication of your new upload occurs via an HTTP Request to a configurable authentication url when the file is ready to be uploaded. In the JavaScript Uploader the HTTP Request is executed via AJAX POST request.
The destination of the authentication request can be configured by setting the authorizeUrl param when calling `panda.uploader.init`.

The default is `/panda/authorize_upload`

A payload is then sent as a JSON encoded object in the body and can contain 2 sets of variables:

1. `filesize`, `filename`, `content_type`
2. `fileurl`

These should be self-explanatory.

Here are some examples of upload authentication for different languages:

```ruby
# Using Rails and https://github.com/pandastream/panda_gem
# app/controllers/panda_controller.rb
class PandaController < ApplicationController
  def authorize_upload
    payload = JSON.parse(params['payload'])
    
    options = {
      profiles: "h264,webm",
      # payload: 'something',
      # path_format: ':video_id/:profile/play',
    }
    if payload['filename']
      url = '/videos/upload.json'
      options['file_name'] = payload['filename']
      options['file_size'] = payload['filesize']
    else
      url = "/videos.json"
      options['source_url'] = payload['fileurl']
    end
    
    upload = Panda.post(url, options)

    render :json => {:upload_url => upload['location']}
  end
end
```
<div style="clear: both;"></div>
```python
# Using Django and https://github.com/pandastream/panda_client_python
# views.py
@csrf_exempt
def authorize_upload(request):
  params = json.loads(request.POST['payload'])
  
  options = {"profiles": "h264"}
  if params["filename"]:
    url = "/videos/upload.json"
    options["file_name"] = params["filename"]
    options["file_size"] = params["filesize"]
  else:
    url = "/videos.json"
    options["source_url"] = payload["fileurl"]
  
  upload = panda.post(url, options)

  auth = {"upload_url": json.loads(upload)["location"]}
  return HttpResponse(json.dumps(auth), mimetype='application/json')
```
<div style="clear: both;"></div>
```php
<?php
// authorize_upload.php
$payload = json_decode($_POST['payload']);

$filename = $payload->{'filename'};
$filesize = $payload->{'filesize'};
$fileurl = $payload->{'fileurl'};

$options = array('profiles' => 'h264');
if ($filename) {
  $url = '/videos/upload.json';
  $options['file_name'] => $filename;
  $options['file_size'] => $filesize;
} else {
  $url = '/videos.json';
  $options['source_url'] => $fileurl;
}

$upload = json_decode(@$panda->post($url, $options);

$response = array('upload_url' => $upload->location);

header('Content-Type: application/json');
echo json_encode($response);
?>
```
<div style="clear: both;"></div>
The api request to Panda should include:
for `/videos/upload.json` endpoint `file_name` and `file_size`
for `/videos.json` endpoint `source_url`

Optionally, you can set:

parameters | atributes
------------ | -------------
use_all_profiles:   |(true / false) Default is false
profiles:           |Comma-separated list of profile names.</br>By default no encodings are created yet.
payload:            |Arbitrary string stored along the Video object.
path_format:        |Allows you to set the location inside your S3 Bucket.</br>It should not include the file extention.

More details on the [api docs](#api)

The generated JSON should include:

parameters | atributes
------------ | -------------
upload_url                     |Location where to upload the file
postprocess_url (optional)     |When a file upload is complete, a request is made to this url to let your server know that the uploader finished uploading the file

### Simplest example

The following is the simplest working form that will upload a video:

```html
<form action="/path/to/action" id="new_video" method="POST">

  <input type="hidden" name="panda_video_id"/>

  <div id="browse-files">Choose file</div>

</form>
```
<div style="clear: both;"></div>
Uploader setup

```javascript
var upl = panda.uploader.init({
  'buttonId': 'browse-files',
  'onProgress': function(file, percent) {
    console.log("progress", percent, "%");
  },
  'onSuccess': function(file, data) {
    $("#new_video")
      .find("[name=panda_video_id]")
        .val(data.id)
      .end()
      .submit();
  },
  'onError': function(file, message) {
    console.log("error", message);
  },
});
```
<div style="clear: both;"></div>
In this example:

1. Click on "Choose file". You will be shown a file selection dialog.
2. Select a file to upload.
3. Wait for the upload to complete.
4. After the upload completes, Panda returns a unique ID that identifies your video. The form will finally be submitted so that your application can read this value and use it to reference the video later on.

</br>
If you need to, you can add some extra data to the payload alongside the ones mentioned above.

```javascript
var upl = panda.uploader.init({
  ...
  'onQueue': function(files) {
    $.each(files, function(i, file) {
      upl.setPayload(file, {'authenticity_token': AUTH_TOKEN});
    })
  },
}
```
<div style="clear: both;"></div>
### Progress Bar and File Drop

The uploader comes with some handy handlers like Progress Bars and File Drop.

```html
<form action="/path/to/action" id="new_video" method="POST">

  <input type="hidden" name="panda_video_id"/>

  <div class='progress'><span id="progress-bar" class='bar'></span></div>

  <div id="file-drop">Drop files here</div>

  <div id="browse-files">Choose file</div>

</form>
```
<div style="clear: both;"></div>
Uploader setup


```javascript
var upl = panda.uploader.init({
  ...
  'progressBarId': "progress-bar",
  'fileDropId': "file-drop",
});
```
<div style="clear: both;"></div>
The uploader doesn't come with any styling. Take a look at [Twitter Bootstrap](http://twitter.github.com/bootstrap/)

### Resume an upload

If you have been disconnected from the Internet while you were uploading a file you will be notified by the `onPause` callback.
It's important to note that the Flash uploader doesn't support this feature so the upload will fail.

By default, the uploader will setup a resume interval of 5 seconds.

You can also create a resume button.

To resume your uploading queue, just call the `start` method and your current upload will continue where it left off.

```javascript
var upl = panda.uploader.init({
  ...
  'autoResume': false,
  'onPause': function(){
    $("#notice").text("You went offline.");
  }
})

$("#resume-btn").click(function() {
  upl.start();
})
```
<div style="clear: both;"></div>
### Abort an upload

If you want to abort an upload that is currently taking place, use the cancel() function from the uploader instance:

```javascript
$("#cancel-btn").click(function() {
  upl.cancel(file);
})
```
<div style="clear: both;"></div>
An aborted upload is no longer resumable.

### Upload file from another server

You can also upload any publicly available video file just by submitting the URL of it.

```html
<form action="/path/to/action" id="new_video" method="POST">

  <input type="hidden" name="panda_video_id"/>

  <input id="file-url" type="text" placeholder="Type URL to file">
  <button id="file-url-send" type="button">Upload from URL</button>
  
  <div id="browse-files">Choose file</div>

</form>
```
<div style="clear: both;"></div>
Uploader setup

```javascript
var upl = panda.uploader.init({
  ...
  'fileUrlInputId': "file-url"
  'fileUrlButtonId': "file-url-send"
});
```
<div style="clear: both;"></div>
### Upload from URL in pure JavaScript

You can also omit the HTML form part, and just call uploader method `uploadFromUrl` to achieve the same effect.

```javascript
var upl = panda.uploader.init({
  ...
});

upl.uploadFromUrl("http://mysite/video.mp4", function (error) {
  // If something went wrong the error parameter will be passed to this callback function.
  // If error is undefined it means action ended with success.
});
```
<div style="clear: both;"></div>
### Post processing

You might want to let your server know that the uploader finished uploading a file.
This is quite useful when handling multiple uploads on a single web page.

To achieve this, you simply need to return the `postprocess_url` during the authentication handshake.

```ruby
# app/controllers/panda_controller.rb
class PandaController < ApplicationController
  def authorize_upload
    ...
    render :json => {
      :upload_url => upload['location'],
      :postprocess_url => '/some/postprocess'
    }
  end
end
```
<div style="clear: both;"></div>
```python
# views.py
def authorize_upload(request):
  ...
  auth = {
    "upload_url": json.loads(upload)["location"],
    "postprocess_url": '/some/postprocess',
  }

  return HttpResponse(json.dumps(auth), mimetype='application/json')
```
<div style="clear: both;"></div>
```php
<?php
// authorize_upload.php
$response = array(
  'upload_url' => $upload->location},
  'postprocess_url' => 'postprocess.php'
  );

header('Content-Type: application/json');
echo json_encode($response);
?>
```
<div style="clear: both;"></div>
Once the video is successfully uploaded to Panda, your server will receive a POST request like this.

```ruby
# app/controllers/some_controller.rb
class SomeController < ApplicationController
  def postprocess
    response = JSON.parse(params["upload_response"])
    render :json => { :id => "some-id" }
  end
end
```
<div style="clear: both;"></div>
```python
# views.py
@csrf_exempt
def postprocess(request):
  response = json.loads(request.POST['upload_response'])
  return HttpResponse(json.dumps({"id": "some-id"}), mimetype='application/json')
```
<div style="clear: both;"></div>
```php
<?php
// postprocess.php

$response = json_decode($_POST['upload_response']);
header('Content-Type: application/json');
echo json_encode(array('id' => 'some-id'));
?>
```
<div style="clear: both;"></div>

The `onSuccess` callback will expose the data received from the postprocess request.

```javascript
var upl = panda.uploader.init({
  ...
  'onSuccess': function(file, data) {
    console.log(data['id']); // 'some-id'
  }
})
```
<div style="clear: both;"></div>
### Upload Strategies

The Panda Uploader will use HTML5 upload if the browser supports it, and will fall back to Flash upload if necessary.
Flash doesn't have resumable capabilities and will upload your content as a MultiPart stream.

|Browser            |Upload Supported? |Method Used|
|-------------------|------------------|-----------|
|Opera              |Yes               |HTML5      |
|Safari             |Yes               |HTML5      |
|Chrome             |Yes               |HTML5      |
|Firefox >= 3.5     |Yes               |HTML5      |
|IE 6-9             |Yes               |Flash      |
|Others             |Yes               |Flash      |
