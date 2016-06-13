---
layout: post
title:  "Cups and Firewalld"
date:   2015-05-01 13:30:00
categories: labnotes
---

# Firewalld : a new beast

I repurposed a machine that had one visible service as a print server.
I used it for a myriad of other things, but to my wife, this was the
cupsd server for the house. I normally add the stupid hp plugin to get this
up and running. One thing that interfered when I tried to set this up on
Centos 7 in the lab was that the firewall prevented remote access to port 631
for management (I did open the listen address in the conf file for this),
and although it was broadcasting the printer on the network, the connections
to the printer were failing.

Right now, after some short troubleshooting, I disabled the firewall service
to get this to work

{% highlight bash %}
systemctl firewalld stop
systemctl firewalld disable
{% endhighlight %}

I did try the dumbest thing I could think of (`firewall-cmd --add-service ipp`),
and tried samba, ipp-client, and a few other items. It didn't seem to allow
connections to the printer. I'll need to revisit this.