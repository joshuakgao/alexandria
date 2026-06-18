---
title: "Generalizable Object Re-Identification via Visual In-Context Prompting"
authors: [Zhizhong Huang, Xiaoming Liu]
year: 2025
venue: "ICCV"
tags: [re-id]
url: ""
pdf: "[[2025-vicp-generalizable-object-reid.pdf]]"
date_ingested: 2026-06-18
---

# Generalizable Object Re-Identification via Visual In-Context Prompting

![[2025-vicp-generalizable-object-reid-thumbnail.png]]

## Research gap

VICP (Visual In-Context Prompting) addresses a fundamental limitation of object re-identification: traditional ReID methods are category-specific (trained separately for persons, vehicles, etc.) and cannot generalize to unseen object categories without costly retraining. The paper formalizes the task of Generalizable Object ReID, where a model trained on seen categories must adapt to novel categories using only a few in-context example pairs, without any parameter updates.

## Contributions

- Formalizes Generalizable Object ReID as a new task: adapting to unseen object categories using only few-shot in-context examples, without parameter updates
- Proposes VICP, unifying LLM in-context reasoning with VFM visual features via dynamic visual prompt generation
- Introduces ShopID10K, a large-scale benchmark (10K instances, 34 categories) for cross-category ReID evaluation
- Outperforms self-supervised and few-shot baselines by ~4% mAP on ShopID10K and standard benchmarks (MSMT17, VeRi-776)
- Visual prompts are cached per category, enabling one-time prompt generation and instant deployment to new categories

## Method

The framework has two stages:

1. **In-Context Visual Prompt Generation**: Few-shot positive/negative image pairs are encoded by a frozen DINOv2, compressed via Q-Former into compact token sequences, concatenated with learnable label embeddings, and processed by a frozen LLaMA LLM. Learnable prompt tokens appended to the input are contextualized by the LLM and projected through a Visual Head MLP to produce task-specific visual prompts.

2. **Generalizable Object ReID**: The generated visual prompts are injected into each transformer layer of a frozen DINOv2 encoder. During self-attention, prompts dynamically reweight spatial features — amplifying ID-sensitive regions (logos, textures) and suppressing irrelevant areas (backgrounds, occlusions). The ViT parameters remain frozen; only prompts modulate the feature space.

Training loss: `L_total = L_ID + λ_ICL * L_ICL + λ_align * L_align`
- **L_ID**: Triplet loss on [CLS] embeddings (preferred over ArcFace/contrastive to preserve VFM generalization)
- **L_ICL**: Autoregressive pair-matching loss on the LLM (masked to label tokens only)
- **L_align**: Optimal transport distance between patch embeddings of positive/negative pairs for local feature consistency

## Datasets & evaluation

- **ShopID10K**: ~4% mAP improvement over DINOv2 and triplet-loss baselines across categories
- **MSMT17** (person ReID): Competitive with domain-specific methods despite no person-specific training
- **VeRi-776** (vehicle ReID): Strong generalization to vehicle domain
- **PetFace** (animal ReID): Superior fine-grained matching of fur patterns and facial markings
- **CUTE** (lab-controlled): Strong performance despite limited scale
- Qualitative results show VICP excels at finding fine-grained local details (e.g., distinguishing individual cats by facial markings, matching specific helmet models, identifying individual peppers)

## Limitations

- Category-aware inference is assumed: the object category must be known at test time (e.g., via a detector), limiting fully open-world deployment
- Performance depends on the quality and diversity of the few-shot support set — poorly chosen examples may lead to suboptimal prompts
- The LLM component adds inference overhead for prompt generation (though prompts are cached per category)
- Cross-category comparison is not addressed — the framework only compares within the same category
- Scalability to extremely fine-grained distinctions (e.g., individual grains of rice) is untested

## Key takeaways

The key idea is to synergize LLMs and vision foundation models (VFMs). Given a few positive/negative image pairs of a new category, a frozen LLM (LLaMA) infers semantic identity rules — e.g., "match objects based on logo placement and stitching patterns, ignore lighting variations." These inferred rules are translated into dynamic visual prompts that are injected into each transformer layer of a frozen DINOv2, modulating its self-attention to prioritize ID-discriminative local features (textures, shapes, logos) while suppressing irrelevant variations (viewpoint, illumination, background).

The framework uses a Query-based Connector (Q-Former, inspired by BLIP-2) to compress image pairs into compact token sequences for the LLM, and a Visual Head (2-layer MLP) to project LLM hidden states into visual prompts. Training combines triplet loss for global ID discrimination, an ICL loss for the LLM's pair-matching capability, and an optimal transport-based patch alignment loss for local feature consistency.

To support evaluation, the authors introduce ShopID10K, a dataset of 10K object instances across 34 daily-life categories from e-commerce platforms, with multi-view images, occlusions, and high inter-class similarity.

VICP bridges two research directions: (1) category-specific ReID methods that achieve high accuracy but cannot generalize, and (2) self-supervised models (DINOv2, MoCo) that generalize well semantically but lack fine-grained ID discrimination. The visual in-context prompting mechanism is distinct from text-based prompting (CLIP-style) — prompts are derived from visual examples rather than language descriptions, better capturing the fine-grained visual cues that define instance identity.

The Q-Former connector is inspired by BLIP-2's architecture for vision-language alignment. The approach also relates to visual prompt tuning (VPT) but generates prompts dynamically from in-context examples rather than learning fixed prompts.
