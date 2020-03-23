---
layout: single
title: "Relevant Phrase Tagging in Semantic Search"
date: 2019-04-22
background_image: "/images/semantic_search.jpg"
author: "Anzor Gozalishvili"
author_image: "/images/avatar.jpg"
category: NLP
abstract: "Idea of automatic tagging of relevant phrases in semantic search results"
---

![title](/images/semantic_search.jpg)
# **Relevant Phrase Tagging in Semantic Search**

## Motivation

Once I was doing one project about semantic similarity search I got an idea of extracting important keywords from 
search results. I applied one intuitive feature importance technique which worked well. I am gonna share this idea and 
maybe someone will find it interesting and useful.

## Introduction

Semantic search is very interesting problem in nlp. You can actually use pre-trained model embeddings and any distance 
measure like cosine similarity to create working example of semantic search engine. It's kind of simple approach and 
you can find many examples of it. One that I used it bert-as-a-service which is very popular for now. Since transformers
are performing well in many nlp tasks they can be used for text embeddings. Several pooling strategies are applied to 
that model in order to get better embeddings. There is another approach where you fine-tune this model to learn cosine 
similarity of actual pairs of documents which seems to be working better, but it needs big amount of labeled sentence
pairs for your domain which is hard to collect. 

## Semantic Search System
