---
layout: post
title:  "Automated Publication"
date:   2016-06-13 00:35 -0500
categories:  labnotes 
---

So I wrote a basic script to push this to the remote. 

{% highlight bash %}

cd ~/src/myblog
git add .
git commit -m "automated $(date +%Y-%m-%d-%T)"
jekyll build --incremental
rsync -Pauv _site/ :/home/blog/public_html/ -e "ssh blog@djuber.tk -p 22222"

{% endhighlight %}