---
topic: Point Tracking
slug: point-tracking
---

# Point Tracking

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "point-tracking")
SORT year DESC
```

## Overview

# Point Tracking — Overview

*Last updated: 2026-06-19 | Sources: 2 papers*

## Current thesis

Real-world point tracking faces a fundamental reliability problem: different trackers excel in different regimes, and no fixed heuristic can reconcile their heterogeneous error profiles. Rather than designing new tracker architectures, recent work focuses on **learned reliability estimation**—a verifier meta-model that learns when and where to trust different trackers. Verifier (2026) demonstrates that synthetic-trained reliability cues transfer to real videos, enabling adaptive pseudo-label selection and ensemble weighting that reduces error propagation during real-world adaptation. This suggests point tracking is converging on learned ensemble strategies that exploit complementary strengths of off-the-shelf models rather than single monolithic trackers. Concurrently, point tracking is becoming a critical enabling technology for downstream domains: PointWorld (Huang et al., CVPR 2026) uses CoTracker3 to lift 2D tracks to 3D point flows for large-scale robotic manipulation data annotation, demonstrating that point tracking quality directly determines the quality of 3D world model training data.

## Key trends

**2026:**
- **Learned reliability scoring over fixed heuristics.** Verifier shows that global confidence thresholds and fixed weighting schemes fail to capture per-frame, per-tracker reliability variation. A learned meta-model trained on synthetic perturbations recognizes consistency cues that transfer to real errors (drift, jumps, occlusions), enabling adaptive selection.
- **Synthetic-to-real transfer via perturbation.** Training Verifier on deliberately corrupted synthetic trajectories (mimicking real failure modes) teaches it to recognize which predictions are trustworthy, without requiring real annotations. This cheaply-annotated synthetic supervision is sufficient for real-world generalization.
- **Ensemble as unified framework.** Verifier serves dual roles: at training, it filters pseudo-labels for student model fine-tuning; at inference, it weights multiple trackers dynamically. This unifies supervision selection and model coordination.
- **Data-efficient adaptation.** Verifier-guided pseudo-labeling reduces the amount of real-world unlabeled video needed for effective fine-tuning compared to naive self-training, lowering the barrier to real-world deployment.
- **Point tracking as 3D data annotation engine.** PointWorld uses CoTracker3 to generate per-pixel 2D correspondences across manipulation video, then lifts them to 3D using FoundationStereo depth and optimized camera extrinsics. This pipeline recovers reliable 3D point flows for >60% of the DROID dataset (~200 hours), demonstrating that 2D point tracking is now mature enough to serve as a scalable 3D annotation tool. Occluded points (identified via CoTracker3 visibility labels) are excluded from training supervision, enabling the world model to learn implicit shape completion.

## Open problems

- **Generalization to new architectures**: does Verifier transfer to novel tracker designs not seen during training?
- **Out-of-distribution failures**: how does Verifier degrade when trackers exhibit fundamentally new error patterns?
- **3D point tracking**: current work focuses on 2D; PointWorld shows 2D-to-3D lifting via depth is viable but depends on external depth estimation quality. Native 3D point tracking (e.g., for 4D reconstruction) remains an open challenge
- **Long-term consistency**: Verifier operates per-frame; whether per-trajectory consistency checks would further improve quality
- **Computational overhead**: cost of running Verifier at inference vs. single tracker trade-offs not fully characterized
- **Interactive feedback**: opportunities for user correction or refinement of Verifier selections unexplored

## Recommended reading order

1. [[papers/2026-verifier-real-world-point-tracking|Verifier (2026)]] — entry point; establishes learned reliability estimation as core paradigm for robust ensemble-based point tracking
2. [[papers/2026/2026-pointworld/2026-pointworld|PointWorld (CVPR 2026)]] — downstream application; shows how 2D point tracking (CoTracker3) enables large-scale 3D dynamics data annotation for world model training

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
