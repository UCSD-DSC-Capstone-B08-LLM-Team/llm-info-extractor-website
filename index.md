# Large Language Models as Information Extractors in Healthcare

## Abstract

Large language models (LLMs) show strong potential for assisting with clinical information extraction, but their performance depends heavily on how relevant evidence is retrieved from long, unstructured medical notes. In this project, we evaluate retrieval-augmented generation (RAG) strategies in a controlled, privacy-preserving “needle-in-a-haystack” setting using the MIMIC-III clinical dataset. We derive clinically meaningful queries from the CMS SEP-1 abstraction specifications and generate synthetic needles grounded in the abstraction logic to serve as known evidence. For each patient, we construct long-context haystacks by inserting one needle into aggregated patient notes, allowing us to systematically test retrieval and downstream LLM performance.
We compare multiple retrieval strategies, including BM25, FAISS (cosine and Euclidean), ColBERT, hybrid retrieval, Semantic Chunking, and SPLADE. These methods are evaluated on their ability to surface relevant evidence under realistic query-based conditions rather than exact keyword matching. We also implement baselines without retrieval and sliding-window prompting to assess whether retrieval meaningfully improves LLM performance. LLM responses are generated using AWS Bedrock (primarily DeepSeek-V3) and evaluated across classification, extraction, and summarization tasks, with particular focus on classification accuracy and top-k retrieval metrics.
Our results highlight how passage segmentation, semantic matching, and retrieval diversity affect LLM accuracy when handling long and complex clinical narratives. By systematically comparing lexical, dense, sparse, and hybrid retrieval methods under identical experimental constraints, we provide insight into when retrieval improves performance, when it may be unnecessary, and how context length and distractors influence LLM behavior. This work contributes a reproducible framework for evaluating retrieval strategies in clinical RAG systems and offers guidance for building cost-efficient, privacy-conscious medical AI pipelines.

## Introduction

## Data

## Methodology

## Results

## Conclusion

