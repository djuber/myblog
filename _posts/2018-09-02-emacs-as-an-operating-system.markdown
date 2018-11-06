---
layout: post
date: 2018-09-02 12:58 -0500
title: emacs as an operating system
categories: 
---
There's a joke that emacs isn't as much an editor as an operating system.
The more I use it the more I prefer it to every other operating system
I've used in a long time. 

I prefer to think of it as a user interface
(there's some operating system down below handling network and disk access).
A sobering observation is that on some days the emacs process has more children
than my X session has (emacsclient runs a single process in the X session and the
server is running independently of the session). If current firefox didn't have
process isolation this would most certainly be the case.

I used to think of emacs as a development environment (I don't know much about Eclipse
but I get the feeling that eclipse works the same way, permitting extensions and plugins
to handle new functionality, which you would naturally program in and for
eclipse.) I remember reading Steve Yegge's comments on Emacs at Amazon's customer service
department.

The other way to rationalize this is that emacs is a lisp interpreter,
emacs lisp bytecode interpreter, and a bytecode compiler, so it's also a bit like the JDK
and JVM. It's a platform you use to build applications, and you can run your applications
there.