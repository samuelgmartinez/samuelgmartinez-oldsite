---
layout: post
title: "Matching similar search queries"
description: "Trying to match similar user queries using clustering algorithms."
image:
  feature: abstract-1.jpg
tags: [analytics, tech]
---

This this post I'm going to explain a little experiment I developed a few days back, which resulted quite useful for extracting trends about the trendiest topics and lowering the cardinality of search queries.

### The problem

In our analytics processes **it is very important trying to deduplicate search queries**: first, **because we ease the analytics user job** packing together queries with the same target expressed in diferent ways, for example "rewards catalog" and "catalog of rewards" are matched as diferent queries when, in fact, are the same. And last but not least, it lowers the number of different aggregations we need to perform in each query, **enabling a faster response time**.

<!--more-->

Working with real data we found a lot of problems trying to match this similarity. In spanish, there are users that uses tittles and others don't, there are ones who capitalize, others don't, even we can find a lot of non letter characters like dashes, quotes, etc. So, we thought that transforming to lowercase and removing/translating to a common character set (removing tittles, dashes, etc) would do the work but, sadly nothing could be further from the truth. Here is where clustering algorithms play really nice.

To be able to do this keeping our design and infraestructure and trying to minimize the writing rate of our analytics stack we looked for one pass clustering algorithms (here we say goodbye to TF-IDF clustering).

### Looking for a solution

Algo que cumple todos estos requisitos es MinHash clustering. Para quien no lo conozca, MinHash es un método que  aproxima el coeficiente de similitud de Jaccard, o lo que es lo mismo, permite estimar cuán parecidos son dos conjuntos. 