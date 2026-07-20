---
title: "JARVIS-VLA: Post-Training Large-Scale Vision Language Models to Play Visual Games with Keyboards and Mouse"
authors: [Muyao Li, Zihao Wang, Kaichen He, Xiaojian Ma, Yitao Liang]
year: 2025
venue: "ACL 2025 Findings"
tags: [vision-language-action, embodied-ai]
url: "https://arxiv.org/abs/2503.16365"
date_ingested: 2026-07-18
---

# JARVIS-VLA: Post-Training Large-Scale Vision Language Models to Play Visual Games with Keyboards and Mouse

![[2025-jarvis-vla-thumbnail.png]]

## Research gap
Prior VLA approaches focus almost exclusively on action post-training — fine-tuning VLMs directly on trajectory data via imitation learning. This neglects the foundation model itself: pretrained VLMs lack environment-specific world knowledge, visual recognition, and spatial grounding needed for effective decision-making. Learning these capabilities purely from action-labeled trajectory data is inefficient and limited by the scarcity of large-scale action-labeled datasets.

## Contributions
- Introduces **ActVLP** (Act from Visual Language Post-Training), a multi-stage training paradigm that enhances the VLM with environment-specific vision-language tasks *before* action fine-tuning.
- Develops **JARVIS-VLA**, the first VLA model in Minecraft capable of following human instructions across 1,000+ atomic tasks (crafting, smelting, cooking, mining, killing).
- Demonstrates a **40% improvement** over the best agent baseline on a diverse set of atomic tasks in the MCU benchmark.
- Investigates **scaling laws** for VLA models, showing that expanding non-trajectory vision-language data during post-training yields significant downstream improvements.
- Open-sources code, models, and datasets.

## Method
JARVIS-VLA uses a LLaVA-like architecture (ViT encoder + MLP projection + language model) with multi-image input for temporal context (non-Markovian). An action decoder maps discrete and continuous actions to tokens repurposed from the 51 least-used vocabulary entries (22 for mouse, 29 for keyboard), avoiding tokenizer retraining.

The **ActVLP training pipeline** has three stages:
1. **Stage I — World Knowledge Post-Training**: Fine-tune only the language model on ~277K text-only Minecraft knowledge QA entries (vision modules frozen).
2. **Stage II — Visual Knowledge & Spatial Grounding**: Fully unfreeze the VLM and fine-tune on 35K captioning/VQA keyframes and 404K spatial grounding data points using Minecraft observations.
3. **Stage III — Action Post-Training**: Fine-tune the language model on trajectory data via imitation learning with action chunking (vision modules frozen). Training data includes OpenAI contractor gameplay, VPT/JARVIS-1 rollouts, and 6.4M synthesized GUI-task entries (~10B tokens total).

Base VLMs tested: Qwen2-VL-7B and LLaVA-Next. Training on 32×A800-80G GPUs; inference on a single RTX 3090.

## Datasets & evaluation
**Benchmark**: MCU Benchmark (Lin et al., 2023) with four categories — Mine Blocks, Kill Entities, Craft Items, Smelt Items — each containing 5+ tasks evaluated over 30+ trials.

**Key results** (JARVIS-VLA-Qwen2-VL vs. baselines):
- Mine Blocks: 0.88 avg success (vs. 0.56 best baseline GROOT)
- Kill Entities: 0.95 avg success (vs. 0.52 best baseline)
- Craft Items: 0.77 avg success (vs. 0.57 best baseline STEVE-1) — more than double some baselines
- Smelt Items: 0.70 avg success (vs. 0.42 best baseline)
- ActVLP post-training outperforms imitation-learning-only (Qwen2-VL IL) by 15%+ while using only 21% of the trajectory data.
- Ablation shows separating VL post-training from action learning (3-stage) dramatically outperforms co-training everything in one stage.
- Among non-trajectory datasets, spatial grounding contributes most to downstream task performance.

## Limitations
- Inference throughput is constrained by the large parameter size of the VLA.
- The action space is specific to Minecraft (keyboard + mouse); generalization to other environments or embodiments is not demonstrated.
- Relies on large-scale curated non-trajectory datasets specific to the Minecraft domain — creating equivalent datasets for new domains requires significant effort.
- Evaluation is limited to atomic tasks; long-horizon compositional tasks (e.g., ObtainDiamond end-to-end) are not benchmarked.

## Key takeaways
- Post-training VLMs on environment-specific vision-language tasks *before* action fine-tuning is substantially more effective than direct imitation learning or co-training, even with less trajectory data.
- The multi-stage separation matters: co-training VL tasks and trajectories in a single stage significantly underperforms the staged ActVLP approach, suggesting that dedicated VL post-training builds foundational capabilities that action learning can then leverage.
- Spatial grounding is the single most impactful non-trajectory capability for downstream decision-making — more so than world knowledge QA or visual captioning.
- Even raw pretrained VLMs (Qwen2-VL without any post-training), when fine-tuned on downstream tasks, outperform several prior Minecraft agents trained with large-scale imitation learning, highlighting the strength of modern VLM backbones.
