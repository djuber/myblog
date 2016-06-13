---
layout: post
title:  "Taking the Plunge"
date:   2015-04-20 18:23:18
categories: rhcsa commentary
---

This week I decided the next steps in professional growth would be to study for, then certify as RHCSA and RHCE over the next year.
Part of this was driven by an honest interest in covering my weak spots, and some was prompted by the desire to improve my marketability.

I ordered three books (the RHEL 7 versions of many of the established study guides are on preorder, except
Ghori's `RHCSA & RHCE Red Hat Enterprise Linux 7: Training and Exam Preparation Guide (EX200 and EX300), Third Edition` .
The other two were Ghori's second edition, and Jang's RHEL 6 version of the certification study guide.
I also got hold of the Sander van Gudt video course. This gives at least three views of what I should be learning.
I think the critical part will be performing the labs.

Today, I took the plunge. I formatted my office machine as a Centos 7 server, for a kvm host.
The virt-manager tool in redhat is pretty lightweight compared to the VirtualBox interface, which I use
on my laptop, and had been using previously. If that works well, its support for online migrations
will make my second machine (currently a file/video server) an attractive target. My biggest regret is moving to
Gnome as a desktop, but I think some conversational fluency with that will be useful on the exam.

I created the first three dummy machines (a server, a client, and a third host outside the network).
Configuring routes for internal virtual bridge networks seems a little funny. Both networks can reach the internet,
but neither can see each other so far. I'll probably need to process this setup a little further.