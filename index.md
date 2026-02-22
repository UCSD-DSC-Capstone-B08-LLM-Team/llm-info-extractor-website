# Large Language Models as Information Extractors in Healthcare

## Abstract

Large Language Models (LLMs) show strong potential for clinical information extraction, yet their effectiveness depends heavily on retrieving relevant evidence from long, unstructured medical records. In this project, we evaluate retrieval-augmented generation (RAG) strategies in a controlled, privacy-preserving “needle-in-a-haystack” framework using the MIMIC-III clinical dataset. We derive clinically meaningful queries from CMS SEP-1 abstraction specifications and generate synthetic needles grounded in the abstraction logic to serve as known evidence within aggregated patient-level notes.

We compare multiple retrieval strategies, including BM25, FAISS (cosine and Euclidean), ColBERT, hybrid retrieval, Semantic Chunking, SPLADE, and Agentic Search—under realistic query-based conditions rather than exact keyword matching. For each patient, a single needle is inserted into long-context haystacks constructed from combined clinical notes, enabling systematic evaluation of retrieval accuracy and downstream LLM performance.

LLM responses are generated using AWS Bedrock (primarily DeepSeek-V3) and evaluated across classification, extraction, and summarization tasks, with emphasis on classification accuracy and top-k retrieval metrics. Our results highlight how passage segmentation, semantic matching, and retrieval diversity influence model accuracy in long clinical narratives. By systematically comparing lexical, dense, sparse, and hybrid retrieval methods under identical experimental constraints, we provide insight into when retrieval meaningfully improves performance, when it may be unnecessary, and how context length and distractors shape LLM behavior. This work contributes a reproducible framework for evaluating retrieval strategies in clinical RAG systems and offers guidance for building cost-efficient, privacy-conscious medical AI pipelines.

## Introduction

## Data

## Methodology

## Results

## Conclusion

