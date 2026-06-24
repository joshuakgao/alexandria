---
title: "OpenEQA: Embodied Question Answering in the Era of Foundation Models"
authors: [Anurag Ajay, Xiaohan Zhang, Pranav Putta, Sriram Yenamandra, Mikael Henaff, Sneha Silwal, Paul Mcvay, Oleksandr Maksymets, Sergio Arnaud, Karmesh Yadav, Qiyang Li, Ben Newman, Mohit Sharma, Vincent Berges, Shiqi Zhang, Pulkit Agrawal, Yonatan Bisk, Dhruv Batra, Mrinal Kalakrishnan, Franziska Meier, Chris Paxton, Sasha Sax, Aravind Rajeswaran]
year: 2024
venue: "CVPR 2024"
tags: [embodied-ai]
url: ""
pdf: "[[2024-openeqa.pdf]]"
date_ingested: 2026-06-18
---

# OpenEQA: Embodied Question Answering in the Era of Foundation Models

![[2024-openeqa-thumbnail.png]]

## Research gap

OpenEQA is the first open-vocabulary benchmark for Embodied Question Answering, reformulating EQA as understanding an environment well enough to answer natural language questions about it. The benchmark contains 1,600+ human-generated questions across 180+ real-world environments (ScanNet, HM3D), supporting two settings: **episodic memory EQA** (EM-EQA, answering from recorded observation history — relevant to smart glasses) and **active EQA** (A-EQA, exploring to gather information — relevant to mobile robots).

## Contributions

- First open-vocabulary EQA benchmark with 1,600+ untemplated questions from 180+ real-world environments, supporting both episodic memory and active exploration settings
- LLM-Match automatic evaluation metric with strong human agreement, enabling scalable open-vocabulary answer scoring
- Comprehensive baseline evaluation of foundation models (GPT-4V, Claude-3, Gemini-1.5) and Socratic approaches (captioning + LLM, scene graph + LLM)
- Identification of spatial understanding as the critical deficiency: foundation models fail at spatial questions even with visual input
- Seven question categories: object recognition, attribute recognition, object state recognition, object localization, spatial understanding, functional reasoning, world knowledge

## Method

**Dataset**: Questions sourced from human annotators watching episode videos from ScanNet and HM3D. Each question annotated by ≥3 individuals for validity and answer diversity. Categories span attributes ("What color are the blinds?"), spatial ("What is left of the kitchen?"), functional ("Where can I get pop drinks?"), and world knowledge.

**EM-EQA**: Agent receives episode history H = {(I_t, D_t, [R|t]_t)} (RGB, depth, camera pose at each timestep) and must answer question Q. No active exploration — must reason from memory alone.

**A-EQA**: Agent spawned in simulated environment, must take exploratory actions to gather information before answering. Scored on both answer correctness and exploration efficiency.

**LLM-Match**: Given question Q, ground truth answer A*, and model answer A, an LLM scores whether A is correct, partially correct, or incorrect. Validated via double-blind study showing high correlation with human preferences.

**Baselines**: (1) Blind LLM — no visual input; (2) Frame captioning + LLM — caption frames, pass text to LLM; (3) Scene graph + LLM — build scene graph from frames, query LLM; (4) Direct VLM — pass frames directly to GPT-4V/Claude-3/Gemini-1.5.

## Datasets & evaluation

GPT-4V: 48.5% aggregate LLM-Match (best baseline). Human: 85.9%. Scene graph + GPT-4: competitive with direct VLM approaches. Blind GPT-4: ~30% (surprising baseline from world knowledge alone). Spatial understanding questions: all models near blind-LLM performance, indicating visual input provides minimal spatial benefit. Attribute and object recognition: strongest categories for VLMs. Functional reasoning and world knowledge: moderate performance, benefiting from LLM priors.

## Limitations

- 85.9% human performance suggests some questions are ambiguous or subjective even for humans
- Spatial understanding gap is identified but not analyzed in depth — what types of spatial reasoning fail and why?
- EM-EQA assumes a complete episode history is given; real smart glasses would need to manage memory under storage constraints
- A-EQA evaluation in simulation (Habitat) — transfer to real-world active exploration untested
- LLM-Match metric depends on the scoring LLM's quality; as LLMs improve, the metric may need recalibration
- No temporal questions ("When did I last see X?") — the benchmark focuses on spatial/semantic understanding, not temporal memory

## Key takeaways

The key innovation beyond the dataset is the **LLM-Match** evaluation metric: an LLM scores open-ended answers against human-annotated ground truth, enabling automatic evaluation of free-form responses. A double-blind user study shows strong correlation between LLM-Match and human judgment, removing the need for expensive human evaluation.

Baseline evaluations reveal that GPT-4V achieves 48.5% aggregate score — impressive but far below the human-level 85.9%. All foundation models struggle particularly with spatial understanding questions (object positions, relative locations), often performing no better than "blind" LLMs that receive no visual input. This highlights that spatial reasoning from visual observations remains a major unsolved challenge.

OpenEQA is the standard evaluation benchmark that multiple papers in the embodied memory topic report results on: R⁴ achieves SOTA on OpenEQA with structured 4D memory, MemoryEQA reports results on it, and LMEE uses it for memory-based QA evaluation. The benchmark's identification of spatial understanding as the critical gap directly motivates the structured memory approaches (R⁴'s metric-anchored object database, Mind Palace's episodic world instances) that encode explicit spatial relationships rather than relying on VLMs to infer them from images.

The EM-EQA setting formalizes what most embodied memory systems implicitly target: answering questions from recorded observations without re-exploration. The A-EQA setting connects to LMEE's multi-goal navigation + QA and Mind Palace's active exploration.

The LLM-Match metric has become the standard for evaluating open-vocabulary embodied QA, replacing the closed-vocabulary accuracy metrics of earlier benchmarks (MT-EQA, EQA-v1).
