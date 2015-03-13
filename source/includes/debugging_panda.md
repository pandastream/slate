#More

## Debugging Panda

If you are having trouble with Panda integration, those are a few things you can try out before getting in touch with support.

### Debug pannel

We provide you with a debug panel for your application which is located in the web app.
From here you can check the state of the  uploading / encoding / notification processes.

Debug data is not persistent, make sure you have the debug window open in a different tab or window while testing the workflow.

![Javascript debug console](/images/docs/app_debug_console.png)

### Turning on debugging in your JavaScript

To print the activity of the uploader, you can turn on debugging directly in your javascript console (webkit inspector or firebug). After loading the page, paste the following code:

```javascript
PandaUploader.log = function() {
  if (window.console && window.console.log) window.console.log(arguments);
};
```
<div style="clear: both;"></div>

This should create output like the following in your browser (Chrome in this example): 

![Javascript debug console](/images/docs/debug_console.png)

### Debugging the encoding

There is a couple of reasons why an encoding can fail. To know more about it, use the api and check the error_message/error_class attribute of the video and encoding.

Also you will find a log file in your bucket which tells why the encoding has failed (_ENCODING_ID_.log).

### FAQ

Please read the [FAQ](faq) before getting in touch with support.

### Where next?

If none of these techniques help get in touch with [support](https://support.pandastream.com) and we'll check whether there is a problem we can help you with. 

Please make sure the problem hasn't been already solved by the support team before creating a new ticket.

Please make sure you can provide us a way to find your account, as a Cloud ID or an Encoding ID.
