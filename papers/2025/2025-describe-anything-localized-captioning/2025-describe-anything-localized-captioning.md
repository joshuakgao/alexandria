---
title: "Describe Anything: Detailed Localized Image and Video Captioning"
authors: [Long Lian, Yifan Ding, Ming-Yu Liu, Trevor Darrell]
year: 2025
venue: "ICCV"
tags: [vision-language-models, image-captioning, video-understanding]
url: ""
pdf: "[[2025-describe-anything-localized-captioning.pdf]]"
date_ingested: 2026-06-18
---

# Describe Anything: Detailed Localized Image and Video Captioning

![[2025-describe-anything-localized-captioning-thumbnail.png]]

## Research gap

The Describe Anything Model (DAM) addresses the challenge of generating detailed and accurate descriptions for specific regions in images and videos, combining high-resolution local detail with global context. DAM introduces two key innovations: focal prompts ensuring high-resolution encoding of targeted regions, and a localized vision backbone integrating precise localization with broader context. To overcome the scarcity of high-quality detailed localized captioning (DLC) data, the authors propose a Semi-supervised Learning-based Data Pipeline (DLC-SDP) that expands existing segmentation datasets to unlabeled web images. The paper introduces DLC-Bench, a benchmark for evaluating DLC without reference captions, and demonstrates state-of-the-art results on 7 benchmarks spanning keyword-level, phrase-level, and detailed multi-sentence localized image and video captioning.

## Contributions

- Introduces focal prompt mechanism for high-resolution region encoding in vision-language models
- Proposes localized vision backbone architecture integrating precise localization with context preservation
- Develops semi-supervised data pipeline expanding limited DLC datasets to web scale
- Creates DLC-Bench evaluation benchmark for reference-free detailed localized captioning assessment

## Method

Vision-language model with focal prompts and localized vision backbone, trained with semi-supervised learning pipeline that leverages existing segmentation data and unlabeled web images to scale training data.

## Datasets & evaluation

State-of-the-art performance on 7 localized captioning benchmarks including keyword-level, phrase-level, and detailed multi-sentence descriptions for both images and videos.

## Limitations

Semi-supervised approach depends on quality of unlabeled data. Focal prompt mechanism adds complexity to inference. Trade-offs between local detail preservation and global coherence not exhaustively explored. Generalization to diverse image domains beyond web data not fully evaluated.

---

## Key takeaways

Extends vision-language models to region-level description generation with focus on detail and locality, building on instruction-tuning approaches while addressing specific challenge of maintaining local precision alongside global context.
