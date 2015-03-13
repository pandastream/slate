## Widget

### Panda Web Widget

Panda Web Widget allows you to easily monitor your cloud encoding activity and share this information on your website.

<br/>
<img src="/images/docs/widget_waiting_for_data.png" alt="Panda Web Widget" style="width: 180px;" />
<img src="/images/docs/widget_ongoing.png" alt="Panda Web Widget" style="width: 180px;" />
<img src="/images/docs/widget_not_encoding.png" alt="Panda Web Widget" style="width: 180px;" />

<br/>
Data displayed includes original name of the uploaded file, encoding profile name, encoding progress and number of jobs left in encoding queue.

<aside class="notice">
  <strong>Note:</strong>
  Panda Web Widget works in 'receiving mode' only. It relies solely on data it receives from PandaStream. Right after it's loaded, it will remain in 'Waiting for data' state untill it receives encoding activity update.
</aside>

### Configuration

To get the widget working, at first you need to enable it in your cloud settings. Once you set **Enable Web Widget** to **Yes**, you will be presented with a small code snippet.

<br/>

<img src="/images/docs/widget_cloud_settings.png" alt="Panda Web Widget" style="width: 600px;"/>

<br/>
Copy this code and pase it into your html body. Please also remember to click **Save Changes** at the bottom of the Cloud Settings page.

If at any time you decide that you no longer want PandaStream to sent out widget data, change Enable Web Widget back to No.
