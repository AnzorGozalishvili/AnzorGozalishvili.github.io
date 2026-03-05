---
layout: single
title: "Creating Corpus for Georgian Language Modelling"
category: NLP
abstract: "A 37GB clean Georgian text corpus and data processing pipeline to support LLM training for one of the most under-resourced Caucasian languages."
---

Georgian is not just low-resource — it is remarkably under-researched for a living language spoken by millions. Existing large language models handle it poorly: GPT-3's tokenizer uses 2–3 BPE tokens per single Georgian character, compared to one token per English word.

To address the absence of well-organized training data, we built a complete data collection and processing pipeline and published an initial **37GB clean corpus** on HuggingFace.

## Pipeline

Data flows through four stages:

1. **Collection** — site-specific web crawlers and PDF extraction (via PyMuPDF with custom Georgian encoding normalization) across 25 Georgian domains, plus CulturaX's Georgian subset.
2. **Metric-based filtering** — Georgian character ratio, repetition/stopword/flagged-word ratios, language detection via FastText.
3. **Cleaning** — PII anonymization and Mtavruli→Mkhedruli character normalization.
4. **Deduplication & splitting** — URL deduplication + MinHashLSH, then 90/5/5 train/val/test split in JSONL format.

90% of the collected data is unique content not present in CulturaX.

## Resources

- [Paper PDF](/resources/creating_corpus_for_georgian_language_modeling_ACL_ARR_2024_Feb.pdf)
- Submitted to ACL ARR February 2024
