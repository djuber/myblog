---
layout: post
title:  "Feeling Bad for Forking"
date:   2020-06-05
categories: crystal
---

I forked a project today (https://github.com/waterlink/spec2.cr) when it seemed easier than
opening an issue. I was going to PR a fix for my problems, then saw a [pretty old issue](https://github.com/waterlink/spec2.cr/issues/68) to check if it's even maintained, and decided to move on.

This is my first time adding a release tag to a repo, after realising crystal-lang's [shards](https://github.com/crystal-lang/shards) tool [uses tags](https://github.com/crystal-lang/shards/blob/master/README.md#usage) to resolve versions and not the `version:` line in the shard.yml file.

It seems the costs of trying to use an alpha quality language is there are no guarantees outside of the stdlib that user libraries are still valid.
