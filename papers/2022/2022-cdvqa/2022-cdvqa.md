---
title: "Change Detection Meets Visual Question Answering"
authors: [Zhenghang Yuan, Student Member, Lichao Mou, Zhitong Xiong, Xiao Xiang Zhu]
year: 2022
venue: "IEEE Transactions on Geoscience and Remote Sensing"
tags: [change-detection, remote-sensing]
url: ""
pdf: "[[2022-cdvqa.pdf]]"
date_ingested: 2026-06-18
---

# Change Detection Meets Visual Question Answering

![[2022-cdvqa-thumbnail.png]]

## Research gap

This paper introduces Change Detection-based Visual Question Answering (CDVQA), a novel task that combines change detection with visual question answering on multi-temporal aerial images. Rather than producing change maps or captions, CDVQA allows users to ask natural language questions about changes between bitemporal images and receive direct answers — making change detection information accessible to non-expert end users.

## Contributions

- Formalization of the CDVQA task — the first work to combine change detection with VQA for multi-temporal remote sensing images
- A CDVQA dataset with 2,968 image pairs and 122,000+ QA pairs, automatically generated from semantic change maps with five question types
- A baseline framework with multi-temporal encoding, multi-temporal/multi-modal fusion, and a change enhancing module
- Systematic study of backbone architectures (ResNet-18/34/50/101, VGG-16) and multi-temporal fusion strategies (concatenation, subtraction, combined)

## Method

**Multi-temporal Feature Encoding:** Siamese CNN (shared weights) extracts features from both temporal images independently. A change enhancing module computes the absolute difference between feature maps to generate a change attention map, which is applied to the original features to emphasize change-relevant regions.

**Multi-temporal Fusion:** Three strategies investigated — concatenation (preserving both features), subtraction (explicit change signal), and concatenation + subtraction (both). Experiments show that the combined strategy generally performs best.

**Multi-modal Fusion:** Question text is encoded via GRU, and the visual feature vector is fused with the question feature via element-wise multiplication.

**Answer Prediction:** Two FC layers with ReLU predict the answer as a classification task over a fixed answer vocabulary.

**Dataset Construction:** Questions and answers are automatically generated from pixel-level semantic change maps of the SECOND dataset. For each image pair, the semantic labels at T1 and T2 are compared to generate five types of QA pairs covering binary change, directional change, change type, extremal change, and change ratio.

## Datasets & evaluation

- ResNet-18 backbone achieves best overall accuracy (68.0%) with the combined fusion strategy, suggesting that simpler encoders suffice for this task
- The change enhancing module consistently improves performance across all configurations
- Combined fusion (concatenation + subtraction) outperforms either alone, confirming that both preservation and differencing of temporal features are valuable
- Performance varies by question type: yes/no questions are easiest; change ratio questions are hardest
- Two test splits evaluate in-distribution (Test Set 1) and out-of-distribution (Test Set 2) generalization

## Limitations

- The dataset is automatically generated from semantic change maps, which limits question diversity to template-based patterns; human-authored questions could improve naturalness
- The answer vocabulary is fixed and small; open-ended generation of answers is not supported
- Only six land cover classes are covered; generalization to more diverse change types and geographic regions is untested
- The baseline framework uses relatively simple fusion (element-wise multiplication); more sophisticated multi-modal reasoning (e.g., cross-attention, transformer-based fusion) could improve performance
- Spatial grounding is absent — the model answers questions about changes but cannot point to where in the image the change occurs

## Key takeaways

The authors build a CDVQA dataset from the SECOND semantic change detection dataset, containing 2,968 pairs of aerial images with over 122,000 automatically generated question-answer pairs. Five question types are designed to cover different granularities of change information: (1) change or not (yes/no), (2) increase/decrease or not (yes/no), (3) change to what (land cover class), (4) largest/smallest change (land cover class), and (5) change ratio (percentage range). Questions are auto-generated using pixel-level semantic change maps, ensuring answer correctness.

The baseline CDVQA framework has four components: a multi-temporal feature encoder (Siamese CNN), multi-temporal fusion (concatenation, subtraction, or both), multi-modal fusion (element-wise multiplication of visual and question features), and answer prediction (MLP classifier). A change enhancing module generates change attention maps from feature differences to weight the visual features toward change-relevant regions.

CDVQA bridges the gap between traditional change detection (which produces maps for experts) and natural language interfaces (which are accessible to end users). It extends remote sensing VQA (RSVQA, Lobry et al.) from single-image to multi-temporal settings. Compared to change captioning approaches (Hoxha et al., Liu et al., later Chg2Cap), CDVQA is interactive — users specify what they want to know via questions rather than receiving a fixed caption. The change enhancing module is conceptually related to attention mechanisms in change detection networks but applied in a VQA context. This work preceded and influenced later multi-modal change understanding approaches including text-conditioned change detection (ViewDelta) and generalist image difference captioning (OneDiff).
