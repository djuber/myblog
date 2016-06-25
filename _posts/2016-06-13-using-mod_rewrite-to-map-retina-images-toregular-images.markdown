---
layout: post
date: 2016-06-13 01:40 -0500
title: "Using mod_rewrite to map retina image requests to plain images"
category: apache mod_rewrite
---

Expensive phones will request double resolution images when they're available, and fallback to low res images when not. They will send a HEAD request first, then a GET if they get a 200 in the HEAD. Lets stop an expensive 404 from running on sites that have index.php as a fallback for all 404's, which can be really expensive:

{% highlight null %}
# strip retina requests if missing
RewriteCond  %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} ^(.*)(@2x\.\w+)$ [NC]
RewriteRule (.*)@2x\.(\w+) $1\.$2 [PT]
{% endhighlight %}

This needs to go above any redirects to the fallback resource.  If you have some of your files retina ready, you're set, this will not downgrade actual files, just shunt 404 requests to the ultimate location.
