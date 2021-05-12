---
layout: post
title: Recommending content with Latent Dirichlet allocation and Jensen–Shannon divergence
---

So, you've decided you want to build a recommendation system where the item you want to recommend is a text-based document of some sort. This could be an article, a peer-reviewed journal entry, a series of blog posts, or perhaps an event-description or similar. 

Say you want to recommend this to an individual user based on some sort of behaviour or preference for specific topics of interests. The actual purpose of your recommendation system is then to recommend any possible number of text-based items, sourced from a broad corpus of documents, to an individial user. 

How to go about this?

Well, enter [Latent Dirichlet allocation](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation) or, simply; LDA. This is a method was first applied in machine learning by David Blei, Andrew Ng and Michael I. Jordan in 2003. The super-duper short cliffnotes on this algorithm is that is finds latent topics in a corpus of documents. These latent topics are essentially multiple discrete probablity distributions over documents and words. Allow me to elaborate a bit...

For our use-case the core LDA algoritm can be illustrated like this: 

![LDAmodel](https://user-images.githubusercontent.com/40164071/117971449-3559ad80-b32a-11eb-8200-602511c3fb72.png)

As illustrated, the data input of the model is the entire corpus of documents. There's also some hyperparameters involved, but let's get back to that later. What you need to know for now is that the model outputs discrete probablity distributions of the identified latent topics in each document. Of course, this is not the only thing the model outputs, but this is actually all we need in order to generate some useful recommendations. 

> Right idea, lacking implementation

Most implementations I've seen of LDA focus on these discovered topics as the primary unit of analysis. They attempt to derive meaning and context from the words that contribute the most to each topic. One could imagine a recommendation system that viewed these latent topics as classifications of each document. I.e. if you have a user that's shown interest in a particular topic, recommend them more from the same topic.

In my opinion, this is the correct idea, but we need an aditional abstraction layer, in order to generate meaninful recommendations. You see, one of the core assumptions of LDA is that each document consists of _multiple_ topics.


## Build a Divergence Matrix
So how can we go about generating recommendations based on the entire list of genereated discrete probablity distributions for each document? 

My suggested approach is to use [Jensen-Shannon Divergence](https://en.wikipedia.org/wiki/Jensen–Shannon_divergence), a simple equation which we can use to find the distance between each these probablity distributions. In practice, you'd write your own implementation of Jensen-Shannon Divergence in your preferred language, then construct a matrix of the computed divergences between all of your probablity distributions, derived from the corpus of documents.

![Divergence_Matrix](https://user-images.githubusercontent.com/40164071/117974802-45738c00-b32e-11eb-8494-04415e68deca.png)

This matrix is essentially all you need in order to generate meaningful recommendations using LDA. Now, we have a complete dataframe of the computed Jensen-Shannon divergence of the topic probability distribution of every single document in our corpus. The only adittional data you need, is a dataframe containing the interactions of your users, with a particular document. This could be a click on said document, a signup for a conference (if you're recommending events based on event-descriptions), a download of an article, or a view on a blog post. 

The logic is then: A user has clicked on document ID132. Do a lookup in your divergence matrix table, and select all documents ordered by least distance to document ID132. Recommend as many of these documents as you like (e.g. top 3). 

## Code
I've written my own implementation of the concept described above in R, [here.](https://github.com/HenrikVarmer/textRec) 

**textRec** utlizes Latent Dirichlet Allocation and Jensen-Shannon-Divergence on the discrete probability distributions over LDA topics per document, in order to recommend unique and novel documents to specific users. It's essentially just a wrapper to the core LDA library in R: [_"topicmodels"_.](https://cran.r-project.org/web/packages/topicmodels/topicmodels.pdf)

In order to get recoommendations, simply input a dataframe of users, a dataframe of documents, and a dataframe of user/document interactions, set model hyperparameters and the amount of LDA topics to model (or alternatively, rely on textRec to automate modt hyperparameters using ldatuning::FindTopicsNumber() to find an optimal k number of topics). 

If not all customers have interaction history with documents, you can use the integrated ColdStart engine in order to find k-nearest neighbors and force a cold-start a recommendation for those users. See below function use example, for an explanation of hyperparameters and inputs. 

Run examplerun.R for an example of functionality and use, with test data from the /dat folder. 

## Installing textRec
Install the package directly from github with devtools. Run the first line if you do not currently have devtools installed.

```R
# install.packages('devtools') 
devtools::install_github('HenrikVarmer/textRec')
```

## textRec() function: Dataframes and hyperparameters

```textRec()``` is the main function, which takes three dataframe inputs, and outputs one single dataframe containing all users with recommendations. One row in the output is one recommendation for one user. See below comments for a brief explanation of what each parameter requires. 

```R 
textRec(users = custo,                          # dataframe of customers/users
        documents = texts,                      # dataframe of documents
        user_id = "UserID",                     # user ID column name
        text_id = "TextID",                     # document ID column name
        text_column_name = "DocumentText",      # text column name
        interactions = inter,                   # dataframe of interactions
        ngram_vector = c(1, 2),                 # vector of min and max ngrams
        lda_method = "Gibbs",                   # LDA method
        topics = 4,                             # k topics
        automate_topics = FALSE,                # automate topics TRUE/FALSE
        min_topics = 10,                        # min topics to iterate from (optional)
        max_topics = 120,                       # max topics to iterate to (optional)
        iterate_by = 3,                         # topic search iterate by (optional)
        lda_alpha = 0.2,                        # LDA alpha hyperparameter (optional)
        r_seed = 123,                           # R random seed for repex (optional)
        jsd_max = 0.1,                          # JSD max value to recommend (optional)
        stopwords = c("example", "vector"),     # stopword vector (optional)
        enable_coldstart = FALSE)               # coldstart (TRUE/FALSE)
        
```

## Output example:

In the output dataframe returned from the ```textRec()``` function, the 'item_history' column constitutes the user interaction history. The 'recommendation' column indicates the recommended document based on Jensen-Shannon Divergence from text documents with which the user has interacted, to another document. The 'votes' column specifies number of times this document was recommended to nearest neighbors, if the recommendation was provided by the cold-start engine. The 'type' column specifies whether the recommendation is derived from the LDA model or from the cold-start engine.

| doc_history    | customer   |	recommendation  |	JSD   | votes  | type   |
|----------------:|-----------:|-----------------:|----------:|------------:|-------:|
| 20,23,24,27     |     1001   |	           29   |	0.01      | NA	      | LDA_JSD  |
| 22,27,33        |     1002   |	           20   |	0.08      | NA	      | LDA_JSD   |
| 11,20,33        |     1003   |	           27   |	NA        | 32	      | ColdStart   |


