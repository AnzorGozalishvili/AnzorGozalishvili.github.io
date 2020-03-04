---
layout: post
title: "Simple QA API"
date: 2020-03-04
background_image: "../images/question_answering.jpeg"
author: "Anzor Gozalishvili"
author_image: "../images/author.jpg"
category: NLP
abstract: "Simple Example of building question answering API using spaCy and nmslib"
---

![title](../images/question_answering.jpeg)
# **Simple Question Answering API using spaCy and nmslib**

Question answering is very popular task in NLP. Each routine agent work can be replaced using such system. 
So imagine you have a list of frequently asked questions and it's answers. Then you can easily automate the job
of operator using simple NLP pipeline which I will describe here.

## **Problem**:
Given the list of questions and their answers, our goal is to build a ml model which will match questions to
right answers. 

## **Solution**:
We can use text similarity. We can calculate embedding of all questions and index them in any database that 
supports vector similarity search. One such library which is [nmslib](https://github.com/nmslib/nmslib). It works 
really fast and is able to search 10^5 samples per second (see [benchmark](http://ann-benchmarks.com/)). 
For calculating text embeddings we can use very popular NLP library [spaCy](https://spacy.io/). So, we will
embed all questions and index them using nmslib. When we get a question, we just calculate it's embedding and 
then find closest embedding vector indexed in nmslib. We will assume that the question is very close to one of which 
closest embedding was found. If the distance is lower than some threshold, we can return the answer linked to given
question. 

## **Example Data**:

| question | answer                                                 |                             |
|----------|--------------------------------------------------------|-----------------------------|
| 0        | Carl and the Passions changed band name to what        | Beach Boys                  |
| 1        | How many rings on the Olympic flag                     | Five                        |
| 2        | What colour is vermilion a shade of                    | Red                         |
| 3        | King Zog ruled which country                           | Albania                     |
| 4        | What colour is Spock's blood                           | Green                       |
| 5        | Where in your body is your patella                     | Knee ( it's the kneecap )   |
| 6        | Where can you find London bridge today                 | USA ( Arizona )             |
| 7        | What spirit is mixed with ginger beer in a Moscow mule | Vodka                       |
| 8        | Who was the first man in space                         | Yuri Gagarin                |
| 9        | What would you do with a Yashmak                       | Wear it - it's an Arab veil |
| 10       | Who betrayed Jesus to the Romans                       | Judas Escariot              |




## **Notebook**:

## Imports


```python
import pandas as pd
import os
import json
import re
import spacy
import nmslib
```


```python
!pip install nmslib
```

```python
!python -m spacy download en
```

```python
!pip install xlrd
```

# Read xslx file sheet


```python
data = pd.read_excel('../data/QA_(0-100).xlsx','Sheet1',  index_col=0, header=None)
```


```python
data = data.iloc[:, 0:2].reset_index(drop=True)
```


```python
data.columns = ['question', 'answer']
```


```python
data.head(2)
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>question</th>
      <th>answer</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Carl and the Passions changed band name to what</td>
      <td>Beach Boys</td>
    </tr>
    <tr>
      <th>1</th>
      <td>How many rings on the Olympic flag</td>
      <td>Five</td>
    </tr>
  </tbody>
</table>
</div>


# Create QA model class

```python
class QA(object):
    def __init__(self, data):
        self.nlp = spacy.load('en')
        self.questions = data.question.tolist()
        self.answers = data.answer.tolist()
    
    def to_vectors(self, texts):
        """Convert texts into their vectors"""
        result = []
        for item in texts:
            result.append(self.nlp(item).vector)
        
        return result
            
    def build_nmslib_index(self):
        """build nmslib index with vectors of question texts"""
        self.index = {}
        self.index = nmslib.init(method='hnsw', space='cosinesimil')
        self.index.addDataPointBatch(self.to_vectors(self.questions))
        self.index.createIndex({'post': 2}, print_progress=True)
        
    def search(self, text, max_distance=0.2):
        """
        K-Nearest-Neighbour search over indexed taxonomy data and distance threshold parameter 
        to get most similar one. 
        Args:
            text: (str) sample question text
            max_distance: (float) maximum allowed distance for neighbours

        Returns:
            result: (tuple) index and distance for found item

        """
        result = {}
        vector = self.nlp(text).vector
        
        if vector is not None:
            ids, distances = self.index.knnQuery(vector)
            
            if ids is not None and distances is not None:
                best_indices_mask = (distances == distances.min()) & (distances < max_distance)
                if best_indices_mask.sum() != 0:
                    result = {'index': ids[best_indices_mask][0], 'distance': distances[best_indices_mask][0]}

        return result
    
    def query(self, question, max_distance=0.2):
        search_result = self.search(question, max_distance)
        index, distance = search_result.get('index', -1), search_result.get('distance', -1)
        result = "N/A"
        if index != -1:
            result = self.answers[index]
        
        return result
```

```python
qa = QA(data)
qa.build_nmslib_index()
```

```python
qa.query('Carl and the Passions day changed band name to what', max_distance=0.05)
```
    'Beach Boys'

```python
data.head(2)
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>question</th>
      <th>answer</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Carl and the Passions changed band name to what</td>
      <td>Beach Boys</td>
    </tr>
    <tr>
      <th>1</th>
      <td>How many rings on the Olympic flag</td>
      <td>Five</td>
    </tr>
  </tbody>
</table>
</div>


```python
preds = data.question.apply(lambda x: qa.query(x))
```

Full source code can be found [here](https://github.com/AnzorGozalishvili/QA_using_spacy_and_nmslib)
