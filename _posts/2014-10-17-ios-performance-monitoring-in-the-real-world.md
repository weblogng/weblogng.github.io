---
layout: post
title: iOS Performance Monitoring in the Real World
description: "iOS Performance Monitoring in the Real World using WeblogNG"
category: performance
tags: [iOS, performance, monitoring]
---
{% include JB/setup %}

## Why monitor peformance? ##
Monitoring the performance of your native iOS application in the real world is important because application performance
and responsiveness is a key factor in whether a user of your application continues to use your application.

After all, one of the main reasons developers _build_ and customers _use_ native applications on iOS is
that they have the potential to be faster and more responsive then mobile web alternatives.  When a native app
fails to meet those expectations, users may be doubly-disappointed.

Oftentimes, applications are only tested in environments that turn-out to be best case scenarios for mobile performance: 
a simulator running on a powerful machine with a fast, reliable network.  There may even be a unicorn delivering Red Bull
to the developers.  In any event, these are not real-world conditions with:

* high latency, congested network
* less powerful CPUs, less memory -- definitely less powerful than your new MacBook Pro!
* significant application state

## Let's Get Started! ##

There are more than 1.3 million apps in the App Store and the competition in your space may be fierce.  It is best to start
with a good first impression.

Q. How fast _should_ a mobile app launch?

A. *2 seconds* -- according to a [study by Compuware](http://techcrunch.com/2013/03/12/users-have-low-tolerance-for-buggy-apps-only-16-will-try-a-failing-app-more-than-twice/) which found that smartphone users expect applications to launch in an median of two seconds.

Q. How fast does _your_ application launch in a real user's hands?

Let's quickly-walk through instrumenting the WNGTwitter sample application's launch time.  WNGTwitter is a Swift application, so we'll need to create a main.swift file in order to hook-into the beginning of the application lifecycle and record the launch:

	import Foundation
	import UIKit

	WNGLogger.initSharedLogger("your WeblogNG api key")
	WNGLogger.sharedLogger().recordStart("application-launch")

	UIApplicationMain(C_ARGC, C_ARGV, 
		NSStringFromClass(UIApplication), NSStringFromClass(AppDelegate))

Next, in the AppDelegate, record that application launch has finished:

	class AppDelegate: UIResponder, UIApplicationDelegate {

	    var window: UIWindow?

	    func application(application: UIApplication, 
	    	didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {

	        NSLog("AppDelegate:application:didFinishLaunchingWithOptions")

	        WNGLogger.sharedLogger().recordFinishAndSendMetric("application-launch")
	        
	        return true
	    }

	    // other methods ommitted ...
	}

If we put the 'application-launch' metric onto a WeblogNG chart, we can see how quickly the app launches:

![Application Launch Chart](/assets/images/posts/ios-performance-monitoring-at-scale/WNGTwitter-application-launch-chart.png "Application Launch Time Chart")

Awesome!  ~150ms is good launch performance, and we can monitor these charts over time to see how application updates, iOS updates, and other factors affect our application to ensure it stays that way.

## The Network is Your Frenemy ##

Networks provide the great content and services applications need, but are also a source of latency and failure that can be difficult to understand and debug.  The [8 Fallacies of Distributed Computing (pdf)](http://www.rgoarchitects.com/Files/fallacies.pdf) are recommended as a gentle introduction into the ways network operations are different from local operations, but we'll focus on the biggest problems for mobile applications here:

* The network is reliable
* Latency is zero
* Bandwidth is infinite

Ilya Grigorik has a done a great job explaining how WiFi, 3G, and 4G networks work in his [Mobile Performance from the Radio Up: 4G Architecture and its Implications](http://lanyrd.com/2013/velocity/sckgrc/) presentation.  In particular, Ilya has investigated and detailed how and why mobile network latency is different from wired network latency.  These differences are key to understanding why WiFi at a busy coffee shop can be so frustrating and may make you leap for joy when a [3G network works at all](https://www.youtube.com/watch?v=a4SbDZ9Y-I4#t=928).  Network latency numbers quoted below are from the presentation.

Perhaps the most important point for this discussion is the latency involved in merely connecting to the radio base station so the first packet can be sent:

* WiFi: 5 - 100 ms, congestion-dependent
* 3-3.5G: 200 - _2500_ ms
* 4G: < 100 ms
* 4G LTE: < 100 ms

This base station connection latency _does not exist_ on wired networks as the wire maintains the connection, so this latency will not be observed unless using the application is used on a mobile network or the latency is injected through artificial means.

Once the mobile device has connected to the base station and is 'allowed to speak', the packet latency is much better. However, it is still an order of magnitude larger than a wire:

* WiFi: < 5 - 100 ms, congestion-dependent
* 3-3.5G: < 50 ms
* 4G (HSPA+): < 10 ms
* 4G LTE: < 5 ms

Exapnding the view to examine a full http request rather than just the first hop, even the best-case scenario of 4G LTE looks surprisingly-high, but this is due to the embedded radio->base station packet latency:

* connect to base station: 50 - 100 ms
* DNS lookup: 80 ms
* TCP connection: 80 ms
* TLS handshake: 80-160 ms
* HTTP request: 80 ms

*Total network latency overhead*: 240 - 500 ms

Now that we know what we're up against, let's press-forward and make some network requests...

Q. How long do _your_ app's requests really take?

Many developers don't have a good idea of how long their app's requests take in the real world.

Fortunately, WeblogNG can help by automatically timing all http requests made with NSURLConnection.  This feature can be enabled by calling `NSURLConnection.wng_setLogging(true)`.  Here's the main.swift code from above with the necessary update:

	import Foundation
	import UIKit

	WNGLogger.initSharedLogger("your WeblogNG api key")
	WNGLogger.sharedLogger().recordStart("application-launch")
	NSURLConnection.wng_setLogging(true) //enable automatic timing of NSURLRequests

	UIApplicationMain(C_ARGC, C_ARGV, 
		NSStringFromClass(UIApplication), NSStringFromClass(AppDelegate))

boom!  real-life measurements of http traffic generated by real usage!

WeblogNG automatically generates metric names of the form [host]-[http method], which we can then include in a chart:

![Twitter API Chart](/assets/images/posts/ios-performance-monitoring-at-scale/WNGTwitter-twitter-api-chart.png "Twitter API Chart")

Simple!  Can DevOps get any easier?

Notice the divergence between the median (green) and 95th-percentile (red) lines.  The request times depicted in this chart were all made by the same application running over the same mobile device while on a 3G network connection.  The intermittent divergence is the base station connection latency at work!

Now that we have visibility into real-life application usage, we can manage the application's performance in a pro-active way by:

* designing the user experience to manage request latency expectations, definitely avoiding promises that network operations complete immediately
* reducing the total number of requests, perhaps by batching them together
* minimizing both request and response data 

If you would like this level of visibility into your iOS application's performance, then [sign-up for WeblogNG](https://www.weblogng.com/) today.

