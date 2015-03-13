## Notifications

Rather than polling Panda to find when a video has finished encoding, Panda allows you to provide a notification endpoint and subscribe to events occurring during the encoding process.


To manage your notifications, log into your [Panda Dashboard](https://app.pandastream.com/), select your cloud and click on the `Notifications` tab.
Here you'll be able to specify a url and select events you would like to receive.
One you've saved your notification settings, notifications will be sent for any new videos and encodings of this cloud.

### Events

Panda provides the following events:

`video-created`

This is called once a video has downloaded (if a url was given), validated, and uploaded to your S3 bucket. The status will either be success or fail. An array of encoding ids is provided, although note that the encodings will not have been processed at this state.

The post request body contains the video id and metadata that has been extracted.

```ruby
{
  "event" => "video-created",
  "video_id" => '123234',
  "encoding_ids" => {0 => 'firstid', 1 => 'secondid'}
}
```
<div style="clear: both;"></div>
```php
$_POST["event"] // => "video-created"
$_POST["video_id"] // => '123234'
$_POST["encodings_ids"][0] // => 'firstid'
```
<div style="clear: both;"></div>
`video-encoded`

This is called when all encodings for a video have been completed. Note that this may be returned multiple times if, for example, a new encoding is added for an existing video.

```ruby
{
  "event" => "video-encoded",
  "video_id" => "bfa6029038de24e4494f20a166700021",
  "encoding_ids" => {0 => 'firstid', 1 => 'secondid'}
}
```
<div style="clear: both;"></div>
If you would like to show videos to your users before all encodings are complete these events can be useful.

`encoding-progress`

This is called approximately every 10s while an encoding is processing, and returns the percentage complete. Use with care since this will generate a lot of traffic!

```ruby
{
  "event" => "encoding-progress",
  "encoding_id" => 'firstid',
  "progress" => 23
}
```
<div style="clear: both;"></div>
`encoding-complete`

This is called for each encoding when the encoding is done (or failed)

The post body contains encoding information

```ruby
{
  "event" => "encoding-complete",
  "encoding_id" => "bfa6029038de24e4494f20a166700021"
}
```
<div style="clear: both;"></div>
After receiving those IDs you can easily and securely retrieve the state of your videos/encoding by simply using your current library.

```ruby
Panda::Video.find(params['video_id']).status
=> 'success'
```
<div style="clear: both;"></div>
### Integrate notifications

For each event you will receive a POST request, containing the event name (ex: encoding-complete) and some ids the videos or encodings having their status changed, as form data.

Panda is expecting a 200 response from you. Otherwise it will retry the notification within an exponential interval of time and during 24 hours. The `Initial notification delay` field allows to select the initial waiting time in order to manipulate the number and frequency of retried notifications, for example in case of endpoint not being capable of serving large amounts of incoming requests and getting overloaded.

The following example shows a best practice for you to handle those notifications using Async workers.
In this example we are going to use [Sidekiq](https://github.com/mperham/sidekiq) to handle async jobs.

```ruby
class PandaController < ApplicationController
  def notifications
    if params['event'] == 'video-encoded'
        video = Video.find_by_panda_video_id(video_id)
        if video
            UpdateVideoState.perform_async(video.id)
        else
            # might have been deleted
        end
    end
  end
end

class UpdateVideoState
  include Sidekiq::Worker

  def perform(id)
    video = Video.find(id)
    if video.panda_video.status == 'fail'
        # video failed to be uploaded to your bucket
    else
        h264e = video.panda_video.encodings['h264']
        if h264e.status == 'success'
            # save h264e.url in your models to avoid querying panda each time
        else # handle a failed job
            # h264e.error_class; h264e.error_message
            # a log file has been dumped in your bucket
        end
    end
  end
end
```
<div style="clear: both;"></div>
### Setup in development

Because your development machine is rarely accessible from the Internet,
the notifications cannot be sent to you directly. At Panda, we use
[ultrahook](http://www.ultrahook.com/) to give access to our local
development machine to the Internet.

1 Create a tunnel

```sh
$ gem install ultrahook
# 3000 is the port of the local webserver
$ ultrahook foo 3000
  Authenticated as senvee
  Forwarding activated...
  http://foo.senvee.ultrahook.com -> http://localhost:3000
```
<div style="clear: both;"></div>

2 Now set the notification URL in your development cloud to http://foo.senvee.ultrahook.com</br>
3 Your local app starts receiving notification callbacks

Alternatively, if you just want to see what output Panda is producing,
create a new target on [requestb.in](http://requestb.in/) and set it up as the notification end-point.
Postbin will record Panda's callbacks and let you see them trough their web interface.
