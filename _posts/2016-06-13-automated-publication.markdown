---
layout: post
title:  "Automated Publication"
date:   2016-06-13 00:35 -0500
categories:  labnotes 
---

So I wrote a basic script to push this to the remote. This script is ~/bin/publish, it runs jekyll build and git push in my site's sources. You can see the remote at [github](https://github.com/djuber/myblog/). I had though incremental was what I wanted, however experimenting showed there were issues when adding new content. 

{% highlight bash %}
cd ~/src/myblog
git add .
git commit -m "automated $(date +%Y-%m-%d-%T)"
jekyll build # --incremental
rsync -Pauv _site/ :/home/blog/public_html/ -e "ssh blog@djuber.tk -p 22222"
{% endhighlight %}