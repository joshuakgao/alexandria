---
title: "Reducing Hallucinations in Vision-Language Models via Latent Space Steering"
authors: [Sheng Liu, Haotian Ye, Lei Xing, James Zou]
year: 2024
venue: "EMNLP"
tags: [vision-language-models]
url: ""
pdf: "[[2025-latent-space-steering-reduce-hallucinations.pdf]]"
date_ingested: 2026-06-18
---

# Reducing Hallucinations in Vision-Language Models via Latent Space Steering

![[2025-latent-space-steering-reduce-hallucinations-thumbnail.png]]

## Research gap

This paper introduces Visual and Textual Intervention (VTI), a test-time technique that reduces hallucinations in LVLMs by steering latent space representations during inference to enhance vision feature stability. The key insight is that hallucinations often arise from misalignments between separately pre-trained vision encoders and text decoders, with text decoders showing excessive sensitivity to vision inputs. VTI addresses this by intervening in the latent space representations to promote better alignment between modalities. As a task-agnostic test-time intervention, VTI can be applied to any problem without additional computational cost beyond standard inference. The approach demonstrates that careful manipulation of intermediate representations can significantly reduce problematic hallucinations while maintaining generation quality.

## Contributions

- Identifies vision-text decoder sensitivity as source of hallucinations in separately pre-trained models
- Proposes task-agnostic latent space steering for hallucination reduction at test time
- Demonstrates effectiveness without additional training or fine-tuning requirements
- Provides interpretable mechanism for understanding hallucination sources

## Method

Test-time intervention technique that manipulates latent space representations of vision and text tokens to reduce sensitivity of text decoder to vision inputs, promoting more stable and accurate generation.

## Datasets & evaluation

Significant hallucination reduction across diverse vision-language tasks without requiring model retraining, with minimal computational overhead.

## Limitations

Optimal steering magnitude and target directions may vary across models and tasks. Approach assumes decomposability of hallucination sources into vision-text misalignment; other causes not addressed. Generalization to different model architectures and training procedures not exhaustively evaluated.

---

## Key takeaways

Complements other hallucination reduction approaches like prompt engineering (Skip \n) by addressing the fundamental alignment issue between vision and text encoders, offering test-time solution without model modification.
