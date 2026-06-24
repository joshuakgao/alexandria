---
topic: Vision Language Models
slug: vision-language-models
---

# Vision Language Models

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "vision-language-models")
SORT year DESC
```

## Overview

# Vision-Language Models — Overview

*Last updated: 2026-06-19 | Sources: 25 papers*

## Current thesis

Vision-language models are experiencing rapid capability expansion across four dimensions: **modality coverage** (from images to video, audio, 3D, thermal), **task breadth** (from understanding to generation and reasoning), **spatial precision** (from global to pixel-level and region-level), and **scale** (from single images to long-context multi-modal reasoning). The field has transitioned from specialized models (image captioning, VQA) toward unified foundation models capable of diverse downstream tasks. Current frontiers: hallucination reduction, structured reasoning (step-by-step VLM), domain specialization (biology, 3D shapes), and efficiency. The dominant paradigm: freeze powerful foundation models (CLIP, LLM, diffusion) and learn lightweight adapters/mappings, enabling rapid capability scaling with minimal training cost.

## Key trends

**2021 — Foundation: Lightweight Bridging**
- [[papers/2021-clipcap-clip-prefix-image-captioning|ClipCap]] introduces prefix-tuning — a minimal trainable layer bridging frozen CLIP and frozen GPT-2. Core insight: powerful pre-trained models can be composed via learned mappings, not full finetuning. Sets the template for parameter-efficient VLM training.

**2023 — Expansion: Multimodality & Instruction Tuning**
- **Instruction tuning emerges as the dominant training paradigm.** [[papers/2023-improved-llava-visual-instruction-tuning|LLaVA-1.5]] systematically studies design choices and demonstrates that simple architectural improvements (MLP connectors, higher resolution) + instruction-following data yields SOTA generalist VLM.
- **Any-to-any modality becomes a goal.** [[papers/2023-nextgpt-any-to-any-multimodal|NExT-GPT]] and [[papers/2023-imagebind-one-embedding-space|ImageBind]] pursue unified multimodal spaces. ImageBind's key innovation: a single embedding space for text, image, video, audio, depth, thermal, egocentric — enabling cross-modal retrieval and reasoning.
- **Generation becomes native to VLMs.** [[papers/2023-gill-generating-images-multimodal|GILL]] demonstrates LLM-to-diffusion mapping, enabling text+image conditioned image generation within a dialogue system; first to support arbitrary image-text interleaving.
- **Survey work consolidates the field.** [[papers/2023-vision-language-models-vision-tasks-survey|VLM survey]] catalogs training paradigms (contrastive, generative, alignment), downstream tasks (VQA, captioning, grounding), and benchmarks.

**2024 — Specialization: Spatial Precision & Domain Focus**
- **Pixel-level grounding becomes accessible.** [[papers/2024-glamm-pixel-grounding-multimodal|GLaMM]] and [[papers/2024-lisa-reasoning-segmentation|LISA]] integrate segmentation into VLMs via SAM or embedding-as-mask, enabling pixel-level grounding, region captioning, and phrase grounding in a unified model. [[papers/2024-llm-seg-bridging-segmentation-language|LLM-Seg]] further shows LLMs can select among mask proposals from specialized models.
- **Unified task representation emerges.** [[papers/2024-florence2-unified-vision-tasks|Florence-2]] demonstrates that a single VLM can handle classification, detection, segmentation, captioning, grounding, OCR via task-specific prompt formatting. Challenges the specialization trend; shows one architecture suffices for diverse tasks.
- **Domain specialization accelerates.** [[papers/2024-bioclip-vision-foundation-tree-of-life|BioCLIP]] finetunes CLIP for all 454K biological species; [[papers/2024-bodyshapegpt-3d-body-shape-manipulation|BodyShapeGPT]] applies LLMs to 3D body shape manipulation. Signals that VLM+domain data can rapidly bootstrap specialized experts.
- **Quality assessment and agentic methods emerge.** [[papers/2024-seagull-image-quality-assessment-roi|SEAGULL]] shows VLMs can assess image quality at region level. [[papers/2024-genartist-multimodal-llm-generation-editing|GenArtist]] positions VLMs as agents for image editing and self-correction.

**2025 — Maturation: Reasoning, Hallucination, Scale**
- **Step-by-step visual reasoning becomes explicit.** [[papers/2025-llamav-o1-step-by-step-visual-reasoning|LlamaV-o1]] applies chain-of-thought reasoning to visual tasks, showing intermediate reasoning steps improve complex visual understanding.
- **Hallucination reduction becomes urgent.** [[papers/2025-skip-newline-reduce-hallucination|SKIP \\N]] and [[papers/2025-latent-space-steering-reduce-hallucinations|VTI]] tackle hallucination via prompt engineering and latent space steering. SKIP \\N's finding (newlines correlate with hallucinations) reveals unexpected model behavior. VTI's approach (precomputed feature vectors) offers inference-time intervention without retraining.
- **Localized understanding reaches maturity.** [[papers/2025-describe-anything-detailed-localized-captioning|Describe Anything]] demonstrates detailed multi-sentence region captioning with focal prompts and SSL data pipelines, closing the localization gap that plagued earlier VLMs.
- **Structured understanding for 3D scenes.** [[papers/2025-spatiallm-llm-structured-indoor-modeling|SpatialLM]] applies LLMs to 3D reconstruction from scene descriptions, bridging language understanding and 3D structure.
- **Large-scale integration.** [[papers/2025-internvl-large-vision-language-model|InternVL]] demonstrates that scaling VLM parameters and data yields consistent improvements across diverse benchmarks.

**2026 — Evaluation: Active Spatial Cognition & Benchmark Integrity**
- **Active exploration reveals new VLM limitations.** [[papers/2026/2026-theory-of-space/2026-theory-of-space|Theory of Space]] systematically evaluates foundation models (GPT-5.2, Gemini-3 Pro, Claude-4.5 Sonnet, Qwen3-VL, InternVL-3.5) on active spatial belief construction. All models show an active-passive gap — reasoning well over given observations but degrading when they must autonomously explore. Vision-based models are particularly affected by belief inertia (inability to revise spatial priors). This identifies active exploration as a fundamental capability gap beyond perception and reasoning.
- **The mirage effect challenges benchmark validity.** [[papers/2026/2026-mirage-illusion-visual-understanding/2026-mirage-illusion-visual-understanding|Mirage]] demonstrates that frontier VLMs confidently describe and reason about images never provided — a failure mode distinct from hallucination. Models in mirage-mode retain 70–80% of benchmark accuracy without any visual input, and a 3B text-only model outperforms all frontier VLMs and radiologists on chest radiology QA. Introduces B-Clean for filtering benchmark questions answerable without vision.
- **Quantitative physical reasoning exposes memorization over reasoning.** [[papers/2026/2026-quantiphy/2026-quantiphy|QuantiPhy]] (CVPR 2026) introduces the first benchmark for quantitative kinematic inference from video. Even the best VLM (ChatGPT-5.1, 53.1% MRA) barely matches human baselines (55.6%), and counterfactual analysis shows models ignore altered priors, anchoring predictions to memorized world knowledge rather than visual input.

**Evolution summary**: Lightweight bridging (2021) → instruction-tuned generalists (2023) → spatial+domain specialization (2024) → reasoning, hallucination mitigation, and 3D understanding (2025). Each phase adds capability while maintaining parameter efficiency through adapter/mapping paradigms.

## Open problems

### Hallucination, mirage behavior, and reliability
- How to reliably detect when a VLM is hallucinating vs. reasoning from genuine visual content? Mirage (2026) shows the problem extends beyond hallucination: models construct entirely false epistemic frames, describing non-existent images with full confidence.
- Can hallucinations be eliminated, or are they inherent to the scaling laws of LLMs?
- How to quantify hallucination types (object, attribute, spatial, relational)?
- The existence of distinct mirage vs. guess reasoning regimes suggests VLMs engage qualitatively different processing depending on whether they "believe" an image is present — what architectural or training interventions can address this?

### Spatial precision and grounding
- Pixel-level grounding requires SAM or dense segmentation models; can VLMs generate fine-grained spatial understanding end-to-end without specialized modules?
- How to handle partial occlusions and ambiguous spatial relationships in grounding?
- Can region-level understanding scale to dense pixel labeling (panoptic segmentation)?

### Long-context reasoning
- MMLongBench (2025) establishes that long-context VLM performance plateaus or degrades; is this fundamental or a training limitation?
- How does reasoning capability scale with sequence length (8K to 128K+ tokens)?
- Can VLMs maintain consistency and coherence over hundreds of images?

### Multimodal alignment and generation
- ImageBind unified text/image/audio/video/depth; can this space handle egocentric, thermal, and other modalities equally well?
- Generation fidelity: image generation from VLMs still lags specialized diffusion models; can this gap close via better LLM-to-generation mapping?
- Can VLMs generate structured outputs (3D meshes, CAD models) reliably?

### Generalization and domain transfer
- VLMs pretrained on web data; do they transfer to specialized domains (medical, legal, scientific) without finetuning?
- How much domain data is sufficient to adapt a general VLM (cf. BioCLIP's 10M biological images)?
- Can prompt engineering alone achieve domain-specific performance, or is finetuning necessary?

### Reasoning and planning
- LlamaV-o1 adds reasoning steps; does intermediate reasoning improve all visual tasks or only complex/ambiguous ones?
- How to balance reasoning depth (number of steps) with computational cost?
- Can VLMs support true multi-step planning (e.g., tool-use sequences) like their text-only LLM counterparts?
- ViewSuite (2026) shows frontier VLMs track local view transitions but cannot compose multi-turn spatial plans (best model 21.3% IVP success). Self-exploration with view graph distillation closes this gap for a 7B model — can similar approaches improve planning in other VLM domains?
- Theory of Space (ICLR 2026) reveals that VLMs degrade substantially when shifting from passive reasoning to active exploration, and suffer from belief inertia (inability to revise obsolete spatial priors). Can training objectives or architectural changes address these active cognition limitations?

## Contradictions and debates

- **Generalists vs. specialists:** Florence-2 shows a single model handles diverse tasks well; yet BioCLIP and domain-specific models outperform general models on specialized benchmarks. Likely both approaches coexist: general models for broad capability, specialists for highest accuracy.
- **Unified multimodal spaces vs. specialized encoders:** ImageBind advocates single embedding space for all modalities. In practice, separate encoders (vision, audio, text) finetuned to align seem equally viable and sometimes superior. Unclear if true unification is necessary.
- **Scale vs. efficiency:** InternVL, LLaVA-1.5 show that careful design + modest scale beats large models. Tension between scaling laws (bigger is better) and practical deployment (efficiency matters). Likely resolution: smaller specialized models for inference, large models for pretraining.
- **Frozen vs. finetuned foundations:** ClipCap, LLaVA maintain frozen CLIP/LLM; others finetune vision encoders. Frozen approach saves compute but may leave capability on table; full finetuning risks catastrophic forgetting. Adapter-based finetuning (LoRA, prefix-tuning) offers middle ground.

## Recommended reading order

1. [[papers/2021-clipcap-clip-prefix-image-captioning|ClipCap (2021)]] — foundational lightweight bridging approach; understand why parameter-efficient tuning works
2. [[papers/2023-improved-llava-visual-instruction-tuning|LLaVA-1.5 (2023)]] — systematic design study establishing instruction tuning as dominant paradigm; strong empirical insights
3. [[papers/2023-imagebind-one-embedding-space|ImageBind (2023)]] — multimodal alignment foundation; understand cross-modal embedding spaces
4. [[papers/2024-florence2-unified-vision-tasks|Florence-2 (2024)]] — unified task representation; shows single model can handle diverse vision tasks
5. [[papers/2024-glamm-pixel-grounding-multimodal|GLaMM (2024)]] — pixel-level grounding; understand spatial precision advances
6. [[papers/2025-skip-newline-reduce-hallucination|SKIP \\N (2025)]] — hallucination reduction via prompt engineering; practical insights into VLM behavior
7. [[papers/2025-describe-anything-detailed-localized-captioning|Describe Anything (2025)]] — region-level understanding maturity; see how focal prompts solve crop-context tradeoff
8. Explore concept pages: [[concepts/instruction-tuning|Instruction Tuning]], [[concepts/hallucination-reduction|Hallucination Reduction]], [[concepts/multimodal-generation|Multimodal Generation]]

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
