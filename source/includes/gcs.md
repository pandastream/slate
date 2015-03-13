## Google Cloud Storage

To use Google Cloud Storage with Panda, you should sign up for a GCS account to allow Panda store the original and encoded video files.


### Create GCS account

1.  Go to [Google Developers Console](https://console.developers.google.com) and follow the sign up instructions. If you don’t have Google account, you will be prompted to create one.

2.  Once signed in create project that you wish to activate GCS for

3.  Enable Google Cloud Storage service</br>
    while in the project select `API’s & AUTH` in the left sidebar
    in the APIs list click button next to Google Cloud Storage to turn it on

4.  In the project Settings click `Enable billing` and you will be guided through the process

5.  To be able to create buckets in Panda you will need your Project Number, Key and Email address

6.  `Project Number` is displayed at the top of your project dashboard

7.  To obtain `Email address & Key`:</br>
    go to your project’s `APIS & AUTH` page and select `Credentials`  from the left menu
    click `Create new Client ID`, as Application type select Service account and click Create Client ID
    your Email address will be generated, together with pair of keys required co create cloud

You are now ready to create a [Panda Cloud](https://app.pandastream.com/#/new) with Google Cloud Storage!

<aside class="notice">
  <strong>Note:</strong>
   Panda will only use your credentials to let it's own user access the bucket and then forgets your credentials.
</aside>
