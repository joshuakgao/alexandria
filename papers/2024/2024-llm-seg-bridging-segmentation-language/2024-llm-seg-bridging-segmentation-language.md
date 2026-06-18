---
title: "LLM-Seg: Bridging Image Segmentation and Large Language Model Reasoning"
authors: [Junchi Wang, Lei Ke, Zheng Peng, Mingyu Wang]
year: 2024
venue: "CVPR Workshop"
tags: [vision-language-models, segmentation, synthetic-datasets]
url: ""
pdf: "[[2024-llm-seg-bridging-segmentation-language.pdf]]"
date_ingested: 2026-06-18
---

# LLM-Seg: Bridging Image Segmentation and Large Language Model Reasoning

![[2024-llm-seg-bridging-segmentation-language-thumbnail.png]]

## Research gap

LLM-Seg introduces reasoning segmentation, a task where segmentation systems reason about implicit user intention through LLM reasoning before generating target segmentation masks. The framework effectively connects the Segment Anything Model (SAM) with LLMs through intelligent mask proposal selection, leveraging LLM reasoning to identify which of SAM's proposals correspond to the user's intent. The authors propose an automatic data generation pipeline and construct LLM-Seg40K, a new reasoning segmentation dataset. The approach demonstrates competitive performance with existing methods and shows the proposed pipeline can efficiently produce high-quality reasoning segmentation datasets, establishing LLM-Seg40K as a benchmark for training and evaluating reasoning segmentation approaches.

## Contributions

- Introduces reasoning segmentation combining SAM's mask generation with LLM reasoning for implicit query understanding
- Proposes efficient mask proposal selection mechanism bridging segmentation models and language reasoning
- Creates automatic data generation pipeline and LLM-Seg40K benchmark for reasoning segmentation
- Demonstrates competitive performance while efficiently generating high-quality training data

## Method

Two-stage pipeline: (1) SAM generates multiple mask proposals, (2) LLM reasons about implicit queries and selects appropriate proposals through cross-modal reasoning about textual descriptions and visual content.

## Datasets & evaluation

Competitive performance on reasoning segmentation tasks, with efficient data generation pipeline capable of producing high-quality datasets for further research.

## Limitations

Approach depends on SAM's proposal quality which may vary across scenarios. LLM-Seg40K dataset, while useful, is relatively small compared to other vision-language datasets. Efficiency of mask selection vs. full end-to-end approaches not thoroughly compared.

---

## Key takeaways

Similar motivation to LISA's reasoning segmentation but uses explicit mask proposal mechanism (SAM) rather than embedding-as-mask approach. Shows complementary approach to dense prediction with reasoning.
