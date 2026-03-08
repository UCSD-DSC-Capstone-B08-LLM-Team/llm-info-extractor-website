---
title: Home
layout: default
---

<div class="paper-container">

<aside class="sidebar" markdown="1">
<p>Table of Contents </p>
* TOC
{:toc}

<div class="paper-sticky-buttons">
  <a href="https://drive.google.com/file/d/1blgz2Qu0_6Q6jLcEsHvOmNzyLmKDGK7j/view?usp=drive_link">Report</a>
  <a href="">Poster</a>
  <a href="https://github.com/UCSD-DSC-Capstone-B08-LLM-Team/LLM_as_Info_Extractor">Github</a>
</div>
</aside>

<main class="paper-content" markdown="1">

# Large Language Models as Information Extractors in Healthcare

<div class="paper-meta">
  <strong>Leah Seseri, Lewis Weng, Omid Alamdar</strong><br>
  University of California, San Diego<br><br>
  <strong>Mentors:</strong> Aaron Boussina and Karandeep Singh
</div>


## Abstract
Large Language Models (LLMs) show strong potential for clinical information extraction, however, electronic health records (EHR) often contain large amounts of redundant or irrelevant text that confuse the LLM making it struggle to reliably retrieve relevant information. Retrieval-augmented generation (RAG) can mitigate this challenge by reducing the amount of context provided to the LLM, although the most optimal approaches have not been explored. We introduce a benchmark for evaluating and comparing retrieval methods for EHR information extraction. Using the Severe Sepsis and Septic Shock Early Management Bundle (SEP-1) from the Centers for Medicare and Medicaid Services (CMS) specifications manual, we generated 100 synthetic needles representing positive qualifiers across five clinically meaningful elements. For each element, we randomly selected 10,000 patients from the MIMIC-III dataset (461,290 total clinical notes) and inserted one synthetic needle per patient to construct patient-level haystacks. We evaluated six retrieval strategies and prompted the LLM using top-10 retrieved passages. We assess the retrieval approaches against a baseline approach without RAG that uses all the information within the context limit of the model. LLM responses were generated using AWS Bedrock (DeepSeek-V3) and evaluated on classification and extraction tasks. SPLADE achieved the highest top-k recall, while BM25 achieved the lowest. Retrieval effectiveness varied by clinical element, with clinical trial queries achieving the highest recall and vasopressor queries the lowest. Baseline performance indicated that longer context and higher top-k retrieval reduced model accuracy, showcasing the need for retrieval. Overall, our results demonstrate a strong positive correlation between retrieval quality and LLM accuracy, emphasizing that developing and improving retrieval strategies can lead to more effective clinical information extraction.  

## Introduction

Extracting information from medical charts is critical for patient care and healthcare operations. Medical documentation contains diagnoses, treatments, medications, laboratory results, and observations that directly influence medical decision making.
Thus, it is significant that extracting this information from charts is not only done but achieved accurately. Moreover, not only does the information on medical charts matter, but there are numerous other tasks that depend on this information. Extracted chart data can be used for direct patient care and populating clinical registries. Such registries include providing information to advance medical research or reporting data to regulatory bodies so that guidelines and standards are enforced. One example of a regulatory body that data needs to be reported to is the Centers for Medicare and Medicaid Services (CMS). CMS oversees programs such as Medicare, Medicaid, and other reimbursement policies designed to improve access and equity of healthcare systems \citep{dasari2024opportunities}. They run reporting programs to evaluate hospital performance which hospitals provide by reviewing medical charts and extracting clinical data. However, this process can be time consuming and often requires the effort of medical staff making quality reporting a costly activity \citep{boussina2024large}. Therefore, medical chart data is not merely stored for documentation. Extracting this information is essential for hospital and patient care while also being the backbone for ancillary activities.

Large language models have been used to extract unstructured information from electronic health records (EHR). However, it is well known that large language models struggle to extract long contexts effectively. An article from Chroma found that having larger having context windows with irrelevant context often ends up in worse performance for an LLM \citep{hong2025context}. As additional context was added, particularly within the middle of an LLM’s context, researchers often found the LLMs performance declined substantially. 

