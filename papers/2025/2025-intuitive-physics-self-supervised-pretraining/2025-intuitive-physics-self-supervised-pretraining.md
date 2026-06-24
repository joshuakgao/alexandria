---
title: "Intuitive physics understanding emerges from self-supervised pretraining on natural videos"
authors: [Nicolas Ballas, Mahmoud Assran, Adrien Bardes, Laurent Najman, Michael Rabbat, Emmanuel Dupoux, Yann LeCun]
year: 2025
venue: ""
tags: [world-models]
url: ""
pdf: "[[2025-intuitive-physics-self-supervised-pretraining.pdf]]"
date_ingested: 2026-06-18
---

# Intuitive physics understanding emerges from self-supervised pretraining on natural videos

![[2025-intuitive-physics-self-supervised-pretraining-thumbnail.png]]

## Research gap

This work demonstrates that **intuitive physics understanding emerges spontaneously** from self-supervised video prediction in learned representation space, without explicit physics supervision or task-specific training. Using V-JEPA (Video Joint Embedding Predictive Architecture), the authors probe physics understanding via the violation-of-expectation framework, testing for object permanence, shape consistency, and contact physics. Crucially, V-JEPA achieves ~96% accuracy on IntPhys (vs. 50% chance), while pixel-space video prediction and text-based LLMs perform near chance. This challenges the "core knowledge" hypothesis—the idea that physics understanding must be innate—and supports a predictive coding framework where learning abstract representations while predicting missing video parts is sufficient to acquire physics intuitions.

## Contributions

- **Representation-space prediction > pixel prediction**: V-JEPA (predicting in learned space) substantially outperforms pixel-space video models on physics tasks, demonstrating the importance of learning an abstract world model representation
- **Emergent physics from self-supervised learning**: No physics labels or task-specific training required; general-purpose masked video prediction in representation space naturally captures physics priors
- **Violation-of-expectation paradigm**: Adapts developmental psychology framework to probe learned physics understanding without task-specific adaptation; uses "surprise" (prediction error) as a proxy for physics violation detection
- **Minimal data sufficiency**: Models trained on as little as one week of natural video achieve above-chance physics understanding, suggesting the signal is learnable at modest data scales
- **Challenges core knowledge hypothesis**: Results suggest physics understanding is emergent from learning dynamics rather than hardwired/innate, aligning with predictive coding neuroscience

## Method

**V-JEPA architecture:**
- Tokenizes video frames into patches
- Learns a joint embedding space via **masked video inpainting**: predicts representations of masked (corrupted) video patches from unmasked ones
- Does not reconstruct pixels; only predicts abstract representations
- Trained on unlabeled natural video via self-supervised loss

**Physics evaluation (Violation-of-Expectation):**
1. Train V-JEPA on unlabeled videos
2. Given a test video (plausible or with physics violation), extract representations
3. Predict representations of N future frames given M past frames
4. Compute "surprise": distance between predicted and observed future representations
5. Videos with physics violations produce higher surprise → can be detected without explicit labels

**Datasets tested:**
- **IntPhys**: synthetic object trajectories testing permanence, continuity, solidity (96% accuracy for V-JEPA)
- **GRASP**: object grasping and contact physics
- **InfLevel**: intuitive understanding of liquid physics

## Datasets & evaluation

**Core findings:**
- V-JEPA: ~96% accuracy on IntPhys (vs. 50% chance)
- Pixel Prediction (VideoMAEv2-g): ~64% accuracy
- Closed Video LLM (Gemini 1.5 pro): ~52% accuracy
- Open Video LLM (Qwen2-VL-72b): ~54% accuracy
- Untrained networks: ~50% (chance)

**Scaling behavior:**
- Models trained on minimal data (1 week) show above-chance performance
- Performance improves with more training data and video diversity
- No task-specific fine-tuning needed; zero-shot transfer to physics tasks

**Architecture ablations:**
- Representation-space prediction critical; pixel-space reconstruction loses physics signal
- Abstract feature learning (predictive coding) is the inductive bias that captures physics

## Limitations

- **Real-world physics**: IntPhys is synthetic; performance on real-world videos with complex physics (soft bodies, friction, fluid dynamics) remains unexplored
- **Temporal range**: Tests examine short-term predictions (next few frames); whether models capture longer-horizon physics (>1 second) is unclear
- **Out-of-distribution generalization**: Trained on natural videos; robustness to novel object types, materials, or physical scenarios not thoroughly evaluated
- **Causal reasoning**: Surprise metric is passive (prediction error); whether models can perform active counterfactual reasoning ("what if?") is open
- **Integration with semantics**: Models currently learn purely visual physics; how to ground physical reasoning in semantic understanding (object categories, intentions) is unexplored
- **Scaling to large models**: Study uses relatively modest models (V-JEPA-H); whether larger architectures show different physics behaviors is unknown
- **Comparison with explicit physics models**: No direct comparison with neural physics engines (e.g., DPI-Net, GNS) that explicitly learn differential equations

## Key takeaways

**Structured models vs. emergent learning:**
- Previous work split into two camps: (1) hand-coded 3D scene representations ("core knowledge" hypothesis), or (2) pixel-based generative models (which fail on physics)
- V-JEPA occupies middle ground: **learned abstract representation** (not hand-coded, like structured models) via **self-supervised prediction** (not hand-designed, like generative models)
- Aligns with predictive coding hypothesis in cognitive neuroscience (Rao & Ballard, 1999; Hohwy, 2013)

**Compared to video understanding work:**
- Unlike [[../video-understanding/papers/2025-video-understanding-with-llms-survey|video understanding surveys]] (QA, captioning), this probes **physical reasoning** as an emergent capability
- Physics understanding is a form of world modeling—learning the dynamics and constraints of visual environments
