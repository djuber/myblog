---
layout: post
date: 2016-09-03 21:49 -0500
title: Rails 4 in Action
categories: 
---

I'm working through 'Rails 4 in Action' now. It's a fun read. I like the test first setup, and it
feels a lot like the Harry Percival book for python (djano and selenium to build a task list site).
I usually read the book on the train coming home, then reread and work the code on
the couch. So by the time I'm actually typing a line, I already have a good idea where I'm going and why. I don't know that I've read a programming book that way before. It seems to be pretty productive. 
You can see the code as I go at [bitbucket](https://bitbucket.org/daniel_uber/ticketee).

Since I don't have watcher or autotest setup yet, I have been just using a sleep loop to run rspec on the desktop. I'm a bit surprised how slow the startup is. I remember from the coursera course that once the seed data was in place, reseeding the test db for tests was especially bad. I don't look forward to that as this grows. Right now, off the platter disk, it's about 1.5 seconds to read the files, and a few milliseconds to run them. Sometimes I must miss file cache, and startup takes closer to six seconds.

Also, I just discovered rubocop. I think I need to be less inclined to listen to it, or to
tune its recommendations, since the rails generators and rubocop have clear
disagreements on good style. I'm all for 80 character lines. I see why they
emit preferences for single quotes. Documenting a class sounds reasonable. But
that's either a huge retooling of libraries or unattainable. I bet that, like rspec's backtrace,
it's tunable to filter out the noise.

