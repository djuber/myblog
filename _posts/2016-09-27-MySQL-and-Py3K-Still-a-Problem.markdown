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