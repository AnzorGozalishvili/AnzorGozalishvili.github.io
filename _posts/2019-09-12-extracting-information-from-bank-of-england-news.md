---
layout: single
title: "Extracting Information from Bank of England News"
category: NLP
abstract: "Example project aimed to extract important structural from bank of england news using 
classical nlp approaches."
---

# Introduction
Bank news is very important for tracking latest stock changes. It's very critical problem since business decisions depend on that numbers. Automatization of that decisions can be very helpful for many banks and organizations.

# Problem
Given England Bank News sample from it's website:
![png](/images/posts/extracting_information_from_bank_of_england_news/bank_of_england_news.png)
Extracting of Bank Rate and Quantitative Easing (QE) numbers is needed. Bank Rate: 0.75%, QE: N/A.

# Solutions
- Regex-requires engineering rules by hand and testing every time it's updated. Can't use context information. Words come in different forms and is hard to generate all patterns from them. It's hard to handle OOV(Out of Vocabulary) words.
- Dependency Parsing-is appropriate for that task since texts are formatted well. Understanding of context and work dependency relations is needed for understanding content. Matching patterns can be done with advanced techniques (POS, LEMMA). It can partially handle OOV.
- QA-needs training of deep learning models. Can have more errors and mistakes which makes it unreliable for that problem. Preparing training data for that model is very hard and requires too much effort.

# How I approached the Problem
I choose second approach since high accuracy and more flexibility was needed.

# NLP Libraries Used
I used spaCy and NLTK libraries for handling texts. I used dependencies information from spaCy and and subclassed NLTK-s Tree class for having nice view of generated dependency tree for convenience.

# steps I did:
1. Generate dependency tree of sentences inside bank news texts.
2. Collect keywords from dependency trees
3. Generate general patterns in such sentences (Bank news texts are strictly formatted and such formats are 10–20)
4. Build context trees for all these patterns
5. Validate information inside dependency trees using context tree

# Generated Dependency Trees
![png](/images/posts/extracting_information_from_bank_of_england_news/bank_of_england_dependency_tree.png)
Through analysis of such tree patterns I found 10's of them in total.

# Collect keywords and find patterns
![png](/images/posts/extracting_information_from_bank_of_england_news/bank_of_england_dependency_tree_2.png)
Mostly patterns consisted with some main verbs (increase, vote, maintain), Subjects/Objects (MPC-Monetary Policy Committee, Bank of England, Committee) and numbers/percents.

# Generating General Patterns
In most of the cases sentence with some meaningful information was starting with subject, then some action was described which could have been done on Bank Rate change or QE amount. After finding that patterns I was able to write just 10–20 general rules which worked on all examples with 100% accuracy.

# Build context trees
I had two abstractions: **Dependency Tree**, **Context Tree** and **Context Matcher**. Dependency tree was the tree of sentences containing dependencies, POS tags, lemmas, etc. Context trees had information for parsing such dependency trees and finding generated patterns. Context Matcher was taking Both trees and were finding patterns and parsing needed information.
Context Tree had several fields inside:
- **Bad Subtree Tokens**: tokens that mustn't be in subtree of given token
- **Candidates**: several versions that were found in that node in parsed dependency tree
- **Children**: context tree children nodes
- **Label**: name for given node of context tree
- **Parent**: parent of given node
- **Validator (lemma, pos, good subtree tokens)**: validator checks lemma, pos tags, and tokens that is needed to be in subtree

There could be more but these were enough for me to get things done.

# Validate Information in Dependency Trees
Context Matcher working principle:

1. Build Dependency Tree for the text.
2. Build one Context Tree for one Rule/Pattern.
3. Brute force search for all patterns from all root of Dependency Tree.

After running searches from each root, Context Trees were storing search information in inner nodes and after running all validation rules with success, it was guaranteed to have have valid numbers.

# Request & Response Sample
![png](/images/posts/extracting_information_from_bank_of_england_news/bank_of_england_request_and_response.png)

# Conclusions & Final Comments
- There are new, old and hard ways to approach given problem and depending on requirements you can choose the best that fits you needs.
- Using NLP libraries are better than just REGEX. It includes all capabilities of regex and adds more advanced text analysis. Only one requirement is to have natural language texts.
- Deep Learning Models are very good if you don't have a critical need of perfect precision and you can handle to collect labeled data.
