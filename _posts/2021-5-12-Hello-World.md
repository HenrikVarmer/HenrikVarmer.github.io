---
layout: post
title: Recommending content with Latent Dirichlet allocation and Jensen–Shannon divergence
---

So, you've decided you want to build a recommendation system where the item you want to recommend is a text-based document of some sort. This could be an article, a peer-reviewed journal entry, a series of blog posts, or perhaps an event-description or similar. 

Say you want to recommend this to an individual user based on some sort of behaviour or preference for specific topics of interests. The actual purpose of your recommendation system is then to recommend any possible number of text-based items, sourced from a broad corpus of documents, to an individial user. 

How to go about this?

Well, enter [Latent Dirichlet allocation](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation) or: LDA. This is a method was first applied in machine learning by David Blei, Andrew Ng and Michael I. Jordan in 2003. The super-duper short cliffnotes on this algorithm is that is finds latent topics in a corpus of documents. These latent topics are essentially multiple discrete probablity distributions over documents and words. Allow me to elaborate a bit...

For our use-case the core LDA algoritm can be illustrated like this: 

![LDAmodel](https://user-images.githubusercontent.com/40164071/117971449-3559ad80-b32a-11eb-8200-602511c3fb72.png)

As illustrated, the data input of the model is the entire corpus of documents. There's also some hyperparameters involved, but let's get back to that later. What you need to know for now, is that the model outputs discrete probablity distributions of the identified latent topics in each document. Of course, this is not the only thing the model outputs, but this is actually all we need in order to generate some usful recommendations. 



![LDAJSD](https://user-images.githubusercontent.com/40164071/117963799-1c003380-b321-11eb-90fa-fc94a33f9725.png)
![TopicsWords](https://user-images.githubusercontent.com/40164071/117963809-1e628d80-b321-11eb-8401-57b430ab6872.jpg)
![AllUsers](https://user-images.githubusercontent.com/40164071/117963820-202c5100-b321-11eb-8b47-d05d7e1fc91c.jpg)
