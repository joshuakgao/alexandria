---
topic: Cognitive AI
slug: cognitive-ai
---

# Cognitive AI

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "cognitive-ai")
SORT year DESC
```

## Overview

# Cognitive AI — Overview

*Last updated: 2026-06-24 | Sources: 3 papers (BabyView, BabyZWM, SpinBench)*

## Current thesis

The "data gap" between human and machine learning—humans achieve sophisticated cognitive abilities with orders of magnitude less data—is a central challenge for both understanding human development and building efficient AI systems. Addressing this gap requires (1) high-fidelity datasets of naturalistic human learning environments (BabyView), and (2) architectures and learning mechanisms that exploit inductive biases grounded in cognitive science principles (BabyZWM). Together, these show that data-efficient learning from human-scale training data is possible, but requires fundamental innovations in model design beyond scaling current architectures.

## Key trends

**2025:**
- **Quantifying the data gap**: BabyView dataset measures performance of self-supervised models trained on 868 hours of naturalistic child egocentric video. Models significantly underperform those trained on curated datasets, establishing the data gap as a quantifiable challenge: human children learn more efficiently from the same distribution of sensory input.
- **High-fidelity developmental data**: BabyView provides the first large-scale, high-resolution egocentric dataset with rich metadata (speech transcription, speaker diarization, pose, language outcomes). Enables systematic study of learning from naturalistic, embodied child experience rather than curated data.
- **Multilingual and diverse contexts**: Unlike prior datasets (SAYCam, hand-collected academic datasets), BabyView includes multiethnic, multilingual families and varying socioeconomic backgrounds, moving toward more representative sampling of human development.
- **Infrastructure for annotation and research**: Gold-standard annotations and sharing via Databrary create a foundation for multi-disciplinary research bridging developmental psychology, cognitive science, and machine learning.

**2026:**
- **Cognitive-aligned architectures for data efficiency**: BabyZWM demonstrates that careful architectural choices—sparse temporally-factored prediction, approximate causal inference, compositional inference—enable learning from human-scale data (868 hours) while matching human developmental trajectories.
- **Developmental plausibility as design principle**: BabyZWM's learning milestones align with infant development; internal representations correlate with neural responses (fMRI). Suggests that developmental alignment is not merely an aesthetic metric but a signal of mechanistically sound learning.
- **Zero-shot transfer from naturalistic data**: BabyZWM achieves diverse visual-cognitive tasks (depth, motion, segmentation, intuitive physics) zero-shot, without task-specific training. Shows that universal problem-solving interfaces emerge from self-supervised learning of world structure.
- **Brain-aligned representations**: BabyZWM's internal activations correlate with visual cortex responses. Bridges computational modeling with neuroscience, suggesting that learning from naturalistic data induces biologically plausible representations.

**2025:**
- **Cognitive science as benchmark design principle**: SpinBench (ICLR 2026) draws on foundational cognitive science — Shepard & Metzler mental rotation experiments, imagery-based reasoning theories — to design a diagnostic benchmark for VLM spatial reasoning. Progressive task structure mirrors cognitive complexity (identity matching → perspective taking), and human response time correlates with VLM accuracy, validating that the benchmark captures shared spatial reasoning challenges. This establishes cognitively grounded evaluation as a distinct methodology for AI diagnostics.
- **Analog spatial reasoning absent in VLMs**: SpinBench's central question — whether VLMs engage in imagery-based spatial reasoning or rely on symbolic/linguistic associations — is answered negatively: most models fail at mental rotation and perspective taking (near-chance kappa), suggesting VLMs lack the analog spatial simulation processes documented in human cognition.

## Open problems

**Data and representation:**
- **Why models underperform on naturalistic data**: BabyView documents the gap but doesn't fully explain mechanism. Is it blur, camera motion, distribution shift, or fundamental architectural limitation? Which data quality factors most impact learning?
- **Cross-cultural and socioeconomic variation**: BabyView skewed toward educated, US-based families. Unclear how findings generalize to other cultural contexts, parenting styles, and language environments.
- **Developmental trajectories in models**: While BabyZWM shows developmental alignment, deep investigation of how models' abilities emerge over time (e.g., when does object permanence emerge?) and whether it matches infants is limited.
- **Motor and embodied learning**: Datasets capture head movement (IMU data) and pose but don't deeply model the embodied, motor aspect of learning (reaching, grasping, locomotion). Role of embodied action in cognitive development unclear.

**Architecture and learning:**
- **Scaling data-efficient methods**: BabyZWM trained on 868 hours; does performance continue to improve with multi-child datasets or larger collections? Scaling laws for naturalistic data unknown.
- **Actionable world models**: BabyZWM learns passive observation-conditioned models; integration with action (learning to intervene, reach, manipulate) and reinforcement learning is open.
- **Semantic and abstract reasoning**: Current work focuses on physics and spatial understanding. Integration with semantic knowledge (object categories, agent intentions) and more abstract reasoning (counterfactual, symbolic) remains to explore.
- **Language-vision integration**: BabyView includes rich speech/transcription data; deep study of how language and vision learning co-develop in models largely unexplored.

**Cognitive science alignment:**
- **Mechanism of innate biases**: Do architectural choices (factorization, causal inference) capture genuine inductive biases of infant cognition, or are they post-hoc engineering? Deeper cognitive science grounding needed.
- **Social learning and interaction**: Infant learning is deeply social (learning from caregivers, joint attention, imitation). BabyView captures this in egocentric video, but models' capacity to learn from social signals (gaze direction, pointing, etc.) is largely unexplored.
- **Motivation and affect**: Human development involves motivation, curiosity, affect. How do these drive learning in models? Role of intrinsic motivation in efficient learning is open.
- **Imagery-based vs. symbolic spatial reasoning**: SpinBench shows VLMs fail at tasks requiring analog spatial simulation (mental rotation, perspective taking) while succeeding at tasks solvable via linguistic/symbolic pattern matching (object-relation grounding). Can architectural innovations (e.g., learned spatial simulators) endow VLMs with analog spatial reasoning?

## Recommended reading order

1. [[papers/2025-babyview-egocentric-video-dataset|BabyView Dataset (2025)]] — establishes the data gap and provides the foundational dataset; read first to understand the empirical challenge and dataset infrastructure
2. [[papers/2026-babyzwm-zero-shot-world-models|BabyZWM (2026)]] — demonstrates how cognitive-informed architectural innovations address the data gap; read after BabyView to see the cognitive-AI methodology in action
3. [[papers/2025/2025-spinbench/2025-spinbench|SpinBench (ICLR 2026)]] — cognitive science as benchmark design; shows VLMs lack analog spatial reasoning processes documented in humans

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
