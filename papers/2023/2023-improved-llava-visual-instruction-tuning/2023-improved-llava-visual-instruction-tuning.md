---
title: "Improved Baselines with Visual Instruction Tuning"
authors: [Haotian Liu, Chunyuan Li, Yuheng Wu, Yong Jae Lee]
year: 2023
venue: "NeurIPS"
tags: [vision-language-models]
url: ""
pdf: "[[2023-improved-llava-visual-instruction-tuning.pdf]]"
date_ingested: 2026-06-18
---

# Improved Baselines with Visual Instruction Tuning

![[2023-improved-llava-visual-instruction-tuning-thumbnail.png]]

## Research gap

LLaVA-1.5 presents improved baselines for visual instruction tuning, demonstrating that the fully-connected vision-language connector in the original LLaVA is surprisingly powerful and data-efficient. By making simple architectural modifications—specifically using CLIP-ViT-L-336px with an MLP projection layer and incorporating academic task-oriented VQA data—the authors establish stronger baselines that achieve state-of-the-art performance across 11 different benchmarks. The final 13B model uses only 1.2M publicly available data and completes full training in approximately one day on a single 8-A100 node, surpassing significantly larger models trained on billion-scale data.

## Contributions

- Demonstrates that simple architectural modifications to LLaVA significantly boost performance on multiple benchmarks
- Introduces LLaVA-1.5-HD with high-resolution image support through intelligent grid-based encoding that preserves both local and global context
- Shows data efficiency and training speed advantages compared to models trained on massive proprietary datasets
- Achieves state-of-the-art results on 11 visual understanding and reasoning benchmarks

## Method

Uses CLIP-ViT-L-336px as the vision encoder connected via an MLP projection to the LLaMA language model backbone. For high-resolution images, employs a grid-based approach that divides images into patches, encodes them independently, and concatenates their features with a downsampled global image representation for context preservation.

## Datasets & evaluation

Achieves SoTA performance on image captioning, visual question answering, and reasoning benchmarks, while using significantly less training data (1.2M vs billions) and training time (1 day vs weeks) than competing approaches.

## Limitations

Relies on public instruction-tuning datasets which may have quality and diversity limitations. High-resolution extension using grid approach may not optimally handle very large images or preserve fine-grained spatial relationships in all cases.

---

## Key takeaways

Improves upon the original LLaVA architecture (2304.08485) with simpler, more efficient design choices while maintaining superior performance. Demonstrates that the field was over-parameterized and that careful engineering can outperform brute-force scaling.
