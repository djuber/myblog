---
layout: post
date: 2018-09-03 10:23 -0500
title: Procrastination finally pays off
categories: 
---
This has been a good weekend. This weekend I made some backend changes.

- moved Beth's site from sqlite to postgresql after four years of running from a single file
- moved both of her sites from the old vps to the dedicated server (same host, [TurnKey Internet in New York](https://turnkeyinternet.net/).
  This means I can finally say goodbye to wheezy (Debian 7) which has served me well.
- moved both of her sites from cloudflare's phoney ssl to letsencrypt
- changed the reservations system to handle large blocks of time instead of hours at a time
- add a number of management features to the site for her to handle this
- reworked huge parts of the site internals to make it slightly more modular and more testable,
  I've learned a few things along the way since I wrote this the first time in more or less 48 hours of
  hacking away at the template application to make a site that worked a little like we wanted it to.
  Taking hundreds of lines at a time out of the controller and into separate classes that all do one thing each
  was satisfying. Web2py is a weird system but once you put your mind in the right place it will do fine.
- used email templates (maybe I did) via jinja2
- moved from mercurial to git and moved from bitbucket to gitlab

And I delivered it all just 36 hours late for the promised release.

On the personally satisfying parts, I was able to update the site software (a few python patch versions and
a few framework point releases), move from apache to nginx (what everything else I have is running),
and finally make use of a server I purchased about 2 years ago and remove the vps I purchased 5 years ago.

I believe I can also remove the [ovh vps](https://www.ovh.com/ca/en/) I have, but I probably will keep
that as a playground and will intend someday to get a mailserver back up for my domains.

I plan to move my static sites (including this one) to the server there and just keep backups regularly when
needed. I still need to update my backup script to change the "copy sqlite file from server" to
do a pg_dump and copy, but it should be fine (I've done this already for another db so it's just infrastructure).

I did bump into [this issue](https://support.cloudflare.com/hc/en-us/articles/115000219871-Why-does-Flexible-SSL-cause-a-redirect-loop-)
when I was moving the sites over, since I was enforcing https and cloudflare was sending http requests (I don't think it should have but
it might have started when I moved the A record).


I still need to get credentials out of code and into configuration, and I still need to move more of the logic out of the controllers (and maybe simplify the routes). The only thing I know I broke now is the robots.txt file gives a 404 error (I think I am missing a route to static for this, not sure how or why the favicon is working or if the static directory is in the wrong place).

