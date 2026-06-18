---
title: "VideoWeave: A Data-Centric Approach for Efficient Video Understanding"
authors: [Zane Durante, Amandeep Singh, Tzu-Sheng Kuo, Lili Lee, Siddharth Khatua, Sumedh Agarwal, Ruohan Gao, Ehsan Adeli, Li Fei-Fei]
year: 2026
tags: [video-understanding]
url: ""
pdf: "[[2026-videoweave.pdf]]"
date_ingested: 2026-06-18
---

# VideoWeave: A Data-Centric Approach for Efficient Video Understanding

![[2026-videoweave-thumbnail.png]]

## Research gap

VideoWeave addresses the prohibitive cost of training video-language models by reorganizing existing short-video datasets rather than modifying model architectures. The key insight is that existing video-text corpora contain limited temporal diversity: they consist mainly of short clips (<1 minute), whereas modern long-form video benchmarks (HourVideo, VideoMME) require models to reason over hours of content. Rather than annotating new long videos, VideoWeave splices together multiple existing short, captioned videos into synthetic long-context training samples. Under identical compute budgets, models trained with VideoWeave outperform conventional video finetuning. The approach is architecture-agnostic, offering a simple path to expand training data diversity without increasing annotation burden.

## Contributions

- **Data splicing for temporal diversity**: method to compose short videos into synthetic long-context samples, expanding temporal variation within fixed compute
- **Systematic study of composition strategies**: comparison of random vs. visually clustered splicing and caption enrichment (concatenation, blending, or semantic reordering) on downstream performance
- **Compute-efficient gains**: under identical FLOPs, VideoWeave-trained models achieve higher accuracy on video question answering than baselines
- **Architecture-agnostic framework**: works with any video-language model without requiring modifications to encoders, decoders, or optimization objectives

## Method

VideoWeave splices L short video-text pairs (V₁, C₁), ..., (Vₗ, Cₗ) into a single training sample (V₁...Vₗ, C₁...Cₗ). The model is trained with causal language modeling: predicting each caption Cⱼ conditioned on all preceding videos V₁...Vⱼ₋₁ and preceding captions C₁...Cⱼ₋₁. This mirrors real streaming: at step j, the model has seen j-1 complete video-caption events and must reason about the j-th event.

Two key design choices:
1. **Composition strategy**: random splicing vs. visually clustered (selecting videos with similar scene/action content) vs. semantically clustered (videos with related caption themes)
2. **Caption enrichment**: simple concatenation ("V1 caption. V2 caption") vs. blended ("In the first clip... In the second clip...") vs. reordered (e.g., question-answer format for long-form QA)

Parametrized by L (number of spliced videos per sample). Experiments vary L from 1–5.

## Datasets & evaluation

Evaluated on video question answering tasks (e.g., LongVideoBench, VideoMME adapted to long-form):
- **Accuracy gains**: models trained with VideoWeave (L=3, L=5) exceed baselines by 3–7% under equal FLOPs
- **Temporal reasoning improvement**: models show stronger performance on temporal reasoning questions with spliced data
- **Composition impact**: visual/semantic clustering modestly outperforms random splicing; caption blending slightly improves over concatenation
- **Scaling**: gains hold across model scales (7B–13B parameter LLMs)

Efficiency implication: VideoWeave achieves compute parity with baselines while training on substantially more diverse temporal content, effectively "expanding" the dataset without new annotation.

## Limitations

- **Distribution shift at inference**: models are trained on synthetic long sequences but may encounter real long videos with different temporal dynamics or scene transitions; robustness unclear
- **Optimal composition strategies**: study is preliminary; more sophisticated graph-based or reinforcement-learned composition policies could outperform heuristic clustering
- **Generalization to non-QA tasks**: evaluation focuses on QA; unclear how gains transfer to captioning, grounding, or other video tasks
- **Very long context limits**: method samples up to 5 videos per sample; performance with larger L (10+, 20+) and impact on context window limits not explored
- **Comparison to real long videos**: synthetic splicing vs. training on actual long-form annotated data not directly compared (e.g., when both have same annotation budget)

## Key takeaways

- **Data augmentation for video**: relates to mixup and other augmentation techniques, but VideoWeave is data-composition, not noise-based
- **Long-form video understanding**: complements temporal search methods like [[2025-timesearch-r-temporal-search-video-understanding|TimeSearch-R]] (which optimize *inference-time* frame selection); VideoWeave optimizes *training-time* data composition
- **Multimodal datasets**: addresses the scarcity of long-video-text pairs noted in [[2026-video-understanding-large-language-models-survey|Video Understanding With LLMs: Survey]]; offers a practical path to synthetic long-form data without annotation
- **Sequence composition**: related to concatenation strategies in language model pretraining (e.g., packing multiple documents); extends to multimodal sequences
