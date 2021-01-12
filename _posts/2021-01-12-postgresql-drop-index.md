---
layout: post
title:  "Drop index needs a schema"
date:   2021-01-12 12:50:00
categories: postgresql
---

This week I needed to manually roll back part of a migration (it included multiple statements, direct shame elsewhere, please).

I knew, because the migration would reapply the `create index ...` statement, that a drop would be needed.

In mysql I think you alter table to drop an index, but in postgresql the documentation points to a more straightforward
`drop index INDEXNAME`. I tried this and it didn't work.

Issue was default schema for my connection was "public", but the index lived in another schema.

For the future, use `drop index SCHEMA.INDEXNAME` when doing this.
