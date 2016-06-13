---
layout: post
title:  "Welcome to your new site"
date:   2016-06-12 23:59 -0500
categories: commentary
---

So today I finished the "introduction to ruby on rails" course at coursera. I think I had a lot of problems with the first http client code, so
what might have been a one week item turned into a three week course. Hard to get into the fun times of
troubleshooting failing tests in a language you don't understand. Once I got the recipe search app running, I was elated. While it was
hard to sell my wife on the magic of a "recipe search application", to which she queried "Doesn't google do that?", it was
really satisfying to build it. It was satisfying to get the grader program to handle the submission, spit out the pass line.

It was not satisfying to be prompted to pay $79 for the certificate of completion, but some day it might make sense to me. I have
too little belief in formal education, and certification in particular, to want to pay for it. It was fun when I passed the cryptography course
with Dan Boneh from Stanford to get a badge on linked in. I was shocked to have passed that (it was just a fraction above 70%, the cutoff).

This was the first distance ed course that I'd completed without a pair, so that was a personal hurdle overcome.

This page is being written in jekyll on my laptop. Study.beta-reduction.com, the predecessor site, was edited in a terminal window in emacs.
I got a new laptop (a refurbished [thinkapd t420](https://www.amazon.com/Lenovo-Thinkpad-T420-Certified-Refurbished/dp/B00W4AMWCI/ref=sr_1_3?ie=UTF8&qid=1465794935&sr=8-3&keywords=thinkpad+t420) ) and was able to go back to using a real OS. Mac OS was fine for gui apps, but the dissonance in the unix layer between GNU/Linux
and OSX was painful. The last straw was my last OS Upgrade. Needing to add credit card details to the itunes music store to complete a free download of the OS, and more
importantly needing to [codesign gdb](http://ntraft.com/installing-gdb-on-os-x-mavericks/) (which then was limited by the move more and more to llvm in place of gcc) was raising my irritation levels.
The Mrs also regularly borrowed it, so installing some wild linux boot installation would be like locking her out.

I've installed this as an ssl vhost on nginx on my vps, I'd like to do more with that. This is using letsencrypt manually. In this case I've let cPanel know about the docroot, my
other docroots were set to /var/www/domainname/, but I created a user for this site. That should allow my automatic scripts, which assume cpanel
knows the location to the document root, to work. This also allows easy rsync from my laptop to the live site.