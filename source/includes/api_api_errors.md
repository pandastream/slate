## API Errors

When there is an issue with a request, Panda will return the appropriate HTTP status code, along with a JSON object containing the `error` name and a `message`. (500 status returns an empty body). For more information on HTTP status codes, see `https://en.wikipedia.org/wiki/List_of_HTTP_status_codes`

<div style="clear: both;"></div>

### 500 InternalError

When there is an internal error a 500 status will be returned along with additional information in the message. Whenever a 500 error occurs our technical team is notified of the issue and will investigate the root of the problem immediately. If your experience a recurring issue, please submit a support ticket to [support.pandastream.com](https://support.pandastream.com)

<div style="clear: both;"></div>

> Example response

```
status: 401
```
```json
{
  "error":"NotAuthorized",
  "message":"Signatures do not match"
}
```

```
status: 401
```
```json
{
  "error":"NotAuthorized",
  "message":"Signatures expired"
}
```

### 401 NotAuthorized

When the `signature` parameter of a request is not correct, the server returns a status code 401 and a descriptive message. If you receive this error please ensure that you are constructing and encoding the `signature` parameter as described in the API Authentication section below.

If you receive a **Signatures expired** message, that means your clock is out of sync. You can maintain the system time in synchronization with time servers using `ntpd`.

<div style="clear: both;"></div>

> Example response (example)

```
status: 404
```
```json
{
  "error":"RecordNotFound",
  "message":"Couldn't find Video with ID=X"
}
```

### 404 RecordNotFound

A record with the `id` supplied could not be found.

<div style="clear: both;"></div>

> Example response

```
status: 400
```
```json
{
  "error":"BadRequest",
  "message":"Currently only .json is supported as a format"
}
```

```
status: 400
```
```json
{
  "error":"BadRequest",
  "message":"All required parameters were not supplied: access_key, signature, timestamp"
}
```

#### 400 BadRequest

This error will be returned in two cases. Either because you have requested a response format that is not supported (currently only JSON is supported, so all urls must end in `.json`), or you have not submitted all of the required parameters to a method.

<div style="clear: both;"></div>

> Example response

```
status: 413
```
```json
{
  "error":"FileSizeLimitExceeded",
  "message":"File size limit for this account is set to 10485760 bytes"
}
```

#### 413 FileSizeLimitExceeded

If you attempt to upload a file > 10MB with a sandbox account, the following error will be returned.

<div style="clear: both;"></div>
