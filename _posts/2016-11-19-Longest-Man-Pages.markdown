---
layout: post
date: 2016-11-19 23:10 -0500
title: Longest Man Pages
categories: 
---

I got bored, and wondered which man pages on my laptop were longest.

After rereading the section in the bash info page and bash-builtins man page,
I finally got `read` working.
I hardly ever use read in loops, prefering `for` loops on input,
but I felt like this was the less awkward way to do things.

So I hacked this together.

First we check our example input:

```bash
$ man -k sudo
sudo (8)             - execute a command as another user
sudo.conf (5)        - configuration for sudo front end
sudo_plugin (8)      - Sudo Plugin API
sudoedit (8)         - execute a command as another user
sudoers (5)          - default sudo security policy plugin
sudoreplay (8)       - replay sudo session logs
visudo (8)           - edit the sudoers file
```

Now we dice it into a section and page, so we can invoke man section page to get the right manual. This is needed to handle things like crontab that is a file(5) and a tool(1) for managing it. I just search for space hoping that gives all the manpages:

```bash
man -k \  > manpages.list
while read;
do
   page=$(echo $REPLY | awk '{print $1}');
   section=$(echo $REPLY | awk '{print $2}' | tr -d '()');
   echo $(man $section $page | wc -w) $page;
   unset page;   unset section;
 done < manpages.list  | sort -n 
```

For reference, manpages.list was 7618 lines long. I'm sure there's a much smarter way to do multiple assignment (Read says it takes a list of variables, but every time I tested it, it seems like it failed to assign anything, so I opted for the REPLY default).

The worst offender was gcc (that's a pile of options that all matter), and it was listed twice (man gcc and man gcc-4.9 should give the same page. cc, c++, g++, gcc are all one page with many names).

```
107994 c++
107994 cc
107994 g++
107994 g++-4.9
107994 gcc
107994 gcc-4.9
```

The next bunch were for wider range of tools, bash being just under 50,000 words. I usually joke about rsync having a painfully long man page (and a bewildering set of options) so this was my measuring stick for complexity. xterm somehow has more to say than rsync, but reading that page suggests the xterm manual is covering the configuration language for the terminal emulator. I've foolishly equated xterm with an internal "produce command prompt in black window on screen" command, but it has a lot more going on internally, comparable to minicom. Mercurial has a monolithic man page (git splits its apart, so it's hard to see the complexity in this list), cmake appears to have multiple distinct sections like git, but each with as much to say as tools like nmap or rsync.  

```
25252 nmap
27400 openvpn
27579 rsync
28717 cmake-commands
30558 mplayer
32164 xterm
34672 Net::SSLeay
41662 hg
41944 cmake-modules
44536 bash
```

Indeed, git breaks things up substantially.
There are 158 git related commands in the man database.
Taken in total this outweighs gcc's options, but some of that is surely boilerplate.

Here's the tail of just the git commands:

```
4743 git
5084 gitattributes
5230 git-diff-index
5382 git-diff
5535 gitweb.conf
5824 git-show
5996 git-rev-list
6860 git-diff-tree
8648 git-fast-import
9818 gitcore-tutorial
10335 git-log
18865 git-config
```

Cmake has a more restricted vocabulary:

```
824 cmake-toolchains
900 cmake-generator-expressions
929 cmake-qt
1405 cmake-generators
1799 cmake
2230 cmake-language
2738 cmake-packages
3613 cmake-buildsystem
3757 cmake-developer
8090 cmake-policies
11600 cmake-variables
15453 cmake-properties
28717 cmake-commands
41944 cmake-modules
```

So none of this means anything, but it was a thought experiment. [xkcd](https://xkcd.com/1343/) regards the sudoers(5) page as the worst read. It may be horribly dense, but it's not overly long:

```bash
man 5 sudoers | wc -w
15151
```