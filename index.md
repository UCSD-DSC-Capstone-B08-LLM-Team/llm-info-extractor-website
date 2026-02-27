# Large Language Models as Information Extractors in Healthcare

## Abstract

Large Language Models (LLMs) show strong potential for clinical information extraction, yet their effectiveness depends heavily on retrieving relevant evidence from long, unstructured medical records. In this project, we evaluate retrieval-augmented generation (RAG) strategies in a controlled, privacy-preserving “needle-in-a-haystack” framework using the MIMIC-III clinical dataset. We derive clinically meaningful queries from CMS SEP-1 abstraction specifications and generate synthetic needles grounded in the abstraction logic to serve as known evidence within aggregated patient-level notes.

We compare multiple retrieval strategies, including BM25, FAISS (cosine and Euclidean), ColBERT, hybrid retrieval, Semantic Chunking, SPLADE, and Agentic Search, under realistic query-based conditions rather than exact keyword matching. For each patient, a single needle is inserted into long-context haystacks constructed from combined clinical notes, enabling systematic evaluation of retrieval accuracy and downstream LLM performance.

LLM responses are generated using AWS Bedrock (primarily DeepSeek-V3) and evaluated across classification, extraction, and summarization tasks, with emphasis on classification accuracy and top-k retrieval metrics. Our results highlight how passage segmentation, semantic matching, and retrieval diversity influence model accuracy in long clinical narratives. By systematically comparing lexical, dense, sparse, and hybrid retrieval methods under identical experimental constraints, we provide insight into when retrieval meaningfully improves performance, when it may be unnecessary, and how context length and distractors shape LLM behavior. This work contributes a reproducible framework for evaluating retrieval strategies in clinical RAG systems and offers guidance for building cost-efficient, privacy-conscious medical AI pipelines. Testing

## Introduction

### 1.1 Overview

Medical notes are a key part of the health care system, informing doctors about their patients, whether it be allergies or previous complications and treatments, so that doctors can make informed decisions when treating patients. However, the medical reports are often unstructured and can be difficult to interpret. To maintain and report quality data, hospitals in the United States on average spend $5 million each year while also requiring around 100,000 person-hours of work. These hospitals collectively spend about $15 billion on maintaining medical notes, with each physician dedicating roughly 785 hours to this task (Casalino et al. 2016).

With the recent advances in large language models, maintaining and reporting on medical notes may become easier for hospitals while also potentially increasing the accuracy of medical chart reporting. LLMs have a wide breadth of knowledge and have shown to perform well on medical exams, such as the MCAT. However, LLMs have shown to struggle in some aspects, such as hallucination, long context windows, and outdated information.

In our research, we are studying to see whether LLMs are able to take on the ever-important task of extracting key information from medical notes and how we can improve their performance. We developed controlled needle-in-a-haystack evaluations and began systematically testing how well different retrieval strategies surface clinically relevant information before it reaches the LLM. These updates allow us to measure not only whether LLMs can extract key information, but how it can be augmented by retrieval strategies.

---

### 1.2 Prior Work

Large Language Models are surprisingly good at extracting unstructured information from electronic health records (EHR). Multiple Large Language Models were evaluated on made-up clinical notes with all models performing well, with Claude achieving the best performance (Ntinopoulos et al. 2025).

A common strategy for enhancing LLM performance is through Retrieval-Augmented Generation (RAG). RAG works by retrieving relevant information from an external knowledge base and augmenting the user’s prompt with this retrieved context before generation. This grounds the LLM’s response in verifiable, up-to-date information, reducing hallucinations and improving factual accuracy.

RAG can also be used to enhance LLM performance in information extraction. Prompting techniques can be very helpful in improving the performance of LLMs as information extractors. One study attempted to store numeric information from LLMs into structured records and used several heuristic-based approaches to reduce hallucinations (Adam et al. 2025). These approaches included using a regex-based cross-checking scheme, removing values that were used as examples in few-shot prompting, and removing values outside of a predefined range of plausible values.

With these techniques, LLMs were as effective at parsing numeric values as carefully constructed, specific regex expressions, though taking substantially less time. However, researchers often observe context rot within an LLM’s performance. An article from Chroma found that having larger context windows with irrelevant context often ends up in worse performance for an LLM (Hong, Troynikov and Huber 2025). As additional context was added, particularly within the middle of an LLM’s context, researchers often found the LLM’s performance declined substantially.

RAG-based approaches may be able to compensate for this weakness. Building on this prior research, our project incorporates both retrieval-augmented techniques and controlled evaluation frameworks to better understand LLM behavior in realistic clinical scenarios. By comparing retrieval methods such as BM25, ColBERT, FAISS, Semantic Chunking, SPLADE, and hybrid approaches, we extend earlier work by quantifying how each method affects the LLM’s accuracy in downstream extraction tasks. This provides a more detailed picture of where current systems succeed, where they fail, and how retrieval choices influence overall model performance.

---

### 1.3 Relevant Data

We use the MIMIC-III Clinical Database, which contains de-identified EHR data for around 46,000 patients from the critical care units of Beth Israel Deaconess Medical Center in Boston between 2001 and 2012. It includes structured data such as demographics, vital signs, lab results, medications, and diagnostic codes, in which identifying information has been removed.

From this database, we focused on the large collection of unstructured clinical notes present in the NOTEEVENTS table. These notes vary significantly in length, structure, and writing style. They often contain domain-specific language with variability in phrasing and abbreviations. Combined with negations in the text, past but not current conditions, and duplicated text across notes, this complexity makes information extraction particularly challenging.

Using the MIMIC-III database, we construct patient-level haystacks of notes and insert meaningful needle events to be retrieved from the haystack. This design allows us to evaluate retrieval methods under conditions that are similar to actual clinical documentation settings.

## Data

## Methodology

## Results

## Conclusion

