---
title: What I learned from ActiveRecord
layout: post
category: commentary
---

I've been fooling around with Rails the past week or two. I'm using postgresql for the development environment instead of sqlite, so I don't end up in a situation where I regret using sqlite and have to migrate later.

Following allong the Getting Started guide, I created the second model, a Comment item on a blog post. This sets an article as a foreign key.
I ran the migration, created the table, and what do I see here? The migration both put the correct foreign key constraint on the article_id field, but then also indexed it. That's smart if you want to load the "comments for this article" on the article page (you will).

{% highlight sql %}
\d+ comments
Indexes:
"comments_pkey" PRIMARY KEY, btree (id)
"index_comments_on_article_id" btree (article_id)
Foreign-key constraints:
"fk_rails_3bf61a60d3" FOREIGN KEY (article_id) REFERENCES articles(id)
{% endhighlight %}

So what this tells me is that when you are going to add a foreign key reference,
it probably makes sense to create an index on the referred item, as rails did here. This will greatly speed up the types of
queries I see all the time from wordpress, for example (made up example) 
{% highlight sql %}
select comment_content, meta_value, title
from commentmeta
inner join comments on commentmeta.comment_id=comments.comment_ID 
inner join posts  on comment_post_ID=100
{% endhighlight %} for some post number 100, loading the comments and details from the comments table.

Indexing commentmeta on comments, comments on posts speeds this up greatly.

Checking a database, I see this is actually done:

{% highlight sql %}
CREATE TABLE `wp_comments` (
        `comment_ID` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
     	`comment_post_ID` bigint(20) unsigned NOT NULL DEFAULT '0',
      	`comment_author` tinytext COLLATE utf8mb4_unicode_ci NOT NULL,
        `comment_author_email` varchar(100) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '',
  	`comment_author_url` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '',
   	`comment_author_IP` varchar(100) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '',
      	`comment_date` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
        `comment_date_gmt` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
	`comment_content` text COLLATE utf8mb4_unicode_ci NOT NULL,
	`comment_karma` int(11) NOT NULL DEFAULT '0',
	`comment_approved` varchar(20) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '1',
	`comment_agent` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '',
	`comment_type` varchar(20) COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '',
	`comment_parent` bigint(20) unsigned NOT NULL DEFAULT '0',
	`user_id` bigint(20) unsigned NOT NULL DEFAULT '0',
	PRIMARY KEY (`comment_ID`),
	KEY `comment_post_ID` (`comment_post_ID`),
	KEY `comment_approved_date_gmt` (`comment_approved`,`comment_date_gmt`),
	KEY `comment_date_gmt` (`comment_date_gmt`),
	KEY `comment_parent` (`comment_parent`),
	KEY `comment_author_email` (`comment_author_email`(10))
	) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
{% endhighlight %}

Wordpress 4.3 at least does do just this, adding the key (with a corresponding index) for the post_id and parent, since you will make queries using these values all the time. The Foreign key constraint is something you need to enforce via the application, but that's because [Mysql Implements Foreign Keys Differently](http://dev.mysql.com/doc/refman/5.7/en/ansi-diff-foreign-keys.html), and it appears it ignores inline references.
I tested this, then had to look up why it didn't give any warning about a specification it was ignoring:

{% highlight sql %}
create table indexed (id integer not null unique, n integer references numbers(n)); 
show create table indexed;

CREATE TABLE `indexed` (
  `id` int(11) NOT NULL,
  `n` int(11) DEFAULT NULL,
  UNIQUE KEY `id` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
{% endhighlight %}

See [MySQL Create Table](http://dev.mysql.com/doc/refman/5.0/en/create-table.html) or more recent [MySQL 5.6 Create Table](http://dev.mysql.com/doc/refman/5.6/en/create-table.html). Specifically, "MySQL Server parses and ignores the FOREIGN KEY and REFERENCES syntax in CREATE TABLE statements."