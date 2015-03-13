---
title: API Reference

language_tabs:
  - shell
  - ruby
  - python

toc_footers:
  - Copyright © 2011-15 PandaStream
  - All rights reserved

includes:
 - faq
 - client_libraries
 - api
 - api_base_url
 - api_videos
 - api_encodings
 - s3
 - gcs
 - rackspace
 - ftp
 - create_an_iam_user_for_panda
 - integrate_with_rails
 - integrate_with_php
 - uploader
 - upload_api
 - notifications
 - video_players
 - video_streaming
 - widget
 - presets
 - encoding_profiles
 - debugging_panda
 
search: true
---


# General

## Getting started with Panda

At the core of Panda is a REST API which supports uploading and managing of videos, encodings and output profiles.

Every Panda account has a number of __clouds__. Each cloud defines a single storage for your uploaded videos, resulting encodings and thumbnails.

Typically you will want to create a separate cloud for each website you plan to integrate Panda into. You can also use clouds to separate production and staging environments.

To access the API there are client libraries available in many languages: [See all client libraries](#libraries). Refer to the [API Docs](#api) when using the API. All API responses are JSON-formatted.

The following guides are also available: [Rails How-to](#rails) and [PHP How-to](#php).

![Javascript Uploader](/images/docs/uploader_preview.png)

Panda provides two ways of sending video files in for transcoding. The first is by using the url of a video anywhere on the web (see [API Docs](#api) for details). The other method is by using our excellent [Javascript uploader](#uploader) which supports seamless HTML5 and Flash uploads, automatically detected depending on the client's browser.

To configure the encoding output formats, refer to the [Encoding Presets documentation](#presets).

### Feedback

Whether you feel that this article is incorrect, outdated or missing information, don't hesitate to contact the [support](http://support.pandastream.com).


