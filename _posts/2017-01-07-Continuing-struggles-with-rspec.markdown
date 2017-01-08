---
layout: post
date: 2017-01-07 20:51 -0500
title: Continuing struggles with bitrot
categories: 
---

Trying the second example from the Continuous Testing book is giving even more difficulties. The test tries to verify that module setup is registering hooks with Autotest. However, the rspec runner seems to load other tests (using this module, so initializing it) first, or all at once, and the test fails if it is not run first. Running this test in isolation gives the expected pass, but running it combined with tests that load the module leads to a failure (the module only initializes once, and only registers with Autotest once. I had a hard time with this, and while I'm confident that including the module does in fact trigger the hooks, it's hard to verify consistently. The last three examples in their test check for ```puts``` to have been called, which belies a bit more internal knowledge of autotest than I think is warranted. Those definitely failed, and I think the internal details of autotest or the way it handles hooks have changed in the past years, and puts is no longer being called as expected. 

Additionally, since jeweller seems to have been deprecated, as the maintainer links to juwelier as an alternative at [github](https://github.com/technicalpickles/jeweler), moving to juwelier changes the scaffolding that's pre-populated. I believe the require_relative might have been added after this book was published, there are everywhere calls to get the current path, and then append '../lib/file' in a require. Fixing those has been fun.

I'm not sure whether the autotest-stats gem will be viable in a modern environment. You can watch me slog through this at [github](https://github.com/djuber/autotest-stats).

