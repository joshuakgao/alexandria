---
title: "VRAG: Retrieval-Augmented Video Question Answering for Long-Form Videos"
authors: [Bao Tran Gia, Khiem Le, Tien Do, Tien-Dung Mai, Thanh Duc Ngo, Duy-Dinh Le, Shin'ichi Satoh]
year: 2024
venue: "CVPR 2024 Workshop"
tags: [video-understanding, vision-language-models]
url: ""
pdf: "[[2024-vrag-retrieval-augmented-video-qa.pdf]]"
date_ingested: 2026-06-18
---

# VRAG: Retrieval-Augmented Video Question Answering for Long-Form Videos

![[2024-vrag-retrieval-augmented-video-qa-thumbnail.png]]

## Research gap

This paper introduces VRAG, a retrieval-augmented generation (RAG) framework adapted for long-form video question answering. VRAG addresses the computational bottleneck of processing entire long videos by first retrieving only the most relevant segments, then refining through re-ranking and chunking before answer generation. The framework combines RAG principles from NLP with multimodal video retrieval, leveraging semantic embeddings, audio transcripts, on-screen text, and object detection to retrieve candidates. A re-ranking module incorporates temporal context by expanding shots with adjacent frames and scoring relevance via MLLM evaluation. The VQA module then applies filtering and aggregation to generate context-aware responses. Evaluated on the Video Browser Showdown (VBS) 2019-2025 dataset, VRAG achieves strong results on both Known-Item Search (40.5/45) and VQA tasks (4/5).

## Contributions

- Applies RAG paradigm from NLP to video domain, showing retrieval-first approach can reduce computational overhead while maintaining coherence
- Multi-modal retrieval system combining semantic similarity (InternVL-G, BLIP-2, BEiT), audio transcription (Whisper), OCR (DeepSolo, PARSeq), and object detection (Co-DETR)
- Re-ranking module that incorporates temporal context by expanding candidate shots with adjacent frames and scoring via MLLM evaluation
- Two-stage VQA module: Filtering (identifies relevant segments) + Answering (aggregates information for response generation)
- Demonstrates effectiveness on long-form video understanding without end-to-end processing of all frames

## Method

VRAG comprises three main stages:

1. **Multimodal Search**: Retrieves top-N candidate segments using late-fusion at shot level across multiple modalities: semantic (vision-language embeddings), audio (automatic speech recognition), text (OCR), and object constraints
2. **Re-ranking Module**: Expands retrieved shots by including ±3 adjacent shots to preserve temporal continuity, uses MLLM to score relevance (0-1 scale), and selects top-K most relevant segments
3. **VQA Module**: For VQA queries, GPT-4o generates a retrieval query from the question. Selected segments undergo filtering (to identify answer-relevant content) and answering (MLLM aggregates across segments to generate coherent, context-aware responses)

The retrieval-first approach enables processing only pertinent content, optimizing both computational efficiency and answer accuracy.

## Datasets & evaluation

- **Known-Item Search (KIS)**: 40.5/45 retrieval score on VBS benchmark
- **Video Question Answering**: 4/5 performance on VBS-derived VQA queries
- Demonstrates significant improvements in retrieval precision and answer quality compared to end-to-end processing baselines
- Shows effectiveness of RAG-inspired retrieval on long-form videos where direct context processing is infeasible

## Limitations

- Scalability and performance on videos longer than VBS benchmark range
- Sensitivity of re-ranking to temporal window size (currently fixed at ±3 shots) — whether adaptive window sizing could improve results
- Trade-off between retrieval-augmented efficiency and potential information loss from not processing all frames
- Generalization across diverse video types and question complexities beyond VBS dataset
- Computational cost of multimodal search across 5+ modalities in real-time scenarios

## Key takeaways

Extends RAG from NLP (DPR, ColBERT, RETRO) to video domain. Related to concurrent temporal search approaches (AKEYS, TimeSearch-R) but differs in focusing on explicit retrieval-augmented pipeline rather than agent-guided or RL-based search. Shares multi-modal fusion strategies with other video-LLM work but emphasizes retrieval efficiency and temporal coherence through re-ranking.
