---
title: "MMLongBench: Benchmarking Long-Context Vision-Language Models Effectively and Thoroughly"
authors: [Haoning Wang, Haohan Yu, Juntao Ren, Shuai Zhang, Yuping Zhao, Divya Saxena, Jiaheng Cheng, Jianhui Wong, Caifeng See, Pasquale Minervini, Dawn Song, Mark Steedman]
year: 2025
venue: "NeurIPS 2025"
tags: [vision-language-models, benchmarking]
url: ""
pdf: "[[2025-mmlongbench.pdf]]"
date_ingested: 2026-06-18
---

# MMLongBench: Benchmarking Long-Context Vision-Language Models Effectively and Thoroughly

![[2025-mmlongbench-thumbnail.png]]

## Research gap

MMLongBench is the first comprehensive benchmark for evaluating long-context vision-language models (LCVLMs) across a diverse set of tasks. The benchmark comprises 13,331 examples spanning five different task categories: Visual RAG (retrieval-augmented generation), NIAH (needle-in-a-haystack), Many-Shot ICL (in-context learning), Summarization, and Document VQA. A key innovation is the standardized cross-modal tokenization scheme that counts both vision patches and text tokens, enabling evaluation at five standardized input lengths (8K, 32K, 64K, 96K, 128K tokens). This unified length control allows systematic analysis of how model performance scales with context length—a critical capability for long-context VLMs.

## Contributions

- **First comprehensive long-context VLM benchmark**: unified evaluation across five diverse task categories (VRAG, NIAH, ICL, Summarization, DocVQA) rather than single-task focus
- **Standardized cross-modal tokenization**: counts vision patches + text tokens together at five fixed lengths (8K–128K), enabling fair comparison and systematic length robustness analysis
- **Diverse image coverage**: includes both natural images and synthetic images (documents, web pages, screenshots), broader than prior work
- **13,331 examples** across multiple downstream applications with varying complexity
- **Comprehensive model evaluation**: benchmarks 46 models of varying architectures, scales, and training approaches
- **Length robustness control**: all examples designed to be extendable to longer contexts, enabling systematic length scaling studies

## Method


## Datasets & evaluation


## Limitations

- **Semantic contamination in NIAH**: needle-in-haystack tasks may not reflect real-world use cases where information is naturally distributed rather than deliberately hidden
- **Benchmark dataset bias**: examples drawn from existing datasets may not represent all real-world long-context scenarios
- **Limited analysis of failure modes**: benchmark reports accuracy but limited qualitative error analysis to understand what types of information models struggle with
- **Vision encoder contribution unclear**: no ablation isolating vision encoder quality from LLM backbone contribution to long-context performance
- **Task correlation underspecified**: paper notes task performance doesn't correlate, but doesn't provide detailed analysis of which capabilities predict overall long-context performance
- **Generalization to video**: most examples are static images or documents; applicability to long-form video understanding (relevant to video-understanding wiki) is untested
- **Future context lengths**: benchmark maxes at 128K; unclear how methods will scale to 1M+ token contexts as models evolve

## Key takeaways

The benchmark covers diverse image types (both natural and synthetic) and provides comprehensive evaluation of 46 models (both closed-source and open-source). Key findings: (1) performance on individual tasks is a weak proxy for overall long-context capability; (2) both closed-source and open-source LCVLMs face substantial challenges, indicating significant room for improvement; (3) models with stronger reasoning ability exhibit better long-context performance.

**Complements video-understanding research:**
- [[../video-understanding/index|Video Understanding]] wikis emphasize temporal search and frame selection for long-form video; MMLongBench provides a general long-context evaluation framework applicable to video-LVLMs
- Long-context efficiency is a shared concern: video-understanding papers (TimeSearch-R, KVTP, T*) all target reducing computation in long-context scenarios; MMLongBench provides a standardized evaluation setting for such methods

**Positions relative to prior benchmarks:**
- Broader task coverage than MM-NIAH, Visual Haystack, MMNeedle (which focus on NIAH only)
- More diverse than document-specific benchmarks (MMLongBench-Doc, M-Longdoc, LongDocURL)
- Unified length control across both text and vision, unlike prior inconsistent practices
- First to standardize length at both vision token and text token levels simultaneously

**Extends general VLM understanding:**
- [[papers/2025-describe-anything-localized-captioning|DAM]] focuses on localized region captioning; MMLongBench provides broad evaluation of VLM capabilities beyond localized understanding
- Complements work on VLM efficiency: benchmarks large-context capability while separate papers optimize for efficiency
