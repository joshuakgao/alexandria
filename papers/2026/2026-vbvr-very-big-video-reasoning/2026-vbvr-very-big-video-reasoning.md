---
title: "A Very Big Video Reasoning Suite"
authors: [Maijunxian Wang, Ruisi Wang, Juyi Lin, Ran Ji, Thaddäus Wiedemer, Qingying Gao, Hokin Deng, Zhongang Cai]
year: 2026
venue: ""
tags: [world-models]
url: ""
pdf: "[[2026-vbvr-very-big-video-reasoning.pdf]]"
date_ingested: 2026-06-18
---

# A Very Big Video Reasoning Suite

![[2026-vbvr-very-big-video-reasoning-thumbnail.png]]

## Research gap

VBVR (Very Big Video Reasoning) introduces an unprecedented 2.015M-sample dataset for large-scale world reasoning research, grounded in cognitive science theory. The dataset spans 200 curated tasks organized around five foundational cognitive capabilities: Abstraction, Knowledge, Spatiality, Perception, and Transformation. Alongside the dataset, VBVR-Bench provides a verifiable evaluation framework using rule-based scorers and human-aligned metrics rather than VLM judges, enabling reproducible diagnosis of world reasoning capabilities. A large-scale scaling study reveals early signs of emergent generalization to unseen reasoning tasks, establishing a foundation for systematic research into video as a substrate for understanding world dynamics—not just visual realism.

## Contributions

- **Unprecedented dataset scale**: 2.015M images, 1.007M video clips; ~1000× larger than existing world reasoning benchmarks (prior max: 12.8K samples)
- **Cognitive architecture taxonomy**: 5 foundational faculties (Abstraction, Knowledge, Spatiality, Perception, Transformation) grounded in human cognitive science theory
- **Systematic task design**: 200 diverse, community-contributed tasks with unified input/output templates; ensures diversity while maintaining consistency for scaling
- **Verifiable evaluation framework**: VBVR-Bench enforces rule-based scorers + human alignment over VLM-as-judge paradigms; enables interpretable, reproducible diagnosis
- **Scaling behavior findings**: Large-scale empirical study demonstrates emergent generalization—models improve on unseen reasoning tasks with more training data

## Method

**Dataset pipeline:**
1. **Task specification**: 50+ researchers contribute reasoning task semantics across five cognitive capabilities
2. **Template standardization**: Unified input/output specification (questions, video, answers) without constraining task design
3. **Expert quality control**: Human inspection ensures task correctness before automated processing
4. **Scalable generation**: Cloud-based automated pipeline generates randomized training examples per task; easily extends to new tasks

**Cognitive capability framework:**
- **Spatiality**: spatial relationships, layout reasoning, object localization
- **Transformation**: object motion, state changes, visual dynamics
- **Knowledge**: semantic understanding, real-world reasoning, commonsense
- **Abstraction**: generalization, categorization, pattern extraction
- **Perception**: visual detail, fine-grained discrimination, sensory reasoning

**Evaluation:**
- VBVR-Bench: rule-based scorers for each task type, human-aligned metrics
- Verifiable scoring eliminates model-judging ambiguity
- In-domain and out-of-domain evaluation tracks generalization

## Datasets & evaluation

**Scale comparison:**
- VBVR-Dataset: 2.015M samples (with 1M train / 7.5K test split)
- Prior largest dataset: ~12.8K samples combined across 9 existing benchmarks
- 155× scale advantage

**Scaling behavior:**
- In-domain: 0.974 Parikh score (near human); strong improvement from 50K to 200K training data
- Out-of-domain: 0.610 score; demonstrates non-trivial generalization gap but emergent unseen-task capability
- Early signs of emerging generalization on novel reasoning patterns

**Benchmark performance:**
- Five-capability radar chart shows relative model strength across cognitive faculties
- Enables diagnosis of specific reasoning bottlenecks (e.g., Spatiality vs. Abstraction)

## Limitations

- **Generalization to real video**: Most tasks are synthetic or curated; robustness on in-the-wild video reasoning not extensively evaluated
- **Task distribution bias**: Despite community contribution, coverage of all reasoning modalities may be uneven; some cognitive capabilities may have fewer exemplars
- **Scaling laws extrapolation**: Do trends observed at 2M samples continue to 10B+ samples? Extrapolation beyond current scale unclear
- **Human baseline variation**: Human baseline (0.974 in-domain) assumes "easy" task definitions; harder out-of-domain tasks may require richer reasoning
- **Model architecture design**: Scaling study uses primarily generation models (Veo, Sora); how discriminative and other architectures scale remains open
- **Long-horizon reasoning**: Current tasks may emphasize short-range spatiotemporal reasoning; multi-step causal chains over extended video remains underexplored
- **Integration with physics**: How VBVR's task-based reasoning integrates with V-JEPA's emergent physics understanding is unexplored

## Key takeaways

Complements recent work on reasoning and world models (Wiedemer et al. 2025, Tong et al. 2025, Guo et al. 2025, Garrido et al. 2025) by providing the first **large-scale training dataset** for world reasoning at scale. While prior work has evaluated video generation models' reasoning and physics abilities in zero-shot settings, VBVR enables **supervised training studies** at scale, analogous to how large language model datasets enabled scaling laws. The [[2025-intuitive-physics-self-supervised-pretraining|V-JEPA physics paper]] demonstrates *how* physics understanding emerges from representation learning; VBVR provides a comprehensive benchmark to *measure* what world reasoning capabilities emerge from training.
