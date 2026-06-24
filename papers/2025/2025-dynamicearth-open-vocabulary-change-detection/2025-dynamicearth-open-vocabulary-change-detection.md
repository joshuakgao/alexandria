---
title: "DynamicEarth: How Far are We from Open-Vocabulary Change Detection?"
authors: [Kaiyu Li, Xiangyong Cao, Yupeng Deng, Chao Pang, Zepeng Xin, Deyu Meng, Zhi Wang]
year: 2025
venue: "AAAI 2026"
tags: [change-detection, remote-sensing, vision-language-models]
url: ""
pdf: "[[2025-dynamicearth-open-vocabulary-change-detection.pdf]]"
date_ingested: 2026-06-18
---

# DynamicEarth: How Far are We from Open-Vocabulary Change Detection?

![[2025-dynamicearth-open-vocabulary-change-detection-thumbnail.png]]

## Research gap

DynamicEarth tackles open-vocabulary change detection in earth observation, enabling systems to answer "4W" queries: when, where, what object, and what change has occurred. The paper proposes combining multiple foundation models (SAM, SAM2, CLIP, Grounding DINO) into a Mask-Compare-Identify (M-C-I) framework that leverages their complementary strengths. The mask proposal module generates candidates via multiple methods (SAM, SAM2, Mask2Former, superpixels), the comparator identifies changes between image pairs (class-agnostic change detection), and the identifier grounds changes semantically using CLIP and open-vocabulary encoders. The work bridges the gap between foundation model capabilities and practical change detection by demonstrating that composing multiple models enables open-vocabulary understanding previously unavailable in earth observation systems.

## Contributions

- **Open-vocabulary change detection**: enables describing changes in natural language rather than predefined categories; supports arbitrary change types
- **Foundation model composition**: systematically combines SAM (segmentation), SAM2 (video/temporal), CLIP (semantics), Grounding DINO (open-vocabulary localization); shows that multi-model pipelines outperform single-model approaches
- **Mask-Compare-Identify (M-C-I) framework**: three-stage pipeline:
  1. Mask Proposal: generate candidate masks via multiple methods for robustness
  2. Comparator: identify class-agnostic changes between image pairs (multiple approaches: CVA, ImageDiff, etc.)
  3. Identifier: semantically ground changes using open-vocabulary models
- **"4W" query support**: addresses fundamental earth observation use case—answering who/what/when/where/why questions over satellite imagery
- **Practical applicability**: demonstrates how foundation models can be orchestrated for real-world earth observation tasks

## Method

**Problem formulation:** Given two multi-temporal earth observation images, detect and semantically describe changes.

**Three-stage architecture:**

1. **Mask Proposal Module**: generate candidate object/region masks
   - Options: SAM, SAM2, Mask2Former, superpixel segmentation, class-aware masks (CAM)
   - Goal: diverse, complementary mask proposals to ensure comprehensive coverage

2. **Comparator Module**: identify pixel-level changes between masks across image pair
   - Methods: Change Vector Analysis (CVA), ImageDiff, pixelwise difference
   - Output: binary change masks (changed vs. unchanged)
   - Class-agnostic: no predefined categories

3. **Identifier Module**: semantically ground detected changes
   - Use open-vocabulary models: CLIP, ProxyCLIP, Grounding DINO, Molmo, SegEarth-OV
   - Input: change masks + change descriptions (natural language prompts)
   - Output: semantic change labels and confidence scores

**Key design choice:** decompose the problem into mask generation (segmentation), comparison (change detection), and grounding (semantics) rather than training end-to-end. Leverages specialized foundation models for each stage.

## Datasets & evaluation

**Benchmark evaluation:** (details from abstract and figures)
- Strong performance on earth observation benchmarks
- Supports open-vocabulary queries: users describe changes in natural language
- Demonstrates practical applicability to real-world satellite imagery

**Qualitative results:** system can answer queries like:
- "Where has deforestation occurred?"
- "Show me new building construction"
- "Identify flooded areas"

## Limitations

- **Composition strategy optimization**: how to select which foundation models to include? Paper uses multiple in parallel; unclear if all are necessary or if subsets suffice
- **Semantic drift**: open-vocabulary grounding may introduce false positives (CLIP confusing similar-looking objects); no systematic error analysis
- **Temporal baseline consistency**: change detection requires establishing what "baseline" or "normal" state is; multi-temporal stacks complicate this
- **Computational cost**: composing multiple foundation models (SAM, CLIP, Grounding DINO, etc.) is computationally expensive; deployment efficiency unclear
- **Evaluation metrics**: open-vocabulary change detection lacks standard benchmarks; evaluation methodology still developing
- **Real-world validation**: results on satellite imagery; applicability to other earth observation modalities (hyperspectral, SAR, thermal) unclear
- **Temporal consistency**: addresses single image pair; consistency across image time series unexplored

## Key takeaways

**Related to scene change detection:**
- [[papers/2024-towards-generalizable-scene-change-detection|GeSCF]] addresses change detection in natural scenes; DynamicEarth applies similar ideas to earth observation domain with additional focus on open-vocabulary semantics
- Both leverage foundation models; GeSCF focuses on zero-shot generalization, DynamicEarth on semantic grounding

**Complements anomaly detection:**
- Change detection is a type of anomaly detection (temporal anomaly)
- DynamicEarth's multi-model composition approach is relevant to broader anomaly detection research

**Foundation model orchestration:** represents emerging paradigm where complex tasks are solved by composing foundation models rather than training task-specific architectures.

**Earth observation perspective:**
- Prior work: fixed categories (land cover, urban extent, water)
- DynamicEarth: open-vocabulary, user-defined change types
- Enables new applications: disaster response, environmental monitoring, infrastructure tracking
