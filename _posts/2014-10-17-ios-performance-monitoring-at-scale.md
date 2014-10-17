---
layout: post
title: "iOS Performance Monitoring in the Real World"
description: ""
category: performance
tags: iOS performance monitoring
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

* slow, unreliable network
* less powerful CPUs, less memory -- definitely less powerful than your new MacBook Pro!
* significant application state

## Let's Get Started! ##

There are more than 1.3 million apps in the App Store and the competition in your space may be fierce.  It is best to start
with a good first impression.

How fast should a mobile app launch?

```2 seconds```

A [study by Compuware published on TechCrunch](http://techcrunch.com/2013/03/12/users-have-low-tolerance-for-buggy-apps-only-16-will-try-a-failing-app-more-than-twice/) found that iPhone users expect applications to launch in an median of two seconds.

How fast does your application launch in a real user's hands?

Let's quickly-walk through instrumenting the WNGTwitter sample application's launch time.  WNGTwitter is a Swift application, so we'll need to create a main.swift file in order to hook-into the beginning of the application lifecycle:

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

Networks provide both the great content and services applications need, but is also a source of latency and failure that can be difficult to understand and debug.  The [8 Fallacies of Distributed Computing (pdf)](http://www.rgoarchitects.com/Files/fallacies.pdf) are recommended as a gentle introduction into the ways network operations are different from local operations, but we'll focus on the biggest problems for mobile applications here:

* The network is reliable.
* Latency is zero.
* Bandwidth is infinite.

Ilya Grigorik's explanation of [how WiFi, 3G, and 4G networks work](http://lanyrd.com/2013/velocity/sckgrc/) may make you leap for joy when a 3G network works at all and shed some light on why WiFi at a busy coffee shop can be so frustrating.  As a teaser, Ilya found that the 95th-percentile latency of a ping from his laptop to the router covering a distance of only 15 feet was 53-90 milliseconds, depending on the WiFi channel being used.  This latency is higher than the typical coat-to-coast latency to cross the United States of ~50ms!

But assuming we'd like to press-forward and make some network requests...

How long do your requests really take?

