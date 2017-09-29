---
layout: post
date: 2017-09-28 22:46 -0500
title: Pipes and Sockets
categories: emacs
---

Fun times, this is what it looks like when slime and cider are both connecting to emacs. For an org-mode session, of course. I still find writing common lisp a lot easier than clojure, and for things like large numbers, I don't need to learn where java squirrelled away the bignums.

{% highlight raw %}
tcp        0      0 127.0.0.1:53652         127.0.0.1:33233         ESTABLISHED 8920/emacs
tcp        0      0 127.0.0.1:33233         127.0.0.1:53652         ESTABLISHED 27488/sbcl
tcp6       0      0 ::1:38413               ::1:33164               ESTABLISHED 25507/java
tcp6       0      0 ::1:33164               ::1:38413               ESTABLISHED 8920/emacs
{% endhighlight raw %}

On the other hand, I do like that cider prefered ipv6 if available, and that emacs could just handle it.
