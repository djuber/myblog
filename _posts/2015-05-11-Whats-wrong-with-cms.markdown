---
title: What's wrong with CMS systems
layout: post
category: commentary
---

## What's wrong with CMS systems now

I'd briefly like to brainstorm what's fundamentally wrong with major
systems like wordpress in deployment now. In fairness, my interaction with
these systems is mainly informed by support operations at a cpanel webhosting
company, so the ways I see the cms system is often tied to the places
where it does introduce problems large enough to elicit concerns.

# Security implications of editable pages

Write access from the internet is a really bad idea.
The promise is that "non-technical" content authors can
manage the system in a way that doesn't require typing any commands.
This is a great idea. The pitfalls have always been when you
try to elevate the notion of content into commands, or full-blown
dashboards that allow editing the core of the system, creating new
scripts, editing the styles, and installing extensions or plugins. The
possibly misguided premise here was that non-technical content authors
can become their own developers. This allows a publicly exposed interface
that makes a webserver into a globally editable turing machine,
you can put any code anywhere with a wordpress admin account. 

# Gross Misuse of database system
I'll single out wordpress here as the most well known to me, and my apologies to
any system that gets this right. I'm going to look at the content of the wp_options
table, in particular. The schema for this is pretty straightforward:

{% highlight sql %}
CREATE TABLE `wp_options` (
  `option_id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `option_name` varchar(64) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '',
  `option_value` longtext COLLATE utf8mb4_unicode_ci NOT NULL,
  `autoload` varchar(20) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT 'yes',
  PRIMARY KEY (`option_id`),
  UNIQUE KEY `option_name` (`option_name`)
  ) ENGINE=InnoDB AUTO_INCREMENT=160 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
{% endhighlight %}

That makes sense. We give a primary key option_id to a tuple (option_name, option_value), with
an autoload marker used to flag whether to just load this the first time (because we expect to
need it and hope to avoid a select later). We require option_name to be unique.

There are arguably good ways to handle this. The option_value column, with type longtext,
is the key point of misdesign in this case. Basically, this single table stores anything
you like to store in it, and the short, atomic options look to be well designed.
These are the ones that ship with core wordpress, rather than being added by a plugin.
Examples in this category include mailserver_url, mailserver_login, mailserver_pass,
and mailserver_port. You can see that each of these options stores a single value. However,
the longtext type invites misuse, as we can see from a simple check on the 'cron' option,
which stores the scheduled jobs:

{% highlight sql %}
select option_value from wp_options where option_name = 'cron';
{% endhighlight %}
{% highlight json %}
a:5:{i:1448697420;a:1:{s:20:"wp_maybe_auto_update";a:1:{s:32:"40cd750bba9870f18aada2478b24840a";a:3:{s:8:"schedule";s:10:"twicedaily";s:4:"args";a:0:{}s:8:"interval";i:43200;}}}i:1448702912;a:3:{s:16:"wp_version_check";a:1:{s:32:"40cd750bba9870f18aada2478b24840a";a:3:{s:8:"schedule";s:10:"twicedaily";s:4:"args";a:0:{}s:8:"interval";i:43200;}}s:17:"wp_update_plugins";a:1:{s:32:"40cd750bba9870f18aada2478b24840a";a:3:{s:8:"schedule";s:10:"twicedaily";s:4:"args";a:0:{}s:8:"interval";i:43200;}}s:16:"wp_update_themes";a:1:{s:32:"40cd750bba9870f18aada2478b24840a";a:3:{s:8:"schedule";s:10:"twicedaily";s:4:"args";a:0:{}s:8:"interval";i:43200;}}}i:1448702919;a:1:{s:19:"wp_scheduled_delete";a:1:{s:32:"40cd750bba9870f18aada2478b24840a";a:3:{s:8:"schedule";s:5:"daily";s:4:"args";a:0:{}s:8:"interval";i:86400;}}}i:1448736988;a:1:{s:30:"wp_scheduled_auto_draft_delete";a:1:{s:32:"40cd750bba9870f18aada2478b24840a";a:3:{s:8:"schedule";s:5:"daily";s:4:"args";a:0:{}s:8:"interval";i:86400;}}}s:7:"version";i:2;}
{% endhighlight %}

I've seen live websites with plugins that on each page load:
+ fetch the cron table
+ decode this serialized block of data into a php data structure
+ check for needed actions by search/iteration
+ update the table to remove any one time events that had been executed

The costs of this on the above, for a low traffic site, are manageable.
However, I've seen this one value swell over time to hit max_allowed_packet,
in excess of 5MB read, manipulated, and restored in the database on every
page load. Arguably, removing the wp_cron and implementing a server cron
solves this one case from becoming a performance nightmare. However, this is a
single instance of the pattern.

{% highlight sql %}
select option_value from wp_options where option_name='theme_mods_twentyfourteen';

| a:2:{i:0;b:0;s:16:"sidebars_widgets";a:2:{s:4:"time";i:1448616899;s:4:"data";a:4:{s:19:"wp_inactive_widgets";a:0:{}s:9:"sidebar-1";a:6:{i:0;s:8:"search-2";i:1;s:14:"recent-posts-2";i:2;s:17:"recent-comments-2";i:3;s:10:"archives-2";i:4;s:12:"categories-2";i:5;s:6:"meta-2";}s:9:"sidebar-2";N;s:9:"sidebar-3";N;}}} |
{% endhighlight %}

You can check the 'user_roles' option for another instance of this kind. So why does this matter?
You've basically taken structured data (there is a schema implicit in the cron or user_roles data),
and serialized a table into a text object, stashed it into the database, and are relying on
php to recreate what's essentially a set of objects, or an array, or similar. You then can search (select),
filter by particular fields, and set new values (update). You lose sql but are performing
database updates in raw php instead. When you're done, you put your 'option_value' back into the table.
This is treating the options table like a document store rather than a database table.

You will also see an enormous mix of data types stashed in the table, from dates as unix timestamps (as strings),
to urls and email addresses, to integers, to the above examples of this options table semi-json format. 

# Plugins as a developer substitute

Plugins in wordpress allow a means to add new functionality to a site,
by installing a project through a simple marketplace style interface.
This can vary from code that changes or disables core functions, or that
adds new capabilities (like a lightbox widget). These plugins are developed by
individuals or small companies, and these come and go. Updates may cease
for free plugins, and paid plugins still have serious problems. Many plugins
introduce dependencies or suggestions that other components be installed, and each
plugin runs on each page, registers its configuration components, and potentially
add database interactions on each step (more on this later).

As the plugin architecture is designed to make this the easiest way for users to
become developers, it's expected that a lot of amateur or first-attempt plugins will
be made, and there might be multiple options of varying quality for any particular need.

# Themes as designer substitutes

The smallest theme contains the css and basic menu/navigation means. This normally
boils down to the stylesheet, any supporting scripts (think bootstrap), and some
minimal server code generating a header and footer. These are planned to make the site
uniform without adding boilerplate to every page ([DRY](http://www.c2.com/cgi/wiki?DontRepeatYourself).)

This is laudable, and allows, like plugins, a site design marketplace. The first problem is that the
headers and footers are first class citizens on the machine. There's not any limit on the kinds
of code that can be run in the php used to generate the footers compared to the code used to access
the database or add new functionality. This means any code in a header or footer, will run with full
access to the system and on every page load. That's a huge performance impact, and a critical inject malware.

# Friendly error pages load the entire site

Two conspiring factors are at play here. One is the replacement of actual paths with friendly urls using
mod_rewrite or the equivalent, sending all content that's not static to the index.php entry point,
and letting this dispatch (so http://mysite.com/kitties/ actually loads /index.php, which  sees the url
was /kitties/ and gets a page, category, or post from the database). The second is the desire to again
give uniform appearance to the site, so the index.php entry point, after deciding there are no posts, pages,
or categories that relate  to /puppies/, you get a "page not found" 404 page that looks like the main site,
and possibly suggests the visitor see other pages. The cost of this is that you then run the code for the site
to do this. Since a 404 not found page is almost always going to be a cache-miss, any caching you have will
not be effective, and the 404 error is now more expensive than any activity a real visitor might exercise.
This makes a scripted scanner (like [wpscan](https://github.com/wpscanteam/wpscan), for example) effective
at a small scale Denial of Service on the server. 