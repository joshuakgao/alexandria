---
title: "Zero-shot World Models Are Developmentally Efficient Learners"
authors: [Kian Hao Aw, Daniel Kotar, Andrew Lee, Sunjin Kim, Khaled Jedoui, Sudhanshu Venkatesh, Chaofei Chen, Michael Frank, Daniel Yamins]
year: 2026
venue: ""
tags: [cognitive-ai, world-models]
url: ""
pdf: "[[2026-babyzwm-zero-shot-world-models.pdf]]"
date_ingested: 2026-06-18
---

# Zero-shot World Models Are Developmentally Efficient Learners

![[2026-babyzwm-zero-shot-world-models-thumbnail.png]]

## Research gap

This paper demonstrates how cognitive science principles—specifically, developmental plausibility and data efficiency—can inform AI architecture design. BabyZWM learns powerful visual-cognitive abilities (depth, motion, segmentation, intuitive physics) from just 868 hours of egocentric video captured from a single child's perspective via the BabyView dataset, far below the data scale of typical models. The key innovation is a three-part architecture grounded in cognitive theory: (1) sparse temporally-factored prediction that naturally decouples appearance from dynamics without explicit supervision, (2) approximate causal inference enabling zero-shot task extraction by comparing counterfactual predictions, and (3) compositional inference for building complex reasoning from simple pieces. Remarkably, the model exhibits developmental trajectories matching human infant learning milestones and internal representations aligning with visual cortex neural responses (fMRI). This work exemplifies cognitive-AI methodology: using principles from child development to guide algorithm design, validating models against cognitive/neural data, and closing the "data gap" between human and machine learning efficiency.

## Contributions

- **Cognitive-informed architecture**: Three design principles (sparse factorization, causal inference, composition) grounded in cognitive developmental theory; architecture is not arbitrary engineering but justified by developmental plausibility
- **Data-efficient learning from naturalistic child data**: 868 hours suffices to achieve competitive performance on standard benchmarks; demonstrates that the "data gap" can be narrowed through careful architectural design informed by how humans learn
- **Zero-shot transfer interface**: Approximate causal inference enables extracting diverse task predictions (depth, flow, segmentation, physics) without task-specific training; shows that universal problem-solving emerges from self-supervised world modeling
- **Developmental and neural alignment**: Learning trajectory parallels human infant milestones; internal representations correlate with fMRI visual cortex responses; validates that cognitive alignment is not decorative but indicates mechanistically sound learning
- **Bridge between cognitive science and AI**: Demonstrates bidirectional value: cognitive science grounds architecture design, and AI illuminates mechanisms of human learning; opens pathway to jointly advance both fields

## Method

**Core architecture**: Sparse temporally-factored masked multi-frame visual predictor. Given frame f₁ and sparse mask of frame f₂ (hiding most of f₂), model predicts full frame f₂. Sparsity encourages natural factorization: model must infer hidden appearance from dynamics.

**Zero-shot extraction**: For any visual task (e.g., depth estimation), compare predictions under ground-truth condition (depth map visible) vs. counterfactual (depth withheld). Prediction difference approximates causal contribution of that factor; serves as zero-shot readout without additional training.

**Compositional reasoning**: Complex tasks are solved by chaining simple extractors. Example: identify moving objects by (1) predicting appearance change given hypothetical motion, (2) computing optical flow from predictions, (3) using flow magnitude for segmentation.

**Training data**: BabyView dataset—868 hours egocentric video from single child, ages 5 months to 5 years, naturalistic home environments.

**Validation**: Developmental trajectory measured by evaluating checkpoints across training; neural alignment via fMRI correlation; behavioral performance on standard benchmarks (depth, motion, segmentation, physics).

## Datasets & evaluation

- **Zero-shot task performance**: Competitive with supervised baselines on depth (NYU Depth v2), motion segmentation (DAVIS), object segmentation (COCO), and violation-of-expectation physics probes, despite zero task-specific training
- **Developmental milestone alignment**: Learning progression mirrors infant development (motion perception before object permanence, etc.); suggests architecture captures genuine cognitive principles
- **Brain-aligned representations**: Internal activations correlate with visual cortex fMRI responses; learned structure is biologically plausible
- **Data efficiency**: 868 hours achieves strong performance; far below standard model training data (billions of images/videos); demonstrates feasibility of learning from human-scale data

## Limitations

- **Generalization of cognitive principles**: Does sparse factorization truly capture infant inductive biases, or is it a fortunate architectural choice? Deeper investigation of how cognitive principles transfer to other domains needed
- **Scaling and multi-child data**: Trained on single child; unclear how performance scales with diverse children or larger datasets; developmental alignment may be child-specific artifact
- **Social and interactive learning**: BabyView is passive observation; role of social interaction, joint attention, imitation in infant learning unexplored in model
- **Action and embodiment**: Model is observation-only; integration with action (reaching, grasping, manipulation) and embodied learning remains open
- **Language-vision co-development**: BabyView includes speech; joint learning of language and vision (how they inform each other in development) not deeply explored

## Key takeaways

Advances prior work on self-supervised world models ([[../../world-models/papers/2025-intuitive-physics-self-supervised-pretraining|V-JEPA]]) by (1) operating on naturalistic child video rather than curated data, (2) providing zero-shot multitask interface via causal inference, and (3) directly grounding design in developmental and neuroscience principles. Complements [[papers/2025-babyview-egocentric-video-dataset|BabyView dataset]] by demonstrating that the data-gap challenge can be addressed through cognitive-informed architecture; together, BabyView quantifies the gap, BabyZWM shows how to close it. Related to broader cognitive-AI agenda: using human learning as inspiration for AI design and validation.
