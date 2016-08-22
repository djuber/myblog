---
layout: post
date: 2016-08-21 22:03 -0500
title: Mail Overflow
categories: 
---

Two weeks ago, contemplating moving mail off of my work vps onto a separate and underused machine, I attempted to install [iredmail](https://iredmail.org/). When I realized the automatic installer was going to have a hard time with the prior existence of nginx, I let this fall by the wayside. However, it looks like I should have made more immediate decisions.

Fast forward to this weekend. I logged in via ssh, and was surprised to see this dormant machine with both higher than anticipated load average, and higher than expected disk use. It looks like postfix was installed, but the site name and outbound routes were not configured effectively, and the mail was building up. What's worse, clamscan and amavisd were going full blast scanning, the target being tens of thousands of messages from [lfd](https://configserver.com/cp/csf.html) that were announcing the unexpected presence of long running processes, like these clamscans.

While there's a way to have resolved all of this, it seemed easier, given that the attempt had reached a halt, to uninstall the more active portions of the iredmail bundle, to uninstall and purge the postfix queue, and to remove the mail logs.

