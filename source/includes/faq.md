## Frequently Asked Questions

##### Does Panda save the original video?
In order for you to create new encodings when a new format comes in, we save your original video in your s3 bucket.

##### Does Panda support audio encoding?
Yes we provide several audio presets or you can check the [Advanced profiles](#encoding_profiles) section and create your own profiles.

##### Does Panda support multi-pass encoding?
Yes 2 pass encoding is supported for presets and custom profiles.

##### I'm using a custom profile and my videos are not scaled
Make sure that your command include the `$filters$` variable

##### Panda gem returns a Hash instead of a JSON string like before
Since Panda 0.6 the default returned value is a Hash but it's possible to get a JSON string back.
Check the panda\_gem's README file in <http://github.com/pandastream/panda_gem>

##### Flash is crashing before or after selecting a file.
Please can you check first if it crashes also with swfupload examples (http://demo.swfupload.org/v220/simpledemo/index.php).

If it does not work get in touch with [support](http://help.copper.io/collection/37-panda).

##### All my recent uploads are failing now. What happen.
You should first check that Panda has still permissions to read and write files to your bucket, using Amazon S3 console.
You can grant access to Panda again by editing your cloud in the web app.

If yes, please get in touch with the [support](http://help.copper.io/collection/37-panda).

If not, use the edit cloud link inside your account, re-type your bucket name and re-enter your amazon credentials.

##### Video resource has status fail. What does this mean?

The status attribute of a video resource is the result of the following process:

* Getting your original video from the source and uploading it to your S3 bucket before encoding it.
* No encoding task has been performed yet.

A failure can be the result of 2 problems:

* You are using the source_url with a free sandbox account and the video is bigger than 10MB.

  Send a smaller video or upgrade your account.

* You are using the source_url and the video is not reachable by Panda. 

  Make sure your video can be downloaded from outside and that you are not referring to localhost.

* Panda has your video but can't upload it to your s3 bucket. 

  This problem means Panda doesn't have access to your bucket anymore. 
  You can check the permission of your bucket and see if `pandastream` has read and write for both files and ACL.
  You can grant access to Panda again by editing your cloud in the web app.
  
Check attributes `error_class` and `error_message` of the response

##### Encoding Resource has status fail. What does that mean?

The status attribute of an encoding resource is the result of the following process:

* Getting your original video from your s3 bucket, selecting the profile to be encoded with and encoding the video.

If you have a failing encoding, make sure: 
* the original video is reachable.
* the profile the encoding is using still exists

Check attributes `error_class` and `error_message` of the response
  
If the steps above are correct, then you should check the log file uploaded to your s3 bucket and see why the command has failed.

If you think your video should have been encoded correctly, please get in touch with [support](http://help.copper.io/collection/37-panda) and give us the ID of this encoding.

##### I can't find the encoding log file in my bucket
It means that your original video has been successfully uploaded to your bucket.

Check first the status of your video by doing `GET /videos/:id.json`. If the status is `fail` then look at the "Video Resource has status fail" section

##### I'm getting an error code 404. What does this mean?
404 means Resource not found. You are trying to access to a resource which doesn't exist.
It can be a video, and encoding, a profile, a preset or trying to connect with a wrong Cloud ID.

If you are posting a video, make sure you are not specifying a wrong profile Id

##### I'm getting an error code 417. What does this mean?
It means you are maybe using the flash uploader behind a proxy server.

##### I'm getting an error code 413. What does this mean?
It means you have reached the upload file size limit. If you are on free plan, make sure you choose a file which doesn't exceed 10MB or upgrade your account to remove this limit.

##### I'm getting an error code 401. What does this mean?
It means you sent a Bad request. At least one required parameters is missing. Check The [API section](#api) and look at the required parameters. This is likely to be caused by the signature not being generated correctly.

##### I'm getting an error code 401 and I have an EU account using panda uploader. What does that mean?
EU and US use difference hosts. You should specify `api_host : "api-eu.pandastream.com"`. 
Check the panda\_uploader's [Documentation](#uploader)

##### Panda uploads temporary files in my bucket. How can I avoid that?
Panda will upload all files having a filename starting with the Encoding ID. So make sure you give to your temporary file a name which doesn't start with $record\_id$.
