---
layout: post
title:  "Managing Users and Groups"
date:   2015-04-21 12:00:00
categories: labnotes objectives
---

One of the easier non-trivial aspects of the [ RHCSA objectives ](https://www.redhat.com/en/services/training/ex200-red-hat-certified-system-administrator-rhcsa-exam)
is the setup of users and groups. They have an entire section for this, putting this
on an equal footing with all of the common command line (sed, grep, su, chmod) access
items. I would recommend starting with `info coreutils user` to get the docs for `id`, `logname`, `whoami`, `who`, `groups`, and `users`.

[ `man usermod` ](http://linuxcommand.org/man_pages/usermod8.html) shows
these in "SEE ALSO":

+ [ chfn(1) ](http://linuxcommand.org/man_pages/chfn1.html)
+ [ chsh(1) ](http://linuxcommand.org/man_pages/chsh1.html)
+ [ passwd(1) ](http://linuxcommand.org/man_pages/passwd1.html)
+ [ crypt(3) ](http://linux.die.net/man/3/crypt)
+ [ gpasswd(8) ](http://linux.die.net/man/1/gpasswd)
+ [ groupadd(8) ](http://linuxcommand.org/man_pages/groupadd8.html)
+ [ groupdel(8) ](http://linuxcommand.org/man_pages/groupdel8.html)
+ [ groupmod(8) ](http://linuxcommand.org/man_pages/groupmod8.html)
+ [ login.defs(5) ](http://linux.die.net/man/5/login.defs)
+ [ useradd(8) ](http://linuxcommand.org/man_pages/useradd8.html)
+ [ userdel(8) ](http://linuxcommand.org/man_pages/userdel8.html)

From RedHat's page, we see (as of today) the following items listed under "Manage users and groups":

+ Create, delete, and modify local user accounts
+ Change passwords and adjust password aging for local user accounts
+ Create, delete, and modify local groups and group membership
+ Configure a system to use an existing authentication service for user and group information

Creating and deleting and groups accounts is straightforward
(useradd, groupadd, userdel, groupdel), but modifying accounts is non-trivial.
Using an 'authentication service' implies probably ldap or kerberos. LDAP is a larger topic than goes here. I'll talk about the other information in this post.
Setting passwords with passwd is straightforward,
but managing the lifespan and expiration dates was new to me.
As a home user, I typically set passwords to not expire (I assume most people do this).
At work, we reset passwords on all machines randomly whenever we have a
user leave the company, with every machine account
set uniquely and randomly.
If we ever had a time without any turnover, the passwords would possibly stay the same
indefinitely. 

As a system administrator, I see group membership changed
incorrectly at least once a month among my peers, in an environment where
most of the decisions have already been taken. cPanel systems tend to restrict
access to several systems based on group membership. We have an internal hardening
that assigns wget and curl to the `get-users` group, and only users in this group
can access those commands. Similarly, cPanel supports an equivalent `compiler` group
that can access the gcc toolchain on the system. Ubiquitous in Unix is the group `wheel`
that is often the only set allowed su access, or can be given automatic sudo privileges.

The video course from Sander van Gudt does go over this pretty exhaustively. The setgid
and sticky bits on directories are explained, and methods for allowing read-write access
to, but not allowing deletion of, files in common directories is covered. The permissions
model (user/group ownership and permissions, and ACL's) needs to be understood as well.

# usermod

The most common pitfall I see is with the [`usermod`](http://linuxcommand.org/man_pages/usermod8.html) command, where the `-G` or `-g`
options are confused.
The capital G sets the list of supplementary groups this user belongs to.
The lower case g sets the initial group, which in RedHat and CentOS was a group with
the same name as the user, and a single member. That is:
{% highlight bash %}
useradd newbie
id newbie
{% endhighlight %}
outputs this:
{% highlight bash %}
uid=1001(newbie) gid=1001(newbie) groups=1001(newbie)
{% endhighlight %}

We can add this user to a new set of groups as the secondary list:
{% highlight bash %}
groupadd new-users
usermod -G new-users newbie
id newbie
{% endhighlight %}

And we see the new situation showing two groups.

{% highlight bash %}
uid=1001(newbie) gid=1001(newbie) groups=1001(newbie),1002(new-users)
{% endhighlight %}

# Why the initial group matters

The users primary group will be the group ownership assigned to any uploaded, created,
or copied files. This can be very important in the context of shared content, and setting
this incorrectly can lead to some security problems.

Let's take a simple example. We have four users `sam`, `tim`, `mark`, and `brian`.
Each are members of the `blue-team` group, and now a fifth person is added to
the blue-team. That is we have a situation like this:

{% highlight bash %}
id sam
uid=1003(sam) gid=1005(sam) groups=1005(sam),1003(blue-team)
{% endhighlight %}

Sam's home directory has files set `sam:sam` (user sam, group sam)
and permissions 751 on the directory.
This allows group members to access files in /home/sam/blue-team, where files are set
group blue-team. Then Dave joins the team. Sam `su`'s to root, and creates an account
for Dave, incorrectly:

{% highlight bash %}
useradd dave
usermod -g blue-team dave
{% endhighlight %}

Now dave only belongs to the group `blue-team`, and all his files are accessible
to the team, including his mail directory, where he plans with the boss to get Sam
fired. Bad days for Dave.

However, for the user's home directories, that's actually less of an issue. The default
behavior is to set `/home/$USERNAME` to permissions 700, and that means Sam can't access
/home/dave/secrets.

{% highlight bash %}
cat /home/dave/secrets
cat: /home/dave/secrets: Permission denied
{% endhighlight %}

In cPanel environments, the home directory has broader permissions, 711, to allow
the access of these files by the `nobody` user, since the document roots in cPanel shared
hosting are under `/home/$USERNAME/public_html/`,
rather than in a location like `/var/www/`
that's more common under stock Linux installations.
This would allow the users in blue-team to access the content in the directory.
On my debian systems at home, the permissions on user homes are broader still, set to
755 (others can list the directory content, while in cPanel they can `cd` but not `ls`).

# Overwriting the current secondary groups

Apart from confusing the `-g` and `-G` options, there's also the destructive update
behavior of the `-G` option. For example, Tim needs access to the system compiler,
and the gcc binary is set 750 root:compiler. Sam now wants to add Tim to the compiler
group to accomplish this. Reading the man page for `usermod`, he find `-G`, remembers
his bad experience with Dave last week, and issues the following:

{% highlight bash %}
usermod -G compiler tim
{% endhighlight %}

Sam should check his work, since this removed Tim from the team (and Tim will not be
getting that report done this weekend!):

{% highlight bash %}
id tim
uid=1004(tim) gid=1006(tim) groups=1006(tim),1009(compiler)
{% endhighlight %}

There is a `-a` append option that needs to be used, and setting this straight requires
one of the following two commands:


{% highlight bash %}
usermod -G blue-team,compiler tim

usermod -a -G blue-team tim
{% endhighlight %}

Either should give the end state as desired:

{% highlight bash %}
id tim
uid=1004(tim) gid=1006(tim) groups=1006(tim),1003(blue-team),1009(compiler)
{% endhighlight %}
