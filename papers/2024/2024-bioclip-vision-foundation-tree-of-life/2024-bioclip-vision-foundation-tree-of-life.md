---
title: "BioCLIP: A Vision Foundation Model for the Tree of Life"
authors: [Samuel Stevens, Jiaman Wu, Matthew Thompson, Elizabeth Campolongo, Chan Hee Song, David Forsyth, Tanya Berger-Wolf, Wei-Lun Chao, Yu Su]
year: 2024
venue: "CVPR"
tags: [vision-language-models]
url: ""
pdf: "[[2024-bioclip-vision-foundation-tree-of-life.pdf]]"
date_ingested: 2026-06-18
---

# BioCLIP: A Vision Foundation Model for the Tree of Life

![[2024-bioclip-vision-foundation-tree-of-life-thumbnail.png]]

## Research gap

BioCLIP is a specialized vision foundation model for organismal biology, applying CLIP architecture to fine-grained species classification across the tree of life. Built on TreeOfLife-10M, a specially-curated dataset covering over 450K taxa and representing the most biologically diverse ML-ready dataset available, BioCLIP achieves substantial improvements (17-20% absolute) over existing baselines on diverse fine-grained biology classification tasks. The model learns hierarchical representations conforming to the biological taxonomy, providing strong generalizability across tasks. The biologically-informed approach demonstrates how domain-specific vision-language models can excel in specialized classification while learning meaningful biological hierarchies, representing a significant advancement in applying computer vision to organismal biology.

## Contributions

- First vision foundation model specifically designed for organismal biology and tree-of-life classification
- Creates TreeOfLife-10M dataset covering 450K+ taxa, most biologically diverse dataset of its kind
- Demonstrates learning of hierarchical representations aligned with biological taxonomy
- Achieves 17-20% absolute improvements over baselines on fine-grained biology classification

## Method

ViT-B/16 vision transformer pre-trained with CLIP objective on TreeOfLife-10M dataset combining iNat21, BIOSCAN-1M, and Encyclopedia of Life (EOL) data with careful cleaning and curation.

## Datasets & evaluation

State-of-the-art performance on fine-grained biology classification tasks with strong zero-shot and few-shot generalization capabilities, and learned representations that align with biological taxonomy.

## Limitations

Specialization to biology domain may limit transfer to non-biological tasks. Dataset quality depends on availability of labeled biological images and expert taxonomy, which may be sparse for some taxa. TreeOfLife-10M's completeness and representativeness of all biological diversity not exhaustively validated.

---

## Key takeaways

Applies CLIP framework to specialized domain of biology, demonstrating how domain-specific datasets and objectives can improve foundation models for particular application areas. Extends vision-language models beyond general natural images.
