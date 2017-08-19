---
layout: post
date: 2017-08-18 12:55:00
title: Learning Clojure
categories:
---

Well, I said I would never do it, but I have decided to take a stab at learning clojure.

First impressions. Cider is pretty slick as an environment (I really am enjoying the "run tests in namespace" `C-c C-t n` combination and the test reporting.) `C-c C-c` and `C-x C-e` do what they're supposed to. Creating a new test file puts a reasonable looking header with some dependency imports already filled in.

The Java part is pretty lousy. You can't compile a function (like a test) referencing an unbound name. Your backtraces, while well formatted, still look like java. It's not the end of the world, and I'm doing my best for the meantime to ignore them, but the compiler errors are less than stellar.

Immutability is probably the biggest difference (apart from a handful of notational shortcuts for sets and vectors) from common lisp, and I fumbled my way through watching the `loop`/`recur` paired construct, but it's not substantially different from the named `loop` in scheme, and not too far away from `labels` in common lisp. But wow, immutability. `fn` is a fine name for lambda, it has variadic functions, polyadic functions (method dispatch based on argument length, I'm probably butchering this word). Not sure what the equivalent of CLOS's `defmethod` is, where the class of the method is used by the runtime to determine the effective method, but CLOS is some wild and crazy (and potentially non-lispy) code.

I started piecing together some code from project euler, which was how I learned python, and a lot of the common lisp code I'd written was tied to that. I got through multiples and fibonacci series, I'm about to try to figure out primality testing, my CL code had all used a lot of updating on let names, so it probably takes some rethinking.

In all, whether I get any work done, it's helping to break habits of mind, which is valuable in any sense.
