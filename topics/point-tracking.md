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

*Last updated: 2026-04-17 | Sources: 1 paper*

## Current thesis

Real-world point tracking faces a fundamental reliability problem: different trackers excel in different regimes, and no fixed heuristic can reconcile their heterogeneous error profiles. Rather than designing new tracker architectures, recent work focuses on **learned reliability estimation**—a verifier meta-model that learns when and where to trust different trackers. Verifier (2026) demonstrates that synthetic-trained reliability cues transfer to real videos, enabling adaptive pseudo-label selection and ensemble weighting that reduces error propagation during real-world adaptation. This suggests point tracking is converging on learned ensemble strategies that exploit complementary strengths of off-the-shelf models rather than single monolithic trackers.

## Key trends

**2026:**
- **Learned reliability scoring over fixed heuristics.** Verifier shows that global confidence thresholds and fixed weighting schemes fail to capture per-frame, per-tracker reliability variation. A learned meta-model trained on synthetic perturbations recognizes consistency cues that transfer to real errors (drift, jumps, occlusions), enabling adaptive selection.
- **Synthetic-to-real transfer via perturbation.** Training Verifier on deliberately corrupted synthetic trajectories (mimicking real failure modes) teaches it to recognize which predictions are trustworthy, without requiring real annotations. This cheaply-annotated synthetic supervision is sufficient for real-world generalization.
- **Ensemble as unified framework.** Verifier serves dual roles: at training, it filters pseudo-labels for student model fine-tuning; at inference, it weights multiple trackers dynamically. This unifies supervision selection and model coordination.
- **Data-efficient adaptation.** Verifier-guided pseudo-labeling reduces the amount of real-world unlabeled video needed for effective fine-tuning compared to naive self-training, lowering the barrier to real-world deployment.

## Open problems

- **Generalization to new architectures**: does Verifier transfer to novel tracker designs not seen during training?
- **Out-of-distribution failures**: how does Verifier degrade when trackers exhibit fundamentally new error patterns?
- **3D point tracking**: current work focuses on 2D; extension to 3D (e.g., for 4D reconstruction) unclear
- **Long-term consistency**: Verifier operates per-frame; whether per-trajectory consistency checks would further improve quality
- **Computational overhead**: cost of running Verifier at inference vs. single tracker trade-offs not fully characterized
- **Interactive feedback**: opportunities for user correction or refinement of Verifier selections unexplored

## Recommended reading order

1. [[papers/2026-verifier-real-world-point-tracking|Verifier (2026)]] — entry point; establishes learned reliability estimation as core paradigm for robust ensemble-based point tracking

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
