---
layout: post
title: "Post-Startup War Stories"
date: 2018-02-07 08:00:00 -0600
categories: 
---

_It hopefully goes without saying that any of this information is sufficiently old, and represents my own views and opinions, not those of NetApp or SolidFire._

I joined SolidFire in May of 2014 as a software engineer, and employee #150,  with a "focus" on full-stack web development. After interviewing about the opportunities involved in the development of a new product, Active IQ, which represented an evolution of the creatively named "Remote Support" tool, I was put on a team focused on rewriting an internal test runner from PHP to Scala. I quickly learned that SolidFire spent very little effort naming their internal tools.

I certainly understood that SolidFire was no longer in the dynamic startup phase by the time I started. That certainly didn't mean the crufty tech debt accumulated during that phase had been undone. Coming from an established (read: 20 year old) company that had gone through an acquisition by Lexmark (the first in a long, painful line), juggling the kinds of tradeoffs made in this early phase of the company was a new experience. It's worth skipping over my year working on the internal test tool, most because the challenges with that project were primarily non-technical. Instead, I want to review the various technical challenges faced on the Active IQ project, starting shortly before I moved over to help that team out.

At the time I started focusing on Active IQ, the layout of the project was:

* RabbitMQ 3.5 as a messaging system
* MySQL 5.5 (Percona, specifically) for "relational" data
* MongoDB 3.0 for "non-relational" and timeseries data
* MapR Hadoop to waste our time and, as a distant secondary concern, store data long term and roll-up timeseries data
* Various other systems I would soon discover
* Services, written in Scala, which formed the core of the application:
  1. Receiver for telemetry data from SolidFire clusters 
  2. Consumer to (re)process data from RabbitMQ into MySQL/MongoDB
  3. API to drive the UI, using JSON-RPC for consistency with the SolidFire storage cluster API
* UI that was copy/pasted among several other tools, which used jQuery and a customized template engine to define pages around JSON-RPC APIs. Everything was a table.

This seemed like an interesting playground to start understanding multi-tenant software-as-a-service application development and architecture. Unfortunately, there were some pressing challenges around this system:

* MongoDB was a very recent addition, resulting from a meltdown of Couchbase in the months just before I switched teams
* Alerting, a feature dependended on by Support, had been rewritten and was "ready to release" to production, with proportionate pressure from Management and Support
* Follow-on features for alerting were "fully defined and ready to start development" which management and Support were also clamoring for
* Scalability concerns in systems that had not (yet) fallen over were already starting to show
* A senior engineer on the project had recently resigned when the prospect of on-call rotations came up
* The former technical lead abruptly switched to a different project

Obviously, this meant there was a lot of fun to be had, plenty to learn, and a good opportunity to go along with these things which could, with effort, be interpretted as warning signs.

I'm going to try to write up what I can remember about a few key events from the transition of this system from a pre-release tool into a highly-available tool critical to Support and widely used by customers and various internal groups.

1. The saga of Alerts
2. MongoDB bites back
3. That chart took how long to load?
