---
layout: post
date: 2018-08-12 19:55 -0500
title: refactoring > rewriting
categories:
---
For about four years I was afraid. I was worried enough about change being difficult that I contemplated moving the technology rather than cleaning up the mess.

I was changing prod and copying back to the source code repo.
That repo was in mercurial because in 2014 that wasn't yet a horrible choice. Everything else I use is in git and this means just trying to commit and merge this project takes extra mental overhead.

I have 1100+ days of uptime on a wheezy server that I'm not sure will reboot cleanly. I'm running production from a sqlite file since I got weird errors (probably 100% tied to passwords and auth, or migrations) when running on a real database server. I am nervous about migrations so I try not to change existing tables out of fear of hard to diagnose errors.

I suspect 40% of the code is not used (abandoned spikes left in prod) or wouldn't work if it ran. It's hard to tell what, since there are no tests. I'm not sure how to correctly test a system like this without resorting to a mix of selenium and post-effect verification.

The environment doesn't separate development from production effectively, so the development tool and the production system both issue error tickets pointing to the backend admin, rather than pouring a stacktrace in front of the developer. This makes troubleshooting a bit harder, but at least prepares you to troubleshoot in prod (since that's all you've been doing).

I have api and password secrets in source code. I have long constant strings (email templates) in the controller. I have a "scheduler" via a "secret" endpoint that only accepts requests from localhost and launches a job, which is called by cron running wget.

Today I started shaving it. I made classes, which is pretty easy. I used DI not to permit testing but to permit moving the code out of a magic hosted context and into plain files (the fastest way to move controller code out of the controller is to pass the request object to the class).

I still have some gnarly form generation code using 2-3 levels of closures for no apparent reason apart from keeping the controller namespace clean - that's next.

I'm feeling surprisingly good. I think I'll knock out a few features tomorrow and stop complaining about having to write in python.
