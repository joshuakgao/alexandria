---
title: "LISA: Reasoning Segmentation via Large Language Model"
authors: [Xin Lai, Zhuotao Tian, Yukang Chen, Yanwei Li, Yuhui Yuan, Shu Liu, Jiaya Jia]
year: 2024
venue: "CVPR 2024"
tags: [vision-language-models, segmentation]
url: ""
pdf: "[[2024-lisa-reasoning-segmentation.pdf]]"
date_ingested: 2026-06-18
---

# LISA: Reasoning Segmentation via Large Language Model

![[2024-lisa-reasoning-segmentation-thumbnail.png]]

## Research gap

LISA introduces the reasoning segmentation task, requiring models to output segmentation masks for complex, implicit textual queries that demand reasoning beyond explicit object categories. Unlike traditional segmentation relying on explicit instructions or pre-defined categories, LISA enables active reasoning and comprehension of implicit user intention. The model inherits language generation capabilities from multimodal LLMs while producing segmentation masks through an embedding-as-mask paradigm. LISA expands the vocabulary with a special <SEG> token and demonstrates robust zero-shot capability when trained exclusively on reasoning-free datasets. Fine-tuning with just 239 reasoning segmentation samples significantly improves performance, while the authors establish a benchmark with over 1,000 image-instruction-mask samples incorporating complex reasoning and world knowledge.

## Contributions

- Introduces reasoning segmentation task requiring implicit query understanding and world knowledge integration
- Proposes embedding-as-mask paradigm enabling segmentation output from language models without architectural modification
- Demonstrates strong zero-shot generalization on reasoning tasks despite training on reasoning-free data
- Shows that minimal fine-tuning (239 samples) can substantially improve reasoning segmentation performance

## Method

Language model with special <SEG> token whose embeddings directly encode segmentation information. The embedding-as-mask approach allows seamless integration of segmentation with text generation without requiring separate segmentation heads or architectural modifications.

## Datasets & evaluation

Strong performance on reasoning-based segmentation tasks, with particular strength in zero-shot generalization and ability to handle complex implicit queries requiring world knowledge and reasoning.

## Limitations

Reasoning segmentation benchmark is relatively small (1000 samples); larger-scale benchmarks needed. Embedding-as-mask approach may have limitations for fine-grained spatial prediction. Trade-offs between reasoning capability and segmentation precision not fully explored.

---

## Key takeaways

Extends LLaVA-style vision-language models to dense prediction tasks, introducing reasoning as a key component beyond traditional segmentation. Demonstrates language models' capability for pixel-level prediction tasks.