Another limitation that comes with using LLM's to extract information is that oftentimes the LLM produces results that are inaccurate or unrelated to the task at hand. One study attempted to store numeric information from LLMs into structured records and used several heruistic-based approaches to reduce hallucinations \citep{adam2025clinical}. These approaches included using a regex-based cross-checking scheme, removing values that were used as examples in few-shot prompting, and removing values outside of a predefined range of plausible values. With these techniques, LLMs were as effective at parsing numeric values as carefully constructed, specific regex expressions, though taking substantially less time. 

Recent work demonstrated that LLMs can successfully extract clinically important information from medical text. In a 2024 study, multiple large language models were evaluated on synthetic clinical notes with all models performing well, and with Claude achieving the best performance \citep{ntinopoulos2025large}. One study uses the annotation of manual reviewers as the ground truth, and found that Chat-GPT performed perfect in some categories, such as detecting smoking and not detecting cancer when not present, but performed worse (58\%) in other categories, such as identifying Family History of Heart Disease \citep{bhagat2024large}. Other studies include humans working alongside LLM's to annotate health data to generate ground truth labels. They found that LLM's helped reviewers work on average 54\% faster. In addition, when the LLM was confident in its answer, it achieved a 96.3\% accuracy, showing that the increase in speed did not come with at a notable loss  \citep{goel2023llms}. These findings indicate that LLMs have are capable of clinical information extraction, however, the effectiveness of these approaches depend on retrieving relevant context from large clinical records. 

Retrieval-augmented generation (RAG) is a common approach used to improve LLM accuracy by retrieving relevant information before model inference. However, evaluation of retrieval strategies and the approaches that may be most optimal for EHR data extraction remain underexplored. To address this gap, this work introduces a benchmark for evaluating information retrieval approaches in medicine by covering a diverse range of methods, metrics, and clinical scenarios. 

We use the MIMIC-III Clinical Database which contains de-identified EHR data for around 46,000 patients from the critical care units of Beth Israel Deaconess Medical Center in Boston between 2001 and 2012 \citep{PhysioNet-mimiciii-1.4}. It includes structured data such as demographics, vital signs, lab results, medications, and diagnostic codes, in which identifying information were de-identified, but we focus on the large collection of unstructured clinical notes present in the data. The NOTEEVENTS table contains a large amount of free text notes that vary in length, structure, and purpose, often written in domain specific language with variability in phrasing and abbreviations. We used the TEXT column from the NOTEEVENTS table, which contains a wide range of unstructured clinical documentation, including nursing notes, physician notes, ECG reports, radiology reports, and discharge summaries \citep{PhysioNet-mimiciii-1.4}. 

This work provides (1) a benchmark for evaluating retrieval approaches on clinical notes, (2) a systematic comparison of retrieval strategies, and (3) insights into which approaches perform best for EHR information extraction.

## Methodology

### Data Preparation
To evaluate LLM performance in a controlled needle-in-a-haystack setting, we utilized version 5.18a of the Centers for Medicare and Medicaid Services (CMS) Specifications Manual for National Hospital Inpatient Quality Measures. In particular, we used the SEP-1 1b-Alpha Data Dictionary (AlphaDD) abstraction specifications to define clinically meaningful queries that served as inputs to the retrieval step for identifying relevant passages \citep{cms_sep1_alphadd}.

We constructed queries representing positive qualifiers based on five elements described in the manual: Administrative Contraindication to Care–Severe Sepsis, Directive for Comfort Care or Palliative Care–Severe Sepsis, Clinical Trial–Severe Sepsis, Severe Sepsis Present, and Vasopressor Administration–Severe Sepsis. Synthetic "needle" statements were generated from these queries using template clinical note patterns described in the CMS documentation. This approach ensured semantic diversity while maintaining clinical validity. Each generated needle satisfied the abstraction logic defined in the manual and provided a definitive response to the associated query, allowing the needles to serve as ground truth evidence for evaluation.

For each patient, all notes contained in the TEXT field were aggregated to create a patient level document representing the clinical "haystack". One synthetic needle corresponding to one of the five clinical elements was then randomly inserted into each patient's aggregated notes. We randomly selected 10,000 patients from the dataset and inserted a needle into each patient for all five elements. This procedure produced long context documents that simulate the review of a full patient chart. This experimental design consisting of a patient level haystack, a query derived from SEP-1 abstraction logic, and a known piece of supporting evidence which is the needle, enabled systematic evaluation of retrieval performance and downstream LLM tasks, including classification and information extraction.

