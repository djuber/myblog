---
layout: post
date: 2016-10-24 22:43 -0500
title: postfix half-configured
categories: 
---
Moving off my last vps, I undertook the fun task of configuring postfix. This is apparently
a straigtforward task in most cases, and I understand why I've made this difficult for myself. I installed, then abandoned clumsily, and installation of the iredmail project, when
I learned that it really likes having a virgin system to install its own set of packages on.
Since replacing nginx and postgresql with other versions was not going to happen,
I unistalled big parts of this. At this point, my clumsy unfamiliarity with the details
of dpkg start to shine. Apparently, removing a package does not remove its configuration files. RedHat might make `.rpmsave` files to preserve initial config on removal, apt-get just leaves `/etc/` alone. So the butchered or tailored dovecot and postfix configuration files are
still installed.

Rather than just reimage, I moved the `/etc/dovecot` and `/etc/postfix` directories aside to
`/etc/postfix.save`, `/etc/dovecot.save`. That was a good plan, and `dpkg --reconfigure`
was going to be my salvation. But it looks like apt is smarter than I am, and announced
it would not reinstall the deleted files. Which was a bit odd, since that's precisely what
I was trying to do. I'll have to look more into that in the future.

So instead, I recreated empty postfix and dovecot directories, then cloned the defaults
from /usr/share/dovecot and `/usr/share/postfix`. I followed along two separate but corroborating guides on the use of mysql as an authentication store for aliases, users, and domains. That sounded like it was exactly what I wanted, and debugging shows that the
database integration is not the issue. After a few hours of fighting the system,
I now have dovecot-imap + ssl, dovecot-lmtp, and postfix as a mta successfully accepting and delivering mail from the internet. The only thing not working is smtp from local users,
which reports mail transport not available. That's a little aggravating, but ultimately not a show stopper, the only think I need sent out are forwards here. I can get by in the meantime. It's also weird that amavis is being looked for (that was part of the iredmail setup) while I can't see it configured in the postfix defaults, and I definitely did not do anything to explicitly set that up on the new instal. `master.cf` and `main.cf` are both really too short to have buried this sort of problem within them. I may have missed something in the permissions, I have the /mail directory set to handle mail, it does deliver, it does deliver from postfix for root@localhost, so I think the mydestinations variable is being read only, rather than in addition to the mysql domains database. 

On the threadline server, I do have outbound mail (with dkim) on postfix configured correctly. I think that should be a better or safer starting point than the defaults, even if the system is wheezy, while the server here is jessie. Postfix should be a slow moving target. If not, then the issue is that the tutorials are out of date, which would be reassuring.