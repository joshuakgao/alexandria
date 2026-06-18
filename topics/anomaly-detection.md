---
topic: Anomaly Detection
slug: anomaly-detection
---

# Anomaly Detection

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "anomaly-detection")
SORT year DESC
```

## Overview

# Anomaly Detection — Overview

*Last updated: 2026-04-07 | Sources: 4 papers*

Visual anomaly detection has evolved through three distinct paradigms: (1) **memory-bank and feature-matching approaches** (PatchCore) that leverage pretrained features with neighborhood aggregation and coreset subsampling for efficiency, (2) **reconstruction-based and self-supervised approaches** (RIAD) that use masked inpainting and feature distillation, and (3) **generative and synthesis-driven methods** (DeSTSeg, AnomalyAny) that improve training via synthetic anomalies and diffusion-based test-time augmentation. The convergence across papers is that pretrained features (e.g., ImageNet-initialized ResNets) are powerful when used without task-specific adaptation, but the composition strategy—how to extract, aggregate, and match features—defines success. Recent work pushes toward zero-shot and synthetic-data-driven approaches, signaling a shift away from task-specific training toward more general, parameter-efficient methods.

## Key trends

**2021:**
- **Two competing paradigms emerge: memory-bank vs. reconstruction-based approaches.** PatchCore demonstrates that a simple memory bank of mid-level patch-level features with nearest-neighbor matching achieves state-of-the-art results (99.6% AUROC on MVTec AD). Simultaneously, RIAD shows that masked inpainting—forcing reconstruction from context only—outperforms full-image reconstruction. Both avoid task-specific training, establishing that pretrained features are powerful enough for industrial anomaly detection without adaptation.
- **Feature matching and aggregation become critical.** PatchCore's locally-aware patch aggregation (pooling over neighborhoods) proves essential for balancing receptive field size and spatial resolution while reducing ImageNet bias. Coreset subsampling (using minimax facility location) enables efficient nearest-neighbor search at scale, demonstrating that algorithmic efficiency is achievable without sacrificing accuracy.
- **Spatial localization is a solved subproblem.** Both PatchCore and RIAD naturally provide pixel-level anomaly segmentation maps; the challenge shifts from localization itself to ensuring localization quality in diverse defect types (fine scratches vs. large structural defects).

**2023:**
- **Student-teacher frameworks mature for anomaly detection.** DeSTSeg advances the S-T paradigm by introducing denoising student encoders (trained on synthetic corruptions) and adaptive feature fusion via segmentation networks. Achieves 98.6% image-level AUC on MVTec AD, establishing a new baseline.
- **Multi-level feature aggregation becomes learnable.** Prior work used empirical aggregation (sum/product); DeSTSeg replaces it with a segmentation network, enabling task-specific feature weighting.
- **Parallel research directions:** RIAD's reconstruction-based approach and DeSTSeg's student-teacher approach represent different solutions to anomaly detection; both achieve strong results, establishing that no single paradigm dominates.

**2025:**
- **Diffusion models enter test-time anomaly generation.** AnomalyAny leverages Stable Diffusion for test-time synthesis without training, using single-sample conditioning and text descriptions. Shifts anomaly generation from fixed patterns (cut-and-paste, textures) to open-ended, description-driven synthesis.
- **Generative models offer multiple entry points.** RIAD uses masked inpainting (2021), DeSTSeg uses synthetic corruption (2023), AnomalyAny uses diffusion-based generation (2025). Each addresses data scarcity differently, suggesting the field is exploring diverse generative approaches rather than converging on one.

## Open problems

- **Synthetic anomaly realism**: AnomalyAny generates diverse anomalies, but no systematic validation of whether synthetic anomalies reflect the distribution of real-world anomalies; potential domain gap between generation and downstream detection
- **Generalization without task-specific supervision**: GeSCF demonstrates zero-shot capability on scene change, but performance still lags trained models; unclear how much performance gap is fundamental vs. due to under-optimized prompting
- **Multi-task anomaly detection**: current work addresses single anomaly types (surface defects, scene changes, earth observation); joint detection of multiple anomaly classes from a single model remains unexplored
- **Temporal anomaly detection**: all papers address single-pair or image-level anomalies; video-level anomaly detection spanning temporal sequences is absent
- **Foundation model selection and composition**: DynamicEarth uses multiple foundation models (SAM, CLIP, Grounding DINO); no principled guidance on which models to combine or how to weight their outputs
- **Evaluation metrics for generalization**: GeSCD introduces novel metrics for temporal consistency and cross-domain performance, but no consensus on how to fairly compare zero-shot vs. fine-tuned models

## Contradictions and debates

- **Data scarcity as motivation vs. data abundance in practice**: AnomalyAny and DeSTSeg are motivated by the claim that real-world anomalies are rare and hard to collect. However, industrial inspection datasets (MVTec AD, VisA) now have extensive normal + anomaly samples. The degree to which data scarcity remains a real bottleneck vs. a motivation artifact is unclear.
- **Synthetic vs. real anomalies**: AnomalyAny generates synthetic anomalies to improve detection; DeSTSeg also uses synthetic corruptions. But both are evaluated on real-world test sets, raising the question: do synthetic anomalies sufficiently represent the distribution of real anomalies encountered in deployment?
- **Zero-shot vs. fine-tuned trade-off**: GeSCF achieves 19.2% improvement over baselines without training, but absolute performance is still 2–3% below fine-tuned methods on some benchmarks. The trade-off between generality and task-specific performance remains unresolved.
- **Localization vs. detection**: DeSTSeg focuses on localization (pixel/instance-level AUC); GeSCF and DynamicEarth focus on change masks. Whether localization precision is necessary or sufficient for downstream applications (e.g., repair guidance) is task-dependent but not formalized.

## Recommended reading order

1. [[papers/2021-patchcore|PatchCore (2021)]] — the memory-bank paradigm: demonstrates that mid-level pretrained features with neighborhood aggregation and efficient nearest-neighbor search achieves near-perfect results; shows feature matching is simpler and more effective than generative approaches; essential foundation
2. [[papers/2021-reconstruction-by-inpainting-anomaly-detection|RIAD (2021)]] — the reconstruction-based paradigm: complements PatchCore by showing masked inpainting forces better anomaly detection than full-image reconstruction; different approach, similar philosophy (avoid task-specific training). Read alongside PatchCore to understand the two 2021 paradigms.
3. [[papers/2023-destseg-anomaly-detection|DeSTSeg (2023)]] — the student-teacher evolution: combines inpainting (RIAD-style) with synthetic corruptions and learnable feature fusion; achieves higher absolute performance on MVTec AD; shows how student-teacher frameworks improve both 2021 approaches
4. [[papers/2025-unseen-visual-anomaly-generation|AnomalyAny (2025)]] — the generative frontier: leverages diffusion models for test-time anomaly synthesis without training; demonstrates that modern generative models can improve any base detector; represents where the field is heading

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
