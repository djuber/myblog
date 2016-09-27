---
layout: post
date: 2016-09-27 09:00 -0500
title: MySQL and Py3K Still a Problem
categories: 
---
I was trying to generate a mysql database from the [headline corpus](https://doc-08-c8-docs.googleusercontent.com/docs/securesc/ha0ro937gcuc7l7deffksulhg5h7mbp1/sch82bq420308vaf4jccgus23avbk5ua/1474956000000/11237625372748346939/*/0BwHjGgf5xZ9RclNGZWN5UHZkV2s?e=download) after reading the related [article](https://freedom-to-tinker.com/2016/09/14/all-the-news-thats-fit-to-change-insights-into-a-corpus-of-2-5-million-news-headlines/).

I started a virtualenv and installed 'dill' which was used to serialize and export the data. I wanted to get sqlalchemy to build (and ideally populate) the tables. I then thought that
I obviously needed a db backend to handle the client connection. I assumed that years
later this would not be a problem, but the 'best practice' interface, [MySQL-python](https://pypi.python.org/pypi/MySQL-python/1.2.5) seems to still rely on ConfigParser syntax that is python2 specific. I'm a little surprised that this is a problem. It would be awesome
if the pypi page had a recommendation. Instead, like all people with a problem,
I find myself on [stackoverflow](http://stackoverflow.com/questions/4960048/python-3-and-mysql#25724855) and will be testing [mysqlclient](https://pypi.python.org/pypi/mysqlclient) with sqlalchemy.

Once I got that working (hint, import MySQLdb, even though the package name is wildly different), I started trying to connect and use it. It's a little raw (not using sqlalchemy):

```
 db = MySQLdb.connect(<all my site-specific secrets>)
 cur = db.cursor()
 cur.execute("Create table sites (id integer primary key, sitename varchar(64));")
```
this sent and ran the following in my headlines db:

```
CREATE TABLE sites (
       id int(11) NOT NULL,
       sitename varchar(64) DEFAULT NULL,
       PRIMARY KEY (id)
       ) ENGINE=InnoDB DEFAULT CHARSET=utf8

```

So that's a first pass at a sites table. The documentation inside the (large) zip is that the dictionary keys are the sites (actually, it's a messy bit with path info tacked on, like 'anchors/past_year_and_half/www.chicagotribune.com_20150601000000.dille.com_20150601000000' instead of 'www.chicagotribune.com', but that's fixable). So the 64 char size is based on wanting to ad the shortened 'www.hollywoodreporter.com' name instead of the long anchors/ path.

The next idea is that this dictionary has a [sites] -> url mapping, and [site, url] -> set of headlines mapping. A first approximation to map this from a dictionary to a set of tables in the database sounds like this in pseudo-DDL:

```
table sites: id, sitename
table urls: id, site references sites.id, url
table headlines: id, url references urls.id, headline
```
There are many sets with a single headline, and some sets with several headlines per story. The premise in the article was that news sites could change the headline, changing the perceived meaning of the article (and possibly changing the 'click rate'). They were training this against the newyorktimes and buzzworthy to detect 'clickbaiting'. I am just doing this as an example of database import, to get from one format to another.