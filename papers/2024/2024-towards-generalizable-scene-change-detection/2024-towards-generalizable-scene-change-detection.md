---
title: "Towards Generalizable Scene Change Detection"
authors: [Jae-Woo Kim, South Korea]
year: 2024
tags: [change-detection, segmentation]
url: ""
pdf: "[[2024-towards-generalizable-scene-change-detection.pdf]]"
date_ingested: 2026-06-18
---

# Towards Generalizable Scene Change Detection

![[2024-towards-generalizable-scene-change-detection-thumbnail.png]]

## Research gap

GeSCF (Generalizable Scene Change Detection Framework) tackles the critical generalization problem in scene change detection: models achieving high performance on training data fail catastrophically on unseen environments (77.6% → 8.0% drop). The framework addresses this via zero-shot adaptation of Segment Anything Model (SAM) without task-specific training. Key innovations include Initial Pseudo-mask Generation and Geometric-Semantic Mask Matching that convert SAM's single-image segmentation capability into pair-wise change detection. Additionally, GeSCF introduces the Generalizable Scene Change Detection (GeSCD) benchmark with novel metrics for cross-domain evaluation and temporal consistency, and contributes the ChangeVPR dataset spanning diverse environments (urban, suburban, rural). Results show 19.2% improvement on existing benchmarks and 30.0% on ChangeVPR.

## Contributions

- **First zero-shot scene change detection method**: adapts SAM for change detection without task-specific training; eliminates domain-specific fine-tuning
- **Initial Pseudo-mask Generation**: leverages SAM's feature space localization to binarize pixel-level change candidates with zero additional cost
- **Geometric-Semantic Mask Matching**: refines change masks using both geometric properties of SAM's class-agnostic masks and semantic embeddings; incorporates object-level information
- **Complete temporal consistency**: ensures change masks are consistent regardless of input image order; addresses practical issue of prior SCD methods failing under reversed input
- **GeSCD benchmark**: novel evaluation protocol for generalizability; cross-domain evaluation and temporal consistency quantification
- **ChangeVPR dataset**: 1000+ image pairs from three Visual Place Recognition datasets; diverse scenarios (urban, suburban, rural) with challenging conditions
- **Substantial performance gains**: 19.2% improvement on existing SCD datasets; 30.0% on ChangeVPR (nearly 2× prior art)

## Method

**Problem formulation:** Scene change detection requires identifying semantic and structural changes between two image pairs, robust to domain shift and temporal inconsistency.

**Zero-shot adaptation of SAM:**
1. **Initial Pseudo-mask Generation**: analyze SAM's localized class-agnostic masks; use feature space analysis to identify change-relevant pixels without additional training
2. **Mask pair generation**: apply SAM to both input images separately to generate candidate masks
3. **Geometric-Semantic Matching**: match masks across image pairs using:
   - Geometric properties: spatial overlap, shape consistency
   - Semantic properties: embedding similarity from mask features
4. **Change mask refinement**: aggregate matches into final change mask; incorporate object-level confidence

**Temporal consistency:** design ensures change(image1, image2) ≈ change(image2, image1), critical for practical deployments where image order is arbitrary.

**Training-free approach:** no gradient updates; all operations rely on SAM's frozen weights and geometric reasoning.

## Datasets & evaluation

**Existing datasets (SCD benchmarks):**
- 19.2% average improvement over prior SOTA
- Strong performance on multiple domains

**ChangeVPR dataset:**
- 30.0% improvement over prior art; nearly 2× performance gain
- Reveals that standard SCD models degrade severely; GeSCF remains robust

**Cross-domain evaluation:**
- Consistent performance across urban, suburban, rural environments
- No retraining required; zero-shot capability validated

**Temporal consistency:**
- Prior methods: inconsistent masks when image pair order reversed
- GeSCF: guaranteed consistency via geometric matching

## Limitations

- **Performance gap to fine-tuned models**: zero-shot GeSCF still lags fine-tuned methods on some benchmarks by 2–3%; trade-off between generality and task-specific performance unresolved
- **SAM-specific design**: method heavily relies on SAM's architecture and capabilities; applicability to other foundation models unclear
- **Semantic change types**: focuses on structural/geometric changes visible in pixels; semantic changes (e.g., object category change without visual difference) not addressed
- **Prompt engineering**: while zero-shot, method requires careful mask matching heuristics; sensitivity to hyperparameter choices (thresholds, matching criteria) not fully analyzed
- **Video change detection**: addresses image pairs; temporal dynamics in video sequences (consecutive frames, motion) unexplored
- **Lighting and seasonal changes**: evaluated on dataset with illumination variations; systematic analysis of robustness to weather, season, time-of-day missing

## Key takeaways

**Positions relative to traditional SCD:**
- Prior SCD methods: train task-specific models on limited datasets; fail on unseen domains
- GeSCF: leverages pre-trained SAM; zero-shot generalization

**Foundation model approach:**
- Aligns with broader trend of zero-shot vision models (CLIP, SAM, etc.)
- Complements anomaly detection work: demonstrates SAM adaptability across tasks

**Benchmark contribution:**
- Prior benchmarks: single-domain evaluation; no generalization assessment
- GeSCD: cross-domain and temporal consistency; establishes new evaluation standard

**Related to change detection in earth observation:**
- [[papers/2025-dynamicearth-open-vocabulary-change-detection|DynamicEarth]] addresses similar problem in satellite imagery; both leverage foundation models but focus on different domains and task formulations
