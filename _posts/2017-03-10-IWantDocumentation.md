---
title: I Want Documentation
layout: post
category: commentary
---

## Documentation should be required

I think that the free software movement has gone a long way toward bringing light
to software, and worked hard both to create powerful software, and usable documentation.
I know that some "server" distributions try to stay lean, and if you're running
multiple VM's or containers of the same OS, that installing manual pages and info pages
is "wasted space". However, a desktop distribution, Fedora in this case, should always
either bundle the complete documentation with the binary, or have a -doc package alongside
it.

I don't know what prompted them to leave out a 256Kb info file from the gnu screen package.
Somehow, info had an index entry for this software, suggesting at some point that the manual
was installed. I most certainly would not have become root, and removed  screen.info.gz from
/usr/share/info/, but it was absent.

## If your distribution won't package documentation

Well, the solution here is to build your own (effectively, bypass the distribution).
I grabbed the git repo for screen, from [Savannah](https://savannah.gnu.org/git/?group=screen),
and ran ```make && make install``` in src/doc. That was small, fast, and, to me, obvious.

I feel bad for the users who lack this know-how, and disagree with Fedora for
omitting this documentation from their repository packages.

## The plot thickens

I will add that, confusingly, the screen package does contain
(both the fedora 25 base install,
and the most recent 4.5.0 version) the info pages.
Reinstalling the package replaces the screen.1.gz manpage,
but /usr/share/info/screen.info.gz is still missing.
I confirmed with rpm2cpio that the correct file
and path are there. I checked my dnf.conf file, nothing surprising,
and I can't find any option from
[DNF's documentation](http://dnf.readthedocs.io/en/latest/conf_ref.html) that
could disable info pages.

So my reaction to blame the maintainer may have been misplaced. I'm still looking into this,
but it's low on my list of priorities, since I've installed the binary and docs from
the git repo.
