---
layout: post
date: 2017-01-07 09:29 -0500
title: All the ways my secrets get commited
categories: 
---

Still working slowly through the continuous testing book.

Cleverly, and having made a myriad of mistakes in the past, I decided not to commit
my access tokens to git. Most documentation currently favors the use of environment
variables to provide api keys, so ```TWITTER_SECRET_TOKEN``` used by the api client would be set in the environment of the running process, and kept outside of the code.

Being a little lazy, I opted instead for just putting the keys in a file in /etc/ to prevent git from adding it. I then ran rspec with simplecov, and was aghast to find the secrets were indexed by the coverage tool (100% used, since it's all just global variable assignments). Fortunately, simplecov added coverage to the gitignore file, so no loss there.

I might want to test a few other methods. You might find yourself in a position where sharing a coverage report is required as documentation of the test suite, but sharing the access tokens and secret api keys is not acceptable. Loading your secrets with require is not a good plan in that case.

