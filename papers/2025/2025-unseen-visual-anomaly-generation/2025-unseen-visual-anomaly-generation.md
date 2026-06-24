---
title: "Unseen Visual Anomaly Generation"
authors: [Han Sun, Yunkang Cao, Hao Dong, Olga Fink]
year: 2025
venue: "CVPR 2025"
tags: [anomaly-detection, diffusion-models, synthetic-datasets]
url: ""
pdf: "[[2025-unseen-visual-anomaly-generation.pdf]]"
date_ingested: 2026-06-18
---

# Unseen Visual Anomaly Generation

![[2025-unseen-visual-anomaly-generation-thumbnail.png]]

## Research gap

AnomalyAny addresses the critical challenge of data scarcity in visual anomaly detection by leveraging Stable Diffusion for test-time generation of diverse, realistic unseen anomalies. Unlike prior work that either uses unrealistic augmentations (cut-and-paste) or requires extensive training data, AnomalyAny conditions Stable Diffusion on a single normal sample during inference to generate high-quality anomalies for arbitrary object types via text descriptions. The framework introduces two key innovations: attention-guided anomaly optimization that directs the diffusion model's attention toward generating hard anomaly concepts, and prompt-guided anomaly refinement that incorporates detailed descriptions to improve generation quality. Experiments on MVTec AD and VisA datasets demonstrate that generated anomalies effectively improve downstream anomaly detector performance.

## Contributions

- **Test-time anomaly generation without training**: generates diverse anomalies from frozen Stable Diffusion conditioned on single normal sample; preserves SD's generalization capability across object types
- **Attention-guided optimization**: directs diffusion attention toward anomalous regions rather than preserving overall image structure; enables realistic hard anomaly concepts
- **Prompt-guided refinement**: incorporates text descriptions (manual or auto-generated via GPT-4) to improve generation quality and control anomaly type
- **Zero-shot capability**: works on unseen objects and anomaly types without task-specific fine-tuning; single model handles arbitrary new objects
- **Practical effectiveness**: generated anomalies improve downstream anomaly detector performance on MVTec AD and VisA benchmarks

## Method

**Baseline challenge:** Stable Diffusion trained on general image distribution generates images that deviate from normal distribution; naive text-conditional generation fails to produce convincing anomalies consistent with normal samples.

**Solution architecture:**
1. **Test-time normal sample conditioning**: encode a single normal sample as context; generate anomalies that are semantically similar but containing specified anomaly patterns
2. **Attention guidance**: manipulate cross-attention mechanism to focus generation on anomalous regions while preserving normal context
3. **Prompt engineering**: use detailed text descriptions (e.g., "A photo of [object] that is damaged") to guide generation; descriptions can be manually specified or auto-generated from object category via GPT-4
4. **Refinement**: iterative refinement with prompt variation to improve quality

**Advantages over baselines:**
- vs. Cut-and-paste: generates semantically consistent anomalies rather than random patterns
- vs. Fine-tuned diffusion: avoids overfitting to limited anomaly training data; maintains diversity
- vs. Adversarial generation: simpler training-free approach; leverages pre-trained SD

## Datasets & evaluation

**MVTec AD dataset:** Generated anomalies improve detection performance; highest improvement when combined with existing anomaly detectors.

**VisA dataset:** Similar improvements across different product categories; demonstrates generalization across diverse object types.

**Comparison to prior methods:**
- DRAEM (cut-and-paste): generates unrealistic patterns
- AnomalyDiffusion (fine-tuned): limited to training data distribution
- AnomalyAny (ours): universal, realistic, diverse

**Qualitative results:** Generated anomalies exhibit realistic damage patterns, color shifts, and structural defects consistent with real anomalies.

## Limitations

- **Synthetic-real distribution gap**: generated anomalies improve detection, but no analysis of whether synthetic distribution matches real-world anomalies; potential domain gap not quantified
- **Anomaly diversity**: text-guided generation enables diverse anomaly types, but no systematic study of coverage — which real anomalies can be synthesized vs. missing?
- **Temporal anomalies**: focuses on single-image anomalies; applicability to video anomaly detection (temporal patterns) unexplored
- **Evaluation scope**: tested on MVTec AD and VisA; generalization to other domains (medical imaging, satellite data, etc.) unclear
- **Attention mechanism details**: attention-guided optimization heuristics not fully justified; ablations on attention targeting strategies missing
- **Comparison to weak labels**: in practice, collecting even weak anomaly labels may be easier than prompt engineering; cost-benefit analysis needed

## Key takeaways

**Complements anomaly detection approaches:**
- [[papers/2023-destseg-anomaly-detection|DeSTSeg]] uses synthetic anomalies during training; AnomalyAny generates at test time, orthogonal to training approach
- Both address data scarcity; DeSTSeg via synthetic corruption, AnomalyAny via open-ended diffusion-based generation

**Data synthesis perspective:**
- Prior work: DRAEM (cut-and-paste), AnomalyDiffusion (fine-tuned diffusion)
- This work: leverages frozen foundation model with test-time guidance; simpler, more generalizable

**Foundation model trend:** aligns with broader shift toward leveraging pre-trained generative models rather than task-specific architectures.
