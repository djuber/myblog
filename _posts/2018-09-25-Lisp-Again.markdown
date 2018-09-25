---
layout: post
date: 2018-09-25 13:43 -0500
title: Lisp Again
categories: lisp
---

I occassionally look back longingly at common lisp. Working full time in ruby by day and dabbling poorly in python on weekends to keep a side project running means I don't have a lot of bandwidth for experiments.

I decided to reimplement a program I wrote a month or two back (in ruby) in common lisp, mainly to get a feel for the cost of finding and using libraries.

I glued together the code in a few hours (if you stop using a language for a while everything becomes hard, but you have the advantage of knowing that solutions exist). I focused on picking recommendations from the [awesome-cl list](https://github.com/CodyReichert/awesome-cl).

The program fetches json from a service (DivvyBike in Chicago) - parses json to a list of clos objects, filters them to the stations I'm likely to need, and emits an html table.

The ruby original generates html programmatically, I used djula templates in this case instead. I used dexador and jsown (recommended from the awesome list) for the fetch and parse steps. The lisp version is longer than the ruby version - I think that's generally true, but at least half of that was not knowing where I was going and having become completely unfamiliar with the feel of lisp after a few years of not working with it.

Source code published to [notabug](https://notabug.org/djuber/divvy/).

Ruby version updates to [here](https://beta-reduction.com/divvy.html).

Parting thoughts - getting Djula to find the template directory seemed harder than I wanted it to be. Getting lisp to write to the correct directory (or figuring out how to make a path from a directory and a filename) were more than I wanted to invest time in for this - so I punted and ended up writing to tmp.
