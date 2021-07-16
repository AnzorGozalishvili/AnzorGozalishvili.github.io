---
layout: single
title: "Unsupervised Named Entity Recognition and Pain Point Analysis using spaCy and Prodigy Labeling Tool"
category: NLP
abstract: "Simple example of using prodigy labeling tool for Named Entity Recognition task and also Pain Point analysis."

---

# Introduction
Pain Point analysis is very popular and helpful method to extract user's negative opinions about the products. Pain Point analysis gives you the information about some entity and the problems with that causing users to stop using them. Looking into the context of these entities can give you the sense of user's opinion.

# Approaches
Pain point analysis can be defined in several ways:
1. One way that I found very useful is entity+action. You are defining company's products as entities and user's opinions as actions. Then you can train one model for NER and another for action detection. NER can be trained in several ways depending on the data you have. Action detection can be defined as classification problem.
2. Another way is entity+sentiment but it gives you more general information. You are detecting company's product and predicting user's sentiment about that product. Sentiment detection will be defined as classification problem.
3. One more way I came up with is entity+action_verb which again requires entity recognition but after that dependency parsing is used to find a verb in sentence of which subject/object is that entity. Then some mapping can be done to predefined pain point classes.
Something else.

First approach is more robust and pain point classes can be defined easily as much as needed. I chose that approach.

# Data
Data source is mostly the company's forum website where people are posting some problems with products and community members or support team are trying to help them.
Products list will be available on company's website which can be collected by hand or scraped. It should contains full list of products on which you will be working.

# Semi-Supervised NER
Named entity recognition requires annotated entities in text. It can't be collected from forum messages. Labeling such data requires too much human resource and it's better to avoid that.
Unsupervised NER can be done with predefined list of entity samples and some rule based system which can detect these entities in text. After meeting some level of accuracy you can start annotating entities in texts and create labeled data. Then you can train NER model on that data. After several iterations these models will be improve each other. Rule based system will match patterns and can work with high precision if you define the rules well, but trained NER model will have lower precision because it will use annotated data from rule-based model. NER model will use contextual information and will find new entities which will help rule-based model to find new patterns. All of these steps will require human resource but it will be too little compared to data labeling job.

# spaCy
SpaCy is "Industrial-Strength Natural Language Processing" library. It has support for many languages. It has a pipeline structure inside which has tokenizer, tagger, parser and NER. Each of them can be added or removed. SpaCy has a support for training models. You can train NER model with annotated data and create rule based NER model using Matcher class. You can define rules for each entity type in terms of spaCy's pattern matching.

# Prodigy Labeling Tool
It's uses spaCy internally. One thing that easily distinguishes that tool from others is bootstraping technique. That technique is used on labeling process and helps to annotate data not randomly but with balanced classes. It uses some model behind which tries to bring data samples close to the class you are annotating. In case of inbalanced classes it's very helpful. You can define some terms (some words of patterns specific to some class) which will help that model to find examples of given class and bring it for labeling. These patterns are defined in spaCy's language which gives it flexibility to match tokens with regex, tag, text etc. While you are labeling examples that model is improving and using vectorization and similarity search technique it is looking for you class examples. It has the database for storing annotated dataset. There are several labeling tasks that you can do: Classification, NER, segmentation, etc. I used that for do the classification job to train the model which predicts the class of pain point.

# The steps I did:
1. Prepared list of items for each entity and started generating rules for them.
2. Created Rule-Based NER for matching these entities using spaCy.
3. After looking for forum text messages I defined several pain point class
4. Collected terms/keywords that would be the indicators of these classes in forum messages.
5. Updated list of terms using Prodigy Labeling Tool which tries to bring similar examples with text similarity search using their embedded vectors.
6. Started annotating of forum messages data for each class separately using predefined terms and with the use of Prodigy Labeling Tool.
7. Trained classification model with "One Vs Rest" method which is default to Prodigy Labeling Tool. For given forum message text I have the predictions for each class separately (predictions doesn't sum to 1)

# Final Results
Entities were recognized well because of Rule-Based approach since people call products with the same name/shortname and no word context analysis is needed to understand entities correctly except one case when abbreviations overlap and abbreviations match but mean very different thing (maybe technical term or something else).
Pain Point classification worked fine and with quite good accuracy. It can be improved with more annotated data and retraining model on that data which become very fast with the use of Prodigy Labeling Tool.

# What can be improved ?!
Several things can be improved with that approach. There are list of steps I am going to follow:
1. Switch from Rule-Based NER to deep learning model which will work better sometimes to guess entities using context analysis. Use that model to improve Rule-Based model and collect more precise data again. After some iterations the results will be better with both model.
2. Consider edge cases where Pain Point classifier misclassifies and try to solve that problems. Sometimes it's very hard to analyze context.
3. Can use dependency parsing technique to further analyze the pain point and give detailed problem information to the user.
4. Several models can be combined for Pain Point analysis to improve accuracy and handle edge cases better.