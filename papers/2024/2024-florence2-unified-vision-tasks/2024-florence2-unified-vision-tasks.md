---
title: "Florence-2: Advancing a Unified Representation for a Variety of Vision Tasks"
authors: [Bin Xiao, Haiping Wu, Weijian Xu, Xiyang Dai, Houdong Hu, Yumao Lu, Michael Zeng, Ce Liu, Lu Yuan]
year: 2024
venue: "CVPR"
tags: [vision-language-models]
url: ""
pdf: "[[2024-florence2-unified-vision-tasks.pdf]]"
date_ingested: 2026-06-18
---

# Florence-2: Advancing a Unified Representation for a Variety of Vision Tasks

![[2024-florence2-unified-vision-tasks-thumbnail.png]]

## Research gap

Florence-2 is a novel vision foundation model with a unified, prompt-based representation capable of handling diverse computer vision and vision-language tasks using text-prompt instructions. Unlike existing large vision models that excel in transfer learning but struggle with task diversity, Florence-2 generates results in text form for various tasks (captioning, object detection, grounding, segmentation) through a unified interface. The researchers co-developed FLD-5B, a massive dataset of 5.4 billion comprehensive visual annotations on 126 million images, using iterative automated annotation and model refinement, covering multiple spatial hierarchies and semantic granularities through 500 million text annotations, 1.3 billion region-text annotations, and text-phrase-region triplets.

## Contributions

- Introduces unified prompt-based interface for diverse vision and vision-language tasks with text-based outputs
- Creates FLD-5B, the largest vision-language annotation dataset with 5.4B annotations across multiple granularities
- Demonstrates ability to handle tasks with varying spatial hierarchy and semantic complexity in a single model
- Achieves strong performance on both traditional and emerging vision-language tasks

## Method

Vision foundation model with prompt-based task specification, trained on FLD-5B with iterative refinement of automated annotations to improve quality and coverage across diverse tasks and granularities.

## Datasets & evaluation

Strong performance across object detection, image captioning, region grounding, and semantic segmentation tasks, with particular strength in dense prediction and fine-grained visual understanding.

## Limitations

Unified approach may sacrifice task-specific optimization compared to specialized models. FLD-5B annotations, though massive, still represent subset of possible visual understanding tasks. Trade-offs between prompt-based flexibility and task-specific performance not fully explored.

---

## Key takeaways

Extends beyond CLIP's image-text alignment to create a unified framework for diverse vision tasks, building on recent trends toward unified vision-language models while introducing the massive FLD-5B dataset.
