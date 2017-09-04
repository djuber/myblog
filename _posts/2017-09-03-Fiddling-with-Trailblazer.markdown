---
layout: post
date: 2017-09-03 22:59 -0500
title: Fiddling with Trailblazer
categories: 
---

This weekend I finally started trying to use [Trailblazer](http://trailblazer.to/), a ruby
framework for architecting applications in terms of the domain concepts,
and not building them based on the needs of the webserver.

I had in the past tried to do this, either gleaning what I could from the [Guides](http://trailblazer.to/guides/) or from the [book](https://leanpub.com/trailblazer), but the book details a version
that no longer compiles on debian stretch, as certain openssl deprecations broke all older versions of ruby without some fiddling. Rather than work around the technological problems of dated api
documentation, this weekend I dove in.

This is maybe the first time I read the example application documentation, while building an
analogous program on another domain. I enjoyed that a lot, reimplementing forces you
to get comfortable both with the meaning and structure of the tool, where copying example
code only builds finger memory and takes you on the tour.

By "building" and "fiddling around", I mean to say that I got most of the model Create
operation classes built, some reform validations for those, and as an aid in writing the specs,
I think I accidentally created a set of Factory classes for these. Having seen FactoryGirl, I think
I knew what it should look like, but while it's functional, I'm sure it handles about as well as a
25 line tdd library that you sometimes see in common lisp as a dare or a hack. But I had
the leisure this time around to actually jump to the Dry validation documentation
(the guide's author prefers dry validation to AR), and first figure out how to implement a
two argument validation, then abstract some common validations into a parent module when I realized I was writing the same code over and over. 

So spending several hours over a few days of a long weekend putting this together was rewarding. Right now 0% of a web ui works (it _is_ a rails app, you can get the "You're on Rails" friendly page on port 3000 and probably see "no route defined" errors everywhere else). I twice mistakenly created columns named `end` in a model class, which is well and good until you start using these
names in code in ruby... `finish` it will be henceforth.

All in all a good start. I think Update actions are next, and maybe gluing the controllers into place. I had the entertaining time of building my own factory class "Default", then realizing that it did not need to have "factory" in the name. Sufficient tests uncovered bugs when they occurred (usually during refactoring), and I usually put a window on the right side with rspec running in a loop when I do these things.

I hope I get a chance to plow through the rest of this. I've been putting off maintenance on my wife's site for a while hoping to rebuild it in something nicer (with rails being "nicer" than web2py), and every interaction with the original site is a sigh of regret at how bad it is under the hood,
for how long it's successfully been running without real issues. All the real operational issues appear to have been tied to user error, or to email delivery. Special thanks to Turnkey Internet in New York for having effectively zero vps downtime in four years.
