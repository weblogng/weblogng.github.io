---
layout: post
title: "WeblogNG Javascript client library 0.6.0 with improved network efficiency"
description: "WeblogNG Javascript client library 0.6.0 with improved network efficiency"
category: performance
tags: [javascript, performance, monitoring, release]
---
{% include JB/setup %}

We are excited to announce the release of [WeblogNG Javascript Client Library](https://github.com/weblogng/weblogng-client-javascript) version 0.6.0 which logs measurements more-efficiently by using the new batch-enabled WeblogNG logging api.

The existing public API of the Javascript library is unchanged by the 0.6.0 release, so existing integration works without changes.

The Javacript client library now:

1. buffers individual measurements made by client code
2. flushes the buffer of measurements in a batch to the WeblogNG api once very 5 seconds, at most
3. connects to the WeblogNG api via https instead of websockets

The new integration approach should enable the library to make more-efficient use of network and battery resources across a wider range of browsers.  More-advanced options for controlling the flush of a batch of measurements are under consideration.  We'd love to hear what you think is the best approach for your application via an issue on [github](https://github.com/weblogng/weblogng-client-javascript) or a tweet to [@weblogng](https://twitter.com/weblogng).

Details on integrating and using the library are available in the [Javascript client library documentation](http://docs.weblogng.com/en/latest/client-library-javascript.html).