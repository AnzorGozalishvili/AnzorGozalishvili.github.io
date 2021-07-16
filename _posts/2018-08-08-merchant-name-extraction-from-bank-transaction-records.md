---
layout: single
title: "Merchant Name Extraction from Bank Transaction Records"
category: NLP
abstract: "Extraction of Merchant names from textual dataset using Different Deep Learning Approaches"

---

# Introduction
Bank transaction record contains encoded information about one transaction. It looks something like that:
![png](/images/posts/merchant-name-extraction-from-bank-transaction-records/bank_transaction_sample.png)

1. Transaction keywords: CHECKCARD, PURCHASE
2. Location: CA ATLANTA
3. Some numbers : 1228, 8005858131, 24755423363153630626348
4. Merchant Name: NETFLIX.COM

Merchant names extraction is very important for bank transaction records analysis.

# Problem
Given USA banks' transaction records, and you need to extract merchant names from it.

# Dataset
Dataset contains millions of records with extracted merchant names.

# Solutions
1. Regex-which is old technique but sometimes it still works.
2. Sequence to sequence RNN-which is deep neural network and very popular for working on texts.
3. Deep Convolutional Neural Network-little bit strange for working with texts but it worked.
4. Others.

# Regex
If you choose regex then you collect all available merchant names and their aliases. After you need to write tests for these patterns to avoid false positives. If you imagine to work on thousands of merchant names and then their aliases it seems to be the hard work. It has problems with generalization and can't detect new merchants without writing new rules. Good with regex is that you have less false positives and in some cases it is the best solution to avoid false positives.

# Sequence to Sequence RNN (LSTM + CRF)
It is very popular for working with texts. If you define that problem as sequence tagging (Merchant, Non-Merchant) then you can definitely use that model. Sequential models are good for analyzing context well but not forget that transaction records aren't natural language texts and have their style and structure which can be unpredictable. Downsides of that model is that it takes time to train since it's deep learning model; it may have problems with speed if the model is too heavy; It can have false positives which is critical for some use.

# CNN
Using that model is somehow strange for text analysis since text is one dimensional and requires different processing/encoding and preparation. It also requires 1D convolutions. 
Using CNN model for that problem is caused by inspirational blog "Deep Learning For RegEx" by Daniel C. LaCombe, Jr.

# Data Analysis
1. I found that Merchant names were at the beginning (78.9% of them) and it was one hint that model could learn predicting first tokens/letters of transaction as merchant.
2. Also letters were in upper case and there was not need to solve the cases problem.
3. Merchant names distribution in data was very imbalanced and right data sampling technique was required.

# Data Processing
1. Augmented data in such a way that merchants were appearing in different places. I started concatenating several transactions, replace merchants by another merchants, etc.
2. Used several sampling approaches to balance merchant names distribution.

# Training LSTM+CRF
Smallest elements were tokens (space separated units). There were only two tags: Merchant and Non-Merchant and training data was tagged that way.
After training on several epochs I got results:
- Train: 98%
- Valid: 97.2%
- Test:96.4%

# Training CNN
First text encoding should have been defined. I used and idea from the blog by by Daniel C. LaCombe, Jr. I encoded all texts like that. CNN model has two output: start and end indices of merchant in text.
CNN architecture looked like that:
![png](/images/posts/merchant-name-extraction-from-bank-transaction-records/merchant_extraction_model_architecture_diagram.png)
![png](/images/posts/merchant-name-extraction-from-bank-transaction-records/merchant_extraction_model_architecture_diagram_2.png)

After training on several epochs I got results:
- Train: 99.1%
- Valid: 98.4%
- Test:97.1%

# Comparing Models (RNN vs CNN)
![png](/images/posts/merchant-name-extraction-from-bank-transaction-records/merchant_extraction_compare_rnn_and_cnn.png)

# Conclusions
- There are many ways to solve merchant name detection problem and all solutions have their pros and cons.
- Convolutional Neural Networks can be used in NLP