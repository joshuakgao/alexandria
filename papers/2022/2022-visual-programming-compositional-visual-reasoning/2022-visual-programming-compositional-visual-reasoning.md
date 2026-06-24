---
title: "Visual Programming: Compositional visual reasoning without training"
authors: [Tanmay Gupta, Aniruddha Kembhavi]
year: 2022
venue: "CVPR 2023"
tags: [agentic-mllms]
url: ""
pdf: "[[2022-visual-programming-compositional-visual-reasoning.pdf]]"
date_ingested: 2026-06-18
---

# Visual Programming: Compositional visual reasoning without training

![[2022-visual-programming-compositional-visual-reasoning-thumbnail.png]]

## Research gap

VisProg introduces a neuro-symbolic approach to compositional visual reasoning where LLMs (GPT-3) generate Python-like programs composed of modular vision and language components, which are then executed to solve visual tasks without any task-specific training. Rather than training specialized models for each task, VisProg uses in-context learning to teach GPT-3 how to compose existing vision models (object detectors, CLIP classifiers, Stable Diffusion), language models, and image processing routines into coherent programs. Given a natural language instruction and few in-context examples, GPT-3 generates executable Python programs. Each line invokes specialized modules producing intermediate outputs consumed downstream. The approach demonstrates flexibility across diverse tasks—compositional VQA (2.7 point gain), zero-shot NLVR (62.4% accuracy without image pair training), knowledge tagging, and language-guided image editing. Crucially, VisProg produces interpretable visual rationales (intermediate step outputs) enabling error diagnosis and user intervention.

## Contributions

- **LLM-based program generation**: Uses in-context learning to teach LLMs to compose existing vision/language modules into programs
- **Training-free adaptation**: No task-specific training; adaptation via in-context examples only
- **Modular composition**: Flexibly composes diverse modules (neural models, image processing, Python functions) without end-to-end training
- **Interpretable rationales**: Produces programs and intermediate outputs serving as visual explanations
- **Task flexibility**: Single framework handles diverse tasks (VQA, NLVR, tagging, editing) without modification
- **Long-tail capability**: Extends AI systems to complex tasks in the long tail beyond typical benchmarks

## Method

**Program generation**:
1. Provide LLM (GPT-3) with:
   - Natural language instruction/question
   - Few in-context examples showing instruction → program pairs
   - List of available modules (functions)
2. LLM generates Python-like program composing modules
3. Program is executed to produce outputs

**Supported modules**:
- **Neural models**: OWL-ViT (open-vocabulary detection), DSFD (face detection), MaskFormer (segmentation), CLIP (classification), ViLT (VQA), Stable Diffusion (image generation)
- **Image processing**: OpenCV routines (filtering, morphological ops, etc.)
- **Language models**: GPT-3 for knowledge retrieval and reasoning
- **Python functions**: Arithmetic, logical operations, list manipulation

**Program execution**:
- Each line processes outputs from previous lines
- Failures in any line tracked; LLM can regenerate program with error context
- Intermediate outputs compose to form final answer

**Key advantages over prior approaches**:
- **vs. Neural Module Networks**: Higher-level abstractions; no end-to-end training; incorporates non-differentiable operations
- **vs. End-to-end models**: Handles long-tail tasks; provides interpretability; modular and extensible
- **vs. Manual programming**: Automatic program generation via LLM; handles diverse tasks with single system

## Datasets & evaluation

**Compositional VQA**:
- 2.7 point improvement over base VQA model
- Demonstrates compositional reasoning capability

**NLVR (Natural Language Visual Reasoning)**:
- 62.4% zero-shot accuracy without training on image pairs
- Leverages in-context examples despite never seeing pair data during LLM pretraining

**Knowledge tagging**:
- Strong results on tagging objects using factual knowledge
- Example: "Tag the 7 main characters on Big Bang Theory"—combines face detection, knowledge retrieval, and classification

**Language-guided image editing**:
- Delightful qualitative results on editing tasks like "Hide Daniel Craig with 8" and "Create color pop of Barack Obama"
- Demonstrates creative composition of editing modules

**Interpretability**:
- Generated programs serve as clear rationales
- Users can verify logical correctness and diagnose failures
- Intermediate step outputs enable user-driven instruction refinement

## Limitations

- How does performance scale with program length and nesting depth?
- Can LLMs generalize to novel module combinations not in training distribution?
- What is the failure mode when required modules are unavailable?
- How does performance vary with quality and quantity of in-context examples?
- Can error messages from failed module executions improve program generation?

## Key takeaways

VisProg advances neural module networks by removing training requirements and enabling incorporation of pre-trained SOTA models. It relates to prompt-based learning (PICa, Socratic Models) but focuses on executable programs rather than direct text generation. The neuro-symbolic approach echoes classical AI (logic programming) adapted for modern LLMs and vision/language models.
