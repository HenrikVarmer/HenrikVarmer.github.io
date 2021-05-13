---
layout: post
title: Build a simple recommender with Association Rules
---

Let's say all you know about your customers is their purchase history. With this data you want to generate individual recommendations representing cross-selling opportunities. How? [Association Rule Learning.](https://en.wikipedia.org/wiki/Association_rule_learning) 

In my opinion, this is a great approach if you find yourself limited by the lack of additional data that could give further context to a business transaction. 


This library relies on the [arules](https://cran.r-project.org/web/packages/arules/index.html) package for mining association rules and simply provides a wrapper for easy generation of recommendations with tidy datasets. 

