---
title: "Real-World Point Tracking with Verifier-Guided Pseudo-Labeling"
authors: [Görkay Aydemir, Fatma Güney, Weidi Xie]
year: 2026
tags: [video-understanding, point-tracking]
url: ""
pdf: "[[2026-verifier-real-world-point-tracking.pdf]]"
date_ingested: 2026-06-18
---

# Real-World Point Tracking with Verifier-Guided Pseudo-Labeling

![[2026-verifier-real-world-point-tracking-thumbnail.png]]

## Research gap

Point tracking models trained on synthetic datasets suffer from sim-to-real gap when applied to real videos: appearance differences, nonrigid motion, occlusions, lighting changes degrade performance. Self-training on unlabeled real videos via pseudo-labels offers a practical solution, but naive pseudo-labeling is brittle—different trackers excel in different regimes (one resists fast motion but drifts under low texture; another handles occlusions but suffers identity switches). This paper introduces **Verifier**, a learned meta-model that scores per-frame reliability of candidate trajectories from multiple pretrained trackers. Given a query point and several tracker predictions, Verifier evaluates each frame and selects the most trustworthy trajectory, reducing error accumulation. Crucially, Verifier is trained entirely on synthetic data using deliberately perturbed candidates (emulating realistic errors like drift, jumps, occlusions) and a contrastive objective; this synthetic-trained reliability estimator transfers to real videos. Verifier enables both training-time pseudo-label generation (filtering/fusing tracker outputs into clean supervision for student models) and inference-time ensemble (combining trackers on-the-fly based on learned reliability).

## Contributions

- **Learned reliability scorer**: meta-model Verifier learns to assess per-frame trustworthiness of tracker predictions without real annotations
- **Synthetic-to-real transfer**: trained on synthetic data with ground-truth and perturbed candidates; recognizes consistency cues that generalize to real videos
- **Ensemble-guided pseudo-labeling**: adaptively selects and fuses predictions from multiple trackers, reducing error propagation vs. fixed heuristics
- **Unified framework**: single mechanism serves both training-time supervision selection and inference-time ensemble, improving robustness and coherence
- **Data-efficient adaptation**: enables fine-tuning on unlabeled real videos with less data than prior self-training methods

## Method

**Architecture:**
- **Verifier input**: query point + multiple candidate trajectories (from different off-the-shelf trackers) + video frames
- **Synthetic training**: use ground-truth labels + deliberately corrupt candidates (drift, jumps, occlusions) to teach reliability discrimination
- **Contrastive objective**: rank correct vs. corrupted trajectory alternatives
- **Per-frame scoring**: outputs reliability scores for each candidate at each frame
- **Adaptive selection/fusion**: at training, selects most reliable predictions for pseudo-labels; at inference, weights tracker outputs dynamically

**Key insight:** Different trackers have complementary error profiles. Rather than fixed global confidence thresholds, learn what makes a prediction reliable at each frame and scene, leveraging multiple teachers.

## Datasets & evaluation

Experiments on four real-world benchmarks (HAVI, RoboTAP, Kinetics Points, TAP-Vid):
- **Training-time pseudo-labeling**: Verifier-guided fine-tuning substantially improves adaptation vs. naive self-training
- **Inference-time ensembling**: combining trackers via Verifier adds further gains, particularly under challenging motion and occlusion
- **State-of-the-art accuracy**: competitive or better than prior methods while requiring less real-world data
- **Robustness**: ablations show Verifier effectively exploits complementary tracker strengths and resists individual failures

## Limitations

- **Generalization to new tracker architectures**: does Verifier transfer to trackers trained after Verifier was deployed, or does it overfit to seen tracker error profiles?
- **Computational cost**: Verifier adds inference overhead when used as ensemble module; efficiency vs. accuracy trade-offs not fully explored
- **Out-of-distribution trackers**: how robust is Verifier to trackers with fundamentally different failure modes than seen during training?
- **Metric scale and 3D consistency**: focuses on 2D point tracking; whether Verifier's reliability cues help 3D reconstruction tasks (beyond 2D trajectory) unclear
- **Interactive correction**: no user-in-the-loop mechanism to provide feedback on Verifier selections

## Key takeaways

Builds on recent long-term point tracking (TAPIN, CoTracker3, Track-On) and pseudo-labeling literature (BootsTAPIR, synthetic-to-real adaptation). Extends prior ensemble and structured-consensus pseudo-labeling work by making it learnable and specific to tracking reliability across frames. Unlike fixed weighting or heuristic thresholds, Verifier learns frame-adaptive reliability from contrastive training on synthetic perturbations.