![Pipeline]({{ '/assets/pipeline_2.png' | relative_url }})
<div class="paper-figure">
  <div class="figure-caption">Figure 1: Overview of the LLM-based information extraction pipeline.</div>
</div>

### Retrieval Methods
The retrieval approaches evaluated in this study included Best Matching 25 (BM25), Facebook AI Similarity Search (FAISS), FAISS using Maximal Marginal Relevance (MMR), semantic chunking, Sparse Lexical and Dense (SPLADE), and a hybrid retrieval approach.

BM25 is a widely used lexical retrieval algorithm that ranks documents based on term frequency and inverse document frequency (IDF) \citep{10.1561/1500000019}. While BM25 is effective for exact keyword matching, it often struggles to capture semantic relationships when queries and documents use different terminology.

We also evaluated dense retrieval using FAISS, a vector search library that enables similarity search over high dimensional embeddings \citep{douze2025faisslibrary}. Text passages were converted into vector representations using sentence-transformer embeddings, and similarity between query and passage vectors was computed using both cosine similarity and Euclidean distance. To further improve retrieval diversity, we implemented MMR with FAISS \citep{10.1145/290941.291025}. MMR selects passages that maximize relevance to the query while minimizing redundancy among retrieved passages, thereby increasing coverage of distinct clinical evidence.

In semantic chunking, the haystack was segmented into contextually coherent units. This method detects semantic shifts within the text and groups related sentences into meaningful segments before computing embeddings. By preserving semantic coherence within each chunk, this approach can improve retrieval quality by ensuring that related clinical evidence remains grouped together.

SPLADE is a sparse retrieval model that expands queries and documents into weighted lexical representations using a transformer-based encoder\citep{10.1145/3404835.3463098}. Unlike traditional lexical methods, SPLADE assigns weights to terms based on contextual importance, enabling semantic matching while retaining the efficiency of inverted index search structures. This approach improves lexical recall by allowing retrieval of semantically related terms and synonyms.

Finally, we implemented a hybrid retrieval approach that combines BM25 and FAISS retrieval scores \citep{Hakdagli_2024}. Passage scores from each method were combined using equal weighting, allowing the hybrid approach to leverage the complementary strengths of lexical and semantic retrieval.

To evaluate the necessity of retrieval prior to LLM prompting, we also implemented a baseline approach. The full-context baseline is that the entire patient haystack was provided directly to the LLM without retrieval. Thus, instead of entering the retrieved passages, the entire haystack acts as the passage. Due to model token limitations, this context was truncated to the max token input allowed. The same context-length constraint was applied to all retrieval based prompts to ensure fair comparison across methods. 

### LLM Prompting and Answer Generation
All LLM inference was conducted using AWS Bedrock, which provides a secure environment compliant with HIPAA and institutional data use agreements (DUA). 

We experimented with several models available through Bedrock, including Claude 3 and DeepSeek-V3, and ultimately selected DeepSeek-V3 for the primary experiments due to its favorable balance between cost and performance \citep{deepseekai2025deepseekv3technicalreport}. In the MedHELM benchmark conducted on multiple LLM's, including versions of Claude, GPT, Gemini, and Llama, Deep Seek achieved the highest win-rate of 0.66, which represents the proportion of pairwise comparisons where each model achieved superior performance across all 35  \citep{bedi2025medhelmholisticevaluationlarge}.

For each retrieval strategy, the top-k passages returned by the retriever were used to construct the prompt context. The prompt structure followed a consistent format in which the instruction and the retrieved passages were provided first, followed by the query. 

The LLM was tasked with performing two types of operations: classification and extraction. Classification required the model to determine whether the provided passages contained sufficient information to answer the query. Information extraction required the model to identify and extract the specific clinical evidence corresponding to the query. In the classification task, a positive (“Yes”) response indicates that the model identified evidence relevant to the query. In the extraction task, baseline and retrieval method outputs were compared against the ground-truth synthetic needles to determine whether the correct evidence had been retrieved and extracted.

### Evaluation
Model outputs were evaluated by comparing responses to the known synthetic needles embedded within each patient document. Retrieval effectiveness was assessed by measuring whether the inserted needle appeared within the retrieved passages. LLM performance was evaluated using classification and extraction accuracy across retrieval strategies and clinical elements.


## Results

## Discussion

## Conclusion


</main>

</div>
