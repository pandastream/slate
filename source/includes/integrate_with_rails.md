#Full Guides

##Rails

### Integrating Panda with Ruby on Rails

The following is a guide to integrating Panda into your Ruby on Rails application. However the instructions here can easily be adapted to any Ruby-based web framework or web application.

If you want to use the Heroku addon then start with the [Heroku add-on documentation](https://devcenter.heroku.com/articles/pandastream).

Before continuing you must have a [Panda account](https://www.pandastream.com/pricing-signup) and have created an encoding cloud.

Free accounts are available, great for playing around.

:TOC:

<aside class="notice">
  <strong>Note:</strong>
  Guide using the uploader 1.x can be found <a href="/docs/deprecated/integrate_with_rails">here</a>
</aside>

### Configuration

First you must [log in](https://app.pandastream.com/login) to your account where you can find your credentials as follows:

 * Under [Api Access](https://app.pandastream.com/credentials): Access key, Secret key, API URL
 * After selecting your [Cloud](https://app.pandastream.com/clouds): Cloud ID

**PRO TIP**: you may want to create two different clouds, one for development and one for production. Note that each cloud will have a different Cloud ID, but the other credentials will be shared across all clouds.

Once equipped with this information, you can create a Panda config file on `config/panda.yml`. Just copy the following example and fill it out with your own credentials where appropriate:

```yaml
development:
  access_key: Your Panda access key
  secret_key: Your Panda secret key
  cloud_id: Your Panda cloud id
  # Uncomment the line below if your panda account is in the EU
  # api_host: api-eu.pandastream.com
```
<div style="clear: both;"></div>
In your `Gemfile`, add the following line:

```ruby
gem 'panda', '~> 1.6.0'
```
<div style="clear: both;"></div>
Now install the gem:

    `$ bundle`

Next create the file `initializers/panda.rb` and then add the line below to connect to Panda when your app starts. If you're using the [Panda Heroku Add-on](http://addons.heroku.com/pandastream) you will need to set the `PANDASTREAM_URL` environment variable on your local system. Read the [Panda Heroku Add-on docs](http://docs.heroku.com/pandastream) for further details.

```ruby
Panda.configure(YAML.load_file(Rails.root.join("config/panda.yml"))[Rails.env])
```
<div style="clear: both;"></div>
### Playing with the library

Fire up a console. Your initialiser should run and connect you to Panda. You should be able to list all your encoding profiles.

```ruby
>> Panda::Profile.all
=> [<Panda::Profile preset_name: h264, ...>]
```
<div style="clear: both;"></div>
Now, upload a sample video. You can use any URL; this is the URL of a sample we've made available:

```ruby
>> video = Panda::Video.create!(:source_url => "http://panda-test-harness-videos.s3.amazonaws.com/panda.mp4")
```
<div style="clear: both;"></div>
Or use a local file:

```ruby
>> video = Panda::Video.create!(:file => File.new("/home/me/panda.mp4"))
```
<div style="clear: both;"></div>
Now wait until the video has finished encoding (which could be several minutes). You can check by doing:

```ruby
>> video.reload.status
=> "success"
```
<div style="clear: both;"></div>
Your input video has been uploaded to s3

```ruby
>> video.encodings['h264'].reload
>> video.encodings['h264'].encoding_progress
=> 100
>> video.encodings['h264'].status
=> "success"
```
<div style="clear: both;"></div>
Now you can get the URL of a re-encoded version:

```ruby
>> video.encodings['h264'].url
=> "http://s3.amazonaws.com/S3_BUCKET/e40c1f68fbc1c4db46.mp4"
```
<div style="clear: both;"></div>
Open this URL in your browser, and you'll see that Panda has received, re-encoded, and stored your video into your S3 bucket.
  
Everything's working great - time to write some application code! If you'd like to play more with the library some more, the full set of resources
are listed in the [API Documentation](/docs/api) or [Gem Docucmentation](https://github.com/pandastream/panda_gem).


### Implementing video uploads

![Javascript Uploader](/images/docs/uploader_preview.png)

The simplest way to begin uploading videos directly to Panda is using our [Resumable Javascript Uploader](#uploader)

#### Setup models and controllers

Create a Video model to save the ID of the video from Panda, we'll call it `panda_video_id`:

```
rails g model Video title:string panda_video_id:string
rake db:migrate
```
<div style="clear: both;"></div>
Panda provides its own Panda::Video model, which we'll wrap with our ActiveRecord model. 

Edit `app/models/video.rb`:

```ruby
class Video < ActiveRecord::Base
  attr_accessible :panda_video_id, :title
  validates_presence_of :panda_video_id

  def panda_video
    @panda_video ||= Panda::Video.find(panda_video_id)
  end
end
```
<div style="clear: both;"></div>
Now you can access the wrapped object with `myvideo.panda_video`, or go directly to the encodings with `myvideo.panda_video.encodings`. This call requires a call to the Panda API, so we cache it with an instance variable to save time.

We'll use a simple VideosController following the REST pattern. 

Create `app/controllers/videos_controller.rb`:

```ruby
class VideosController < ApplicationController
  def show
    @video = Video.find(params[:id])
    @original_video = @video.panda_video
    @h264_encoding = @original_video.encodings["h264"]
  end

  def new
    @video = Video.new
  end

  def create
    @video = Video.create!(params[:video])
    redirect_to :action => :show, :id => @video.id 
  end
end
```
<div style="clear: both;"></div>
In the show action, we're first getting a reference to our ActiveRecord object, as normal. Then we're getting a reference to the wrapped **Panda::Video**, as **@original_video**.

Finally, we're getting a reference to the actual encoding of the video that's been performed by Panda, so that we can pass it to the View. When a video is uploaded, one Encoding will be created for each Profile that's defined. A shiny-new Panda account comes with a single Profile created, named **h264**, but you can create more in your [account](https://app.pandastream.com/clouds).

#### Create the video upload form

Create the layout `/app/views/layouts/application.html.erb`, and include the uploader javascript library:

```html
<script src="//cdn.pandastream.com/u/2.3/panda-uploader.min.js"></script>
```
<div style="clear: both;"></div>
Now create a view which will be the video upload form: `/app/views/videos/new.html.erb`.

```erb

<%= form_for @video do |f| %>
  
  <!-- field where the video ID will be stored after the upload -->
  <input type="hidden" name="panda_video_id"/>

  <label>Title</label>
  <input type="text" name='video[title]' placeholder="Give a title">

  <!-- upload progress bar (optional) -->
  <div class='progress'><span id="progress-bar" class='bar'></span></div>

  <!-- file selector -->
  <div id="browse">Choose file</div>

<% end %>

<script>
  var upl = panda.uploader.init({
    'buttonId': 'browse',
    'progressBarId': 'progress-bar',
    'onQueue': function(files) {
      $.each(files, function(i, file) {
        upl.setPayload(file, {'csrf': "<%= form_authenticity_token %>"});
      })
    },
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
#### Create an upload session

  Before you can upload a video you’ll need to authenticate and reserve an upload url for each file.

  The authentication process of your new upload occurs via an HTTP request to a configurable authentication url when the file is ready to be uploaded.
  The HTTP request is executed via AJAX POST request. The destination of the authentication request can be configured by setting the authorizeUrl.

  The default route is `/panda/authorize_upload`

  ```ruby
  # app/controllers/panda_controller.rb
  class PandaController < ApplicationController

    # override rails' default csrf check to use the json encoded payload instead of params
    def verify_authenticity_token
      handle_unverified_request unless (form_authenticity_token == upload_payload['csrf'])
    end

    def upload_payload
      @upload_payload ||= JSON.parse(params['payload'])
    end

    def authorize_upload
      upload = Panda.post('/videos/upload.json', {
        file_name: upload_payload['filename'],
        file_size: upload_payload['filesize'],
        profiles: "h264",
      })

      render :json => {:upload_url => upload['location']}
    end
  end
  ```
<div style="clear: both;"></div>
#### Set up routes

  Finally, update your routes in `config/routes.rb`.

  ```ruby
  match "/panda/authorize_upload", :to => "panda#authorize_upload"
  resources :videos
  root :to => "videos#new"
  ```
<div style="clear: both;"></div>
#### Basic view

Now create a basic view to show the video data once it has been uploaded. Edit `/app/views/videos/show.html.erb`:

```erb
<h2><%= @video.title %></h2>
<p><%= h @video.panda_video.inspect %></p>
```
<div style="clear: both;"></div>
#### Test video uploading

Start your Rails application with `rails s` and visit [http://localhost:3000/videos/new](http://localhost:3000/videos/new) where you will see the video upload form. Choose a video (download this splendid [video of a sneezing panda](http://panda-test-harness-videos.s3.amazonaws.com/panda.mp4) if you need a test one) and hit submit. The video will be uploaded directly to Panda, and once complete the form will be submitted to your Rails app, with the upload's `panda_video_id` included.

You should now be looking at /videos/1, which will show the detail of the video object. The status should be "processing". Refresh this page and you will see it change to "success" when the video has finished encoding.

#### Create a player page

There are a few options for video playback. Read the [Video Players documentation](#player) for detailed information. The example below should be enough to get you started though using Html5.

If you don't have to support older browsers, the simplest is to use the new HTML5 `<video>` tag. To support Firefox you'll also need to add in an OGG source.

Edit your show page in `/app/views/videos/show.html.erb`:

```erb
<% if @video.panda.status == "success" %>
  <video id="movie" width="<%= @h264_encoding.width %>" height="<%= @h264_encoding.height %>" preload="none" 
    poster="<%= @h264_encoding.screenshots.first %>" controls>
    <source src="<%= @h264_encoding.url %>" type="video/mp4">
  </video>
<% end %>
```
<div style="clear: both;"></div>
If you refresh the show page you'll see the encoded video!

### Advanced topics

#### Encoding Profiles

You can change the formats that Panda encodes videos into by logging in to your [Panda account](https://app.pandastream.com/login). Each Cloud has its own set of Profiles. To learn more, see the sections on [Encoding Profiles](#presets).

It's possible to define several profiles and then decide on a per-video basis which ones to use. 
You can specify the list of profiles you would like your video to be encoded with while creating your upload session.

```ruby
upload = Panda.post('/videos/upload.json', {
  file_name: upload_payload['filename'],
  file_size: upload_payload['filesize'],
  profiles: "profile1, profile2",
})
  ```
<div style="clear: both;"></div>
### Learn more

Now that you have a basic implementation, you might want to check out the [Ruby on Rails examples](https://github.com/pandastream/panda_example_rails), [Panda Gem Documentation](https://github.com/pandastream/panda_gem/blob/master/README.md) or the full [API Documentation](/docs/api) for more ideas.

### Feedback

Whether you feel that this article is incorrect, outdated or missing information, don't hesitate to contact the [support](http:// https://support.pandastream.com.io/collection/37-panda).
