---
title: "Reconstruction by inpainting for visual anomaly detection"
authors: [Vitjan Zavrtanik, Matej Kristan]
year: 2021
venue: "Pattern Recognition"
tags: [anomaly-detection]
url: ""
pdf: "[[2021-reconstruction-by-inpainting-anomaly-detection.pdf]]"
date_ingested: 2026-06-18
---

# Reconstruction by inpainting for visual anomaly detection

![[2021-reconstruction-by-inpainting-anomaly-detection-thumbnail.png]]

## Research gap

RIAD (Reconstruction by Inpainting for Anomaly Detection) addresses a fundamental limitation of auto-encoder-based anomaly detection: neural networks generalize well even to unseen anomalies, reconstructing them with high fidelity and thus reducing detection capability. Rather than training auto-encoders to reconstruct full images, RIAD casts anomaly detection as a self-supervised reconstruction-by-inpainting problem. The method randomly removes rectangular regions from training images and trains an inpainting network to reconstruct only the removed regions from their immediate neighborhood. This forces the network to rely solely on local context, making accurate reconstruction of anomalous regions far less likely than non-anomalous ones. The increased reconstruction error gap enables superior anomaly detection and localization.

## Contributions

- **Reconstruction-by-inpainting formulation**: reformulates anomaly detection as a self-supervised inpainting task; shifts from full-image reconstruction to local region reconstruction
- **Masked region approach**: removed regions are not visible to the network during inference; anomalies in masked regions cannot be accurately reconstructed by neighborhood generalization
- **Self-supervised training**: no anomaly labels required; trained on anomaly-free images with random masking
- **Gradient similarity loss**: proposes gradient-based loss function for training inpainting networks
- **Anomaly score estimation**: uses region reconstruction quality to compute anomaly maps
- **State-of-the-art results**: achieves SOTA on challenging anomaly detection benchmarks at the time of publication
- **Video anomaly detection**: extends to video domain by leveraging temporal information

## Method

**Core insight:** Auto-encoders fail at anomaly detection because they reconstruct anomalies well due to high network capacity. In contrast, if anomaly pixels are not visible to the network during reconstruction, the network cannot accurately "hallucinate" them from neighborhood context.

**Algorithm:**
1. **Image partitioning**: divide input image into grid of k×k pixel regions
2. **Random masking**: randomly partition grid into n disjoint sets; set regions in each set to zero
3. **Inpainting training**: train encoder-decoder network to reconstruct masked regions from unmasked neighbors; optimize with gradient similarity loss
4. **Inference**: generate multiple partial reconstructions by masking different region sets
5. **Anomaly scoring**: evaluate reconstruction quality; regions with high error indicate anomalies; compile anomaly map

**Key design choice:** Masking forces reconstruction to rely on immediate neighborhood only, not global image features. Anomalies are harder to reconstruct when masked because the network must extrapolate from normal neighborhood appearance.

**Training:** Uses only anomaly-free images; self-supervised via masking/reconstruction task.

## Datasets & evaluation

**MVTec AD dataset (industrial inspection):**
- Sets new SOTA at publication time on challenging anomaly benchmarks
- Strong pixel-level localization capability
- Competitive image-level classification accuracy

**Comparison to baselines:**
- vs. Auto-encoder methods: reconstruction error gap larger (anomalies poorly reconstructed vs. normals well-reconstructed)
- vs. Geometric transformation pretext tasks: avoids issues with rotation-invariant textures and symmetric objects
- vs. GANomaly and others: more robust to different anomaly types

**Video anomaly detection:** Extends to temporal sequences using motion information.

## Limitations

- **Hyperparameter sensitivity**: method has hyperparameters (grid size k, number of partitions n); sensitivity analysis limited
- **Scalability to larger images**: inpainting networks may struggle with very large high-resolution images; computational cost not thoroughly analyzed
- **Domain specificity**: trained and evaluated primarily on industrial inspection; generalization to other domains (medical, surveillance) unclear
- **Comparison to contemporary methods**: limited direct comparison to GAN-based and other generative approaches contemporary at publication
- **Multi-scale anomalies**: single grid size may not capture anomalies at multiple scales; adaptive or multi-scale variants not explored
- **Semantic understanding**: method is reconstruction-based; doesn't reason about what types of anomalies are present, only that reconstruction fails

## Key takeaways

**Auto-encoder foundation:**
- Standard anomaly detection approach trains auto-encoders on normal data; anomalies detected via reconstruction error
- RIAD critique: auto-encoders generalize too well, reconstructing anomalies accurately; violates core assumption

**Self-supervised learning:**
- Relates to pretext task methods (rotation, translation prediction)
- RIAD's inpainting is a pretext task, but avoids geometric transformation invariance issues

**Foundational for later work:**
- Establishes reconstruction-based anomaly detection as a core paradigm
- Later works (DeSTSeg, 2023) build on student-teacher frameworks but also use synthetic training data
- Influenced modern approaches combining reconstruction with other constraints
