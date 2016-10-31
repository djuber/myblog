---
layout: post
date: 2016-10-30 21:57 -0500
title: mysql deep dive, revisted
categories: 
---

I cloned the mariadb repo today. It's big (the repo for 10.2 was about 950MB with full revision history). I think I want to get my mind wrapped around the bigger parts first. I'll start with MyISAM and Aria. I assume that if I don't already have a good grasp on the details of any storage engine, starting with the simpler one might make sense.

The first place I started was `storage/myisam/NEWS`, where some key departures from NISAM (where does this word exist apart from here?), and I think that comparing reading that file with the structures listed at [mysql.com](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html) is a good start to get the main 'header level' details.

There seem to be several grouped files in the myisam directory,
those with ft prefixing apply to the full-text index feature,
ha is presumably high availability, but looking into the code, it might be 'handler' or similar,
mi is MyISAM core functionality,
rt is for RTree keys,
and sp is for spatial.

The core myisam.h file is outside this directory, in the main include directory, and should
also be considered part of the table code. The ha files seem to have the important (for a user) definition of the various state conditions during repair (if you take definition as "when the code is here at X, it sets state to Y"). The changelog for this code points  back to 1999, so the myisam changes are either old, or per-engine changelogs aren't being actively maintained. I'm still a little confused about the HA designation there.