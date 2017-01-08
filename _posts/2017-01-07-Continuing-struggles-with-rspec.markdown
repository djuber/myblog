---
layout: post
date: 2017-01-07 20:51 -0500
title: Continuing struggles with rspec
categories: 
---

Trying the second example from the Continuous Testing book is even more difficult. The test tries to verify that module setup is registering hooks with Autotest. However, the rspec runner seems to load other tests (using this module, so initializing it) first, and the test fails if it is not run first. I had a hard time with this, and while I'm confident that including the module does in fact trigger the hooks, it's hard to verify. The last three examples in their test check for puts to have been called, which belies a bit more internal knowledge of autotest than I think is warranted.

In other news, since jeweller seems to have received official deprecation (the maintainer links to juwelier as an alternative), moving to juwelier changes the scaffolding that's pre-populated. I believe the require_relative might have been added after this book was published, there are everywhere calls to get the current path, and then append '../lib/file' in a require. Fixing those has been fun.

I'm not sure whether the autotest-stats gem will be viable in a modern environment.

