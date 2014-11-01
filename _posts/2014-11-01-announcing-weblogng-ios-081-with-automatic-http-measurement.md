---
layout: post
title: "WeblogNG iOS client library 0.8.1 with automatic HTTP request measurement"
description: "Announcing WeblogNG iOS library 0.8.1 with automatic HTTP request measurement"
category: performance
tags: [iOS, performance, monitoring]
---
{% include JB/setup %}

We are excited to announce the release of [WeblogNG iOS Client Library](https://github.com/weblogng/weblogng-client-iOS) version 0.8.1 with support for automatic measurement and logging of HTTP requests performed via NSURLConnection.

With the 0.8.1 release, measuring the performance of HTTP requests made by an iOS app implemented in either Objective-C or Swift is as easy as:

1. integrate the WNGLogger library using CocoaPods, as usual
2. import the WNGLogging category for NSURLConnection via `#import <WNGLogger/NSURLConnection+WNGLogging.h>`
3. invoke `NSURLConnection +wng_enableLogging`

### Objective-C example ###
The [application delegate](https://github.com/weblogng/weblogng-client-iOS-sample-app/blob/master/sample-app/WNGAppDelegate.m) for an Objective-C app might look like:

{% gist skuenzli/4c9dbc4779a71a4d3145 %}

### Swift example ###

The [main.swift](https://github.com/weblogng/example-weblogng-WNGTwitter/blob/master/WNGTwitter/main.swift) file for a Swift app might look like:

{% gist skuenzli/944c9691dca8910c0b03 %}

### Resources ###

The [iOS Client Library documentation](http://docs.weblogng.com/en/latest/client-library-ios.html) has more:

* details on integrating and using the [iOS Client Library](https://github.com/weblogng/weblogng-client-iOS)
* examples of common usage
* how the new measurement feature works

For more details on *why* measuring http request performance is important, please check-out [iOS Performance Monitoring in the Real World]({% post_url 2014-10-17-ios-performance-monitoring-in-the-real-world %}).