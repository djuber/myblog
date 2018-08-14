---
layout: post
date: 2018-08-14 11:53 -0500
title: web2py console
categories: 
---
I finally (re-)learned how to get into the web2py application without a browser to test code.

I don't know if that's something that can be leveraged to write proper unit tests,
but ```python web2py.py --shell appname --import_models``` seems to do the trick nicely for interactive exploration.

This should speed up testing "what query do I need and how do I process the results" which has been hit or miss for me in the past (I do a lot of looking at error tickets and backtraces in web2py).





