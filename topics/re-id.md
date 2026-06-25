---
topic: Re-ID
slug: re-id
---

# Re-ID

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "re-id")
SORT year DESC
```

## Overview

*Last updated: 2026-06-25 | Sources: 5 papers*

## Current thesis

Re-identification is undergoing two parallel shifts toward generalization. First, across *modalities*: AIO (2024) shows that a frozen foundation model with lightweight multimodal tokenizers can handle arbitrary combinations of RGB, IR, sketch, and text queries in a single framework, eliminating the need for separate cross-modal models. Second, across *object categories*: VICP (2025) demonstrates that LLM in-context reasoning can generate visual prompts enabling few-shot ReID on unseen categories without parameter updates. Both approaches converge on the strategy of keeping large pre-trained encoders frozen and learning only lightweight adaptation layers, preserving zero-shot generalization.

## Key trends

- **Frozen foundation models as backbone**: Both AIO (LAION-2B ViT) and VICP (DINOv2 + LLaMA) keep large encoders frozen, training only lightweight adapters — this preserves zero-shot capabilities that fine-tuning would destroy
- **From category-specific to generalizable**: VICP shows a single framework can generalize across object categories using in-context examples
- **From modality-specific to modality-agnostic**: AIO shows a single framework can handle any combination of RGB, IR, sketch, and text — real-world deployments face uncertain query modalities
- **Foundation model synergy**: Combining LLMs (semantic reasoning) with VFMs (visual features) bridges semantic understanding and fine-grained discrimination
- **Missing modality synthesis**: AIO's Channel Augmentation (IR) and Lineart (sketch) enable training without complete multimodal data — synthetic bridging reduces domain gaps
- **Dynamic visual prompting**: VICP injects task-specific prompts into frozen transformers rather than fine-tuning
- **Re-ID as a component in downstream pipelines**: Koshkina & Elder (CVPRW 2024) use Centroid-ReID features for unsupervised main subject filtering in sports video tracklets — iterative Gaussian outlier rejection removes occluded frames without frame-level annotations, demonstrating that re-id embeddings have utility beyond identity matching as general-purpose person appearance features
- **Jersey number as identity signal**: Liu & Bhanu (CVPRW 2019) frame jersey number recognition as a player identification problem, using a pose-guided R-CNN to detect and classify digits on jerseys. This complements appearance-based ReID — jersey numbers provide a semantically explicit identity cue that is invariant to lighting and viewpoint changes but requires number visibility
- **Uncertainty-aware identity signals**: Grad (CVPRW 2025) adds calibrated uncertainty to jersey number predictions via Dirichlet modeling, enabling the system to abstain when numbers are not reliably visible. This is critical for ReID integration — an overconfident wrong number causes identity switches, while an honest "uncertain" allows fallback to appearance-based ReID

## Open problems

- Fully open-world ReID without assuming known object categories at test time
- Combining cross-category generalization (VICP) with cross-modality generalization (AIO) in a single framework
- Reducing LLM overhead for prompt generation in latency-sensitive deployments
- Handling extreme appearance changes (season, aging, damage) across long time gaps
- Scaling to massive galleries with millions of instances
- Real-world multimodal data collection — most benchmarks have limited paired multimodal coverage

## Contradictions and debates

- **Appearance vs. semantic identity cues**: Appearance-based ReID (AIO, VICP) captures holistic visual similarity, while jersey number recognition (Liu & Bhanu 2019, Koshkina & Elder 2024, Grad 2025) provides explicit identity. Grad's uncertainty-aware approach enables principled fusion — use jersey numbers when confidence is high, fall back to appearance-based ReID when uncertain — but this integration remains untested
- **Specialization vs. generalization**: Domain-specific ReID models still outperform generalizable approaches on their target domains; the question is whether flexibility justifies the accuracy trade-off
- **Frozen vs. fine-tuned**: Both AIO and VICP freeze the backbone, but this imposes a performance ceiling. When sufficient data exists, fine-tuning may still win.
- **Synthetic vs. real modality data**: AIO's synthetic IR/sketch bridges domain gaps but may not capture real-world noise patterns. The gap between synthetic and real multimodal evaluation remains unclear.

## Recommended reading order

For someone new to this topic:
1. **AIO** — Understand the multimodal ReID challenge: why existing cross-modal methods fail with uncertain modalities, and how a frozen foundation model + tokenizers solves it
2. **VICP** — See the complementary challenge of cross-category generalization: how LLM reasoning generates visual prompts for unseen object types

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
