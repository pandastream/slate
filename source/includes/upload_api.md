##Upload API


Panda's Upload API supports 2 ways of uploading a file: resumable (also known as streaming) and multipart upload.
The first is a better choice when your environment allows you to do so.
The seconds is the standard method for web browsers to send large files. Recent browsers now supports the first method too.

Both methods are described in this document.



### Create an upload session

Before uploading a new file, you need to create an upload session. This is normally done by making an ajax call request from your javascript uploader (with the file size and name) to your backend. The ajax request should return the location for the upload.

```
->
POST /videos/upload.json
Host: api.pandastream.com
--
file_size     :     integer
file_name     :     string

<-
Status Code 201

"id"          :     integer
"location"    :     location to upload the video
```
<div style="clear: both;"></div>

At this point, Panda gives you a unique resource location to upload the file.

### File upload

##### Streaming

#### Protocol

```
->
PUT /<location>
"Content-Type: application/octet-stream"
"Content-Range: bytes RS-RE/L"                  [ where RS=range start, RE=range end, L=file_size ]
--

Body <BINARY>
```
<div style="clear: both;"></div>


If the file has been uploaded successfully, the original request is sent to the backend with the file and you receive the backend's response. `(R2=L-1)`

If the Content-Range is missing, the whole file is expected to be sent.

The method can also be POST, as long as the Content-Type is
application/octet-stream we can differentiate with the mulitpart upload.

```
<-
Status Code 200
--

Body <Video JSON>
```
<div style="clear: both;"></div>

If the file is incomplete, you get a response that lets you know the missing data `(R2<L-1)`:
  
```  
<-
Status Code 204
Range: RS-RE
--

Body <empty>
```
<div style="clear: both;"></div>

#### Example

1st chunk:

```
->
PUT <location/>
Content-Length: 5
Content-Range: bytes 0-4/9

abcde

<-
Status: 204
Range: 0-4

<empty>
```
<div style="clear: both;"></div>

2nd Chunk:

```
->
PUT <location/>
Content-Length: 4
Content-Range: bytes 5-8/9

fghi

<-
Status: 200

<{"id":"12345"...}>
```
<div style="clear: both;"></div>

#### Upload status

If the client connection broke, You need to query the server to know where it left the upload. In this case, send an empty request specifying <code>Content-Range: bytes *</code> and <code>Content-Lenght: 0</code> in the headers.

```
->
PUT /<location>
"Content-Range: bytes */L"                       [ where L=total file size ]
--

<-
Status Code 204
Range: RS-RE
--

Body <empty>
```
<div style="clear: both;"></div>
#### Abort the upload
```
->
DELETE /<location>

<-
Status Code 200
--

Body <empty>
```
<div style="clear: both;"></div>
### Multipart

  To POST data using an `HTML <form>` you will have to send the data within the parameter `"file"`.

```
->
POST /<location>
"Content-Type: multipart/form-data"; boundary=---- 
--

Body <BINARY>

<-
Status Code 200
--

Body <Video JSON>
```
<div style="clear: both;"></div>

#####Client notes

#### Error handling

The client is expected to retry requests that fail with a status of 5xx or weren't able to pass through at all. All <strong>4xx</strong> errors are errors in the client's implementation and should be treated accordingly.

#### Location handling

On each response you may receive a new Location that you need to follow in the subsequent requests. This is to leave us the flexibility of moving the upload to a different host. Please record the new location if it appears to have changed.

#### Content-Range

Here is the regexp to parse the range header: <code>"^(\\d+)-(\\d+)$"</code>
