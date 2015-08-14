---
layout: post
title: "Colaborating with Mongodb (or trying) "
description: "MongoDB aggregation pipeline improvements."
excerpt_separator: <!--more-->
tags: [mongodb, opensource, tech]
---

After a lot of testing about features and performance, we finally switched our analytics storage engine from MySQL to MongoDB.

During our first tests we were surprised by the good write performance (using w=2) compared to MySQL (using MyISAM), which is not that good to be honest. Our first problem was the madurity of the Aggregation Framework, because the query routing and optimization is not good, specially in sharded setups, so we decided to give it a shot reporting those possible improvements using their JIRA (or upvoting them if they were filed already):

- [SERVER-5477](https://jira.mongodb.org/browse/SERVER-5477): In sharded setups when grouping using the sharding key (or a superset of it) as grouping key mongos should group the sharded results again, because there won't be duplicate group keys across the sharded results.
- [SERVER-4656](https://jira.mongodb.org/browse/SERVER-4656): The whole dataset is returned after sorting in every mongod and a merge-sort using all sharded results in mongos instead of using a queue for sorting when a limit is added to the pipeline.
- [SERVER-5361](https://jira.mongodb.org/browse/SERVER-5361): The group stage should use an index over the key to be able to read every group values.

To all of this, **we have to add that as an analytical tool we use aggregation [a lot](http://i.imgur.com/wsP9uab.png) and with a big penalty since we don't know all dimensions upfront since most of them are dynamic**, so pivoting all the data over big cardinality fields (the search terms, for example) is a slow process. So we hoped that 10gen (or the community) fixes these issues as soon as possible.

Seeing their slowness fixing this issues I've told myself: **"why I should wait for another developer to fix this bugs when I can do it for myself"**. After the initial joking and poker faces from my colleagues, I decided to start refreshing my little C++ knowledge. So I took the plunge and started working on [SERVER-5477](https://jira.mongodb.org/browse/SERVER-5477) issue on my own.

After two days trying to understand the current aggregation framework code and learning boost I started to develop the initial solution and a few unit tests. The result? A pull request that I've already sent to be approved.