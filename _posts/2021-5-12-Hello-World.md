---
layout: post
title: Recommending text-based content with Latent Dirichlet allocation and Jensen–Shannon divergence
---

So, you've decided you want to build a recommendation system where the item you want to recommend is a text-based document of some sort. This could be an article, a peer-reviewed journal entry, a series of blog posts, or perhaps an event-description or similar. 

Say you want to recommend this to an individual user based on some sort of behaviour or preference for specific topics of interests. The actual purpose of your recommendation system is then to recommend a text-based item, sourced from a broad corpus of text-based items, to an individial user. 

How to go about this?

Well, enter Latent Dirichlet allocation or: LDA. This is a method was first applied in machine learning by David Blei, Andrew Ng and Michael I. Jordan in 2003. 

![LDAJSD](https://user-images.githubusercontent.com/40164071/117963799-1c003380-b321-11eb-90fa-fc94a33f9725.png)
![TopicsWords](https://user-images.githubusercontent.com/40164071/117963809-1e628d80-b321-11eb-8401-57b430ab6872.jpg)
![AllUsers](https://user-images.githubusercontent.com/40164071/117963820-202c5100-b321-11eb-8b47-d05d7e1fc91c.jpg)
