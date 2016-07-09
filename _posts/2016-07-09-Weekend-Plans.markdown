---
layout: post
title:  Weekend Plans
date:  2016-07-09 15:00:00 -0500
categories: journal
---

Working from my desk at home today, since Beth has a shop day. One fun project, make
a restful service that gives diffs of all versions of wordpress files. For any install,
for any file, determine if it's core, give the md5 sum, and optionally give the fulltext
so a diff can be made (to patch changes back to original state, or identify malware).

First, let's gather all the release files (there are only so many because this includes betas):

```bash

lynx -dump https://wordpress.org/download/release-archive/ | grep tar.gz$ | grep [0-9]\.\ htt

```

Maybe we don't care about the betas.

```bash

lynx -dump https://wordpress.org/download/release-archive/ | grep tar.gz$ | grep [0-9]\.\ htt | grep -Ev 'beta|RC'

```

That gives 211 dotted versions from 0.71-gold to the current 4.5.3. That's a manageable data set, I think. Lets grab all the links for those. We'll process them as part of the data acquisition later.
```bash
mkdir wp-files
cd wp-files
for url in $(lynx -dump https://wordpress.org/download/release-archive/ | grep tar.gz$ | grep [0-9]\.\ htt | grep -Ev 'beta|RC' | awk '{print $2}')
do
  wget $url
done
```

Hint: This is much faster on the server than on your desk. 