## Error classes and Error messages

Encoding and Video have 2 special attributes when their status is set to 'fail'

`error_class`: Helps you figure out what happened and what to do next.

`error_message`: Gives you a more descriptive message of the error

The following table list all possible error classes:

- `S3Error`

    Whenever the system try to access your bucket and gets an bad response from S3.

- `VideoStatusInvalid`

    The original video is in error state and can not be processed. Check the videos error to know the reason (only applied for encodings).

- `CommandInvalid`

    The command of your custom profile is not correct. You should take a look at your encoding logfile.

- `EncodingError`

    The encoding has failed. You should take a look at your encoding logfile.

- `HLSVariantsNotFound`

    No HLS variants were found when creating HLS variants playlist.

- `FileNotFound`

    Your original video file doesn't exist on S3.

- `DownloadFailed`

    Panda was not able to download the file from source_url.

- `UnexpectedError`

    Error is not expected from the system we will look at it.

- `FormatNotRecognised`

    Your file is not a video or audio file valid.

<div style="clear: both;"></div>
