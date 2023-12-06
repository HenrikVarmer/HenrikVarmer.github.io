---
layout: post
title: Jobmatch with AI
---

In this post, I'll share some details on how we built a small application that matches resumes (CVs) with active job listings in Denmark. We use this as an internal tool, and we have recently soft-launched a customer facing product with the same functionality at its core.

### What is it
We built an AI tool that gives the user 3 (hopefully) relevant and active job listings, based on their resume. The AI also lets the user know what they should focus on in a written application, and even helps them get started.

You can try it now, live [here.](https://www.lederne.dk/kampagner/jobmatch)

### How does it work
It works by taking the following input:
* E-mail
* ZipCode
* City
* Resume in .pdf format

The application maintains a database of all active job listings in Denmark, which is constantly updated using a combination of webscraping and API integrations to third parties.

The database of active job listings is vectorized with embeddings using OpenAI's [embedding API endpoint.](https://platform.openai.com/docs/api-reference/embeddings)

The resume is OCR scanned, and vectorized using the same approach.

You can see where this is going.. Now we have a vector database we can query with any given resume, giving us closest n job listings.

A key factor in searching for a job, is (still- in 2023), physical proximity to the location where you will be working. This is why the application needs zipcode and city as input from the user.

We use this data to query Google's Maps API and get an approximate commute distance in return. We then find top n jobs that fulfil the two main criteria:

* Close physical distance
* Close distance in vector space (relevance proxy)

Having found the job listings we want to present to the user, we subsequently ask GPT 4 pick the three job listings it likes the best, for any given resume.

We then ask GPT 4 to draft content for the final e-mail which we'll send the user, and which will serve as the final output of this small data product.

The content we ask it to create is:

* The users core qualifications (_essentially, why is this person particularly qualified for this job?_)
* Get started with an application (_help the user draft a motivated application_)

This, coupled with a summary of the job listing, which is created by another GPT model in a different dataflow, is sent to our marketing automation system, is embedded in an automated mail flow, and finally sent to the user.

This application is mainly a fun gimmick, but serves two main purposes:

1) It introduces people to the power of generative AI.
2) It might help people get exposed to new jobs which they had not previously considered

The team has many ideas on how to expand this product into something more. On the roadmap currently is:

* Better UI/UX
* Reverse matching (companies can find people)
* Career paths (i.e. show how people progress throughout their career, which job titles are tied together, perhaps shown in a graph visualisation)
* Even more integrations

I'll end with this: A talk i gave, where - among other things - I talked about [the technical implementation of this project](https://www.youtube.com/watch?v=0TAvwGoF_Mw&start=646)
