---
layout: post
date: 2016-09-09 11:42 -0500
title: typos and sni
categories: 
---
SNI (Server Name Indictation) is supported by all modern web clients, and servers. I'm very familiar with how this works under apache. However, I recently added a second 'server' block to my nginx configuration on a vps, and was dismayed to find that the requests to the primary hostname were being picked up by this new domain's certificate. I removed the new server block, the old certificate showed up. I setup a default server block (not what I wanted) and that worked, allowing the new name through, but the original server block did not work. Thirty minutes of fiddling (was the leading dot not needed, I had ``server_name .sub.domain.tld;``` but all examples I saw had it without a leading (terminal?) dot. I removed that, nothing changed. When I finally found a character transposition it all made sense.

So, both apache and nginx, without a matching server name, will offer up the first found ssl certificate listening on that IP:Port combination. If you define a default block, that will be offered, and if not, it's based on the order of the inclusion in the configs. The new subdomain preceded alphabetically the hostname, and that led to my problem. Fixing the server name line solved everything, and resolved any doubts I had about nginx.conf and SNI.

