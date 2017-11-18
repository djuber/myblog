---
layout: post
date: 2017-11-17 20:40 -0500
title: Twiddling
categories:
---

I started reading Maciej Godek's master's thesis on data structure optimatization a few days ago. I'm pretty excited about the way it's going so far, at least for the introductory material the tone is clear and the pace is good. This must be what nerds do for fun on a weekday night.

I just read this cute schemism, "The list function could be defined simply as `(lambda x x)`, because if the list of arguments of a lambda expression is improper, then they are captured in a list and bound with the dotted tail of the argument list."

This of course works in scheme, the equivalent lisp (emacs or common lisp) would use a rest list, the lamdba form won't accept a symbol there. It's much less cleverly `(lambda (&rest x) x)`. On the other hand, scheme always seems a little alien to me, and the rest list notation is something I'm very comfortable with.