---
layout: post
title: "WeblogNG at DCC14 - Follow-up"
description: "WeblogNG at DCC14 - Follow-up"
category: presentations
tags: [html5, performance]
---
{% include JB/setup %}

We'd like to offer a big "thank you" to everyone who attended <a href="http://apr2014.desertcodecamp.com/session/999">Using HTML5 Navigation and Resource Timing APIs to Understand Client User Experience</a> at the April 2014 Desert Code Camp.

We worked through the material and covered many good questions from the crowd.  The WeblogNG team found the variance in performance for the clients participating in the resource timing demo very interesting.  We were all in the same room and sharing the same network and still observed differences in page load time of more than
25% -- even with a relatively lightweight page.

If you'd like to experiment with or examine the test-app more closely, you can find it at:

* the [client](http://resource-timing-demo.herokuapp.com/) and [ops center](http://resource-timing-demo.herokuapp.com/display) page on Heroku
* the [resource timing demo source code](https://bitbucket.org/beardedrobotllc/resource-timing-demo) on Bitbucket

Additionally, here are the client-performance resources we looked-at during the presentation:

* [HTML5 Navigation Timing Spec](www.w3.org/TR/navigation-timing)
* [HTML5 Resource Timing Spec](www.w3.org/TR/resource-timing)
* Steve Souders' [Rules for High-Performance Websites](stevesouders.com/hpws/rules.php)
* [Igor Grigorik's blog](http://www.igvita.com/)
* Google's [Make the Web Faster](https://developers.google.com/speed/) developer site

If you are planning to make use of the Navigation and/or Resource Timing APIs, we'd love to hear from you via a tweet to @weblogng.  If you are
looking-for an easy way to aggregate and visualize this data, then let us know because WeblogNG can help with that, too.