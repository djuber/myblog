---
title: What's really wrong with wp-cron
layout: post
category: commentary
---

## What's wrong with wp-cron

Let's review what can happen with a wordpress cron  

I've seen the wordpress cron option and system backfire in a number of ways.
I'd like to sketch out my thoughts. I first read through these thoughts about
how the cron wasn't sufficient to ensure jobs run on schedule https://www.lucasrolff.com/wordpress/why-wp-cron-sucks/

# Loads on each page view

A real cron loads every minute, looks for jobs to run, and executes them. Wordpress cron, a php program,
is by default invoked by the wp-settings.php file, so it loads on each page view. That's cool if you're me, and google visits
your page once a week, less optimal if you're a big site with lots of visits. Every wp-cron.php process uses memory, waits on the
database, and consumes needless resources, especially if the job will not run.

# Schedule of jobs stored as a serialized array in the options table

Okay. I understand the wordpress developers. They say "runs with php + mysql". In order to accomodate this,
they made a horrible tradeoff, and shoved as much as possible into the options table. Core wordpress does not rely on cron
being available on the system. Core wordpress does not rely on additional tables outside its very limited schema
being functional. I'm not sure core wordpress even requires foreign key support in mysql (absent from MyISAM for a ridiculous
amount of time). To work around this, they did the best they could. I really think if you wanted to schedule a set of jobs,
you could make a table for this. Serializing an array smells alot like putting a table inside a row. I'll leave it to the relational
theory nazis to analyze that.

# Schedule of jobs is actually a work queue in some cases

As a hosting company, I only see this when it reaches perverse levels. Lets inspect the process in the context of a lot of traffic,
plugins that schedule jobs after or during every page view, a long queue of jobs to process, and remote updates.
If you're unsure why I suggest this situation, consider the impact of https://wordpress.org/plugins/disqus-comment-system/.
This puts a comment thread on every post, and a separately scheduled job on every post thread. On a moderately busy site,
these grow to the megabytes, and since the serialized array is just a text field (option_value) in the table, the database hands
it off to any and everyone as a string. I've seen 40-50% of php execution time taken by retrieval, deserialization, manipulation (enqueue), and update of this value as the database grows. If you think I'm making this up, compare https://wordpress.org/support/topic/high-server-load-and-dsq_sync_forum-problem/.

So when you have a large, serialized list, and a network latency for each job, what happens is that this value
(appended to on each pageview with a key that says something like "five minutes from now, check for more comments), 
takes longer to process than the space between page views. Since the read from option cron does not lock the row (not part of mysql)
or the table (harikiri for wordpress, which won't load if the options table is not readable), 
what happens is that subsequent reads and updates will cause the row to be updated while the worker is handling the cron.
What this means in practice is that as long as the traffic rate is high enough that the odds of a read occurring 
(part of "select option_name, option_value from options where autoload='yes') occurs within the execution time of the main site
before the cron process finishes, then the cron job, completing its tasks queue, goes to write the cron option, but the read a moment
before commits an append to dirty/old data immediately afterward, and all completed work is enqueued again. This is a nightmare. Rather
than a set of independent jobs, because of serialization, this is a single blob. The only way this ever empties is when the ambient
request level drops to nearly nothing, so that the cron process can complete. If the time to process exceeds the frequency of requests,
the picture is bleak.


# How this could be done right.

Well, apart from my first instinct --
a cron table, with one column for next time stamp, one column for periodicity, one column for action --
the next best thing would be to mark doing-cron in the options table, and have a cron-append option
that is normally empty if not doing-cron, and the final step in cron.php will be to execute logic that
takes the processed job list, merges in the cron-append content, sets cron to the result, and clears both
doing-cron flag and cron-append array in the options table.