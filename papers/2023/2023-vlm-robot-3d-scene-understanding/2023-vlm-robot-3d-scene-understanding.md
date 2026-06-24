---
title: "Leveraging Large (Visual) Language Models for Robot 3D Scene Understanding"
authors: [William Chen, Siyi Hu, Rajat Talak, Luca Carlone]
year: 2023
venue: ""
tags: [embodied-ai, vision-language-models, scene-graphs]
url: ""
pdf: "[[2023-vlm-robot-3d-scene-understanding.pdf]]"
date_ingested: 2026-06-18
---

# Leveraging Large (Visual) Language Models for Robot 3D Scene Understanding

![[2023-vlm-robot-3d-scene-understanding-thumbnail.png]]

## Research gap

This paper systematically investigates how pre-trained language models and vision-language models can provide common-sense knowledge for abstract robot scene understanding — specifically room classification in 3D scene graphs. Given a scene graph (from Hydra or Kimera) where room nodes contain lists of detected objects, the paper compares a wide range of classification paradigms: language-only (zero-shot scoring, embedding-based fine-tuning, structured-language fine-tuning) and vision-language (zero-shot CLIP/BLIP/BLIP-2, fine-tuned approaches).

## Contributions

- Systematic comparison of language-only and vision-language paradigms for room classification in 3D scene graphs
- Informativeness-based object selection using entropy of p(r|o) to pick the most disambiguating objects for LM queries
- Structured-language approach that encodes spatial features (room dimensions, object positions) as serialized structured data for LM fine-tuning
- Demonstration that LM/VLM-based approaches exceed pure-vision and GNN baselines for abstract scene understanding
- Analysis from the same lab as Hydra (MIT SPARK/Carlone), directly targeting the room labeling gap in Hydra's scene graphs

## Method

**Object Selection**: Compute p(r|o) either from ground-truth co-occurrences or by querying LM with "A room containing o is called a(n) r." Select k objects with lowest entropy H_o (most informative/disambiguating).

**Language-Only — Zero-shot**: Score query strings "A room containing o1, o2, ... and ok is called a(n) r" via LM log probability. Highest-scoring room label wins.

**Language-Only — Embedding-based**: Feed query "This room contains o1, o2, ... and ok" into LM, extract embedding, classify with shallow MLP.

**Language-Only — Structured-data**: Serialize room dimensions + object labels/counts/positions into structured text. Fine-tune encoder-decoder LM to decode room label probabilities.

**Vision-Language — Zero-shot**: CLIP/BLIP/BLIP-2 embed room label strings and room images; match by cosine similarity. Generative VLMs prompted with "What type of room is this?" optionally prepended with object descriptions.

**Vision-Language — Fine-tuned**: Fine-tune VLMs on room images + object descriptions for room classification.

## Datasets & evaluation

Best language-only: ~70% accuracy (structured-language with spatial features). Best vision-language: ~70% accuracy (fine-tuned with object descriptions). Both exceed pure-vision baselines and GNN classifiers. Zero-shot LM: 27-53% depending on model. Key room types (bathroom, kitchen, bedroom) reach 79-97% accuracy. Informativeness-based selection consistently improves over using all objects. Structured-language outperforms natural-language queries by incorporating spatial features that natural language cannot easily express.

## Limitations

- Room classification only — does not address object labeling, spatial relationship inference, or other scene understanding tasks
- Assumes access to ground-truth object labels in experiments; real detection would introduce noise
- The ~70% accuracy ceiling suggests room classification remains a hard problem, especially for ambiguous room types (open floor plans, multi-use spaces)
- Informativeness-based selection requires knowing p(r|o) — either from data or LM queries — which may not generalize to novel environments
- No evaluation of how classification errors propagate to downstream planning or navigation tasks

## Key takeaways

A key insight is the **informativeness-based object selection**: rather than feeding all detected objects into the LM query, the method selects the k most informative objects — those with the lowest entropy p(r|o) distribution, i.e., objects that strongly imply specific room types (a toilet implies bathroom more than a chair implies anything). This draws from Grice's conversational maxims and rational speech acts.

The best approaches in both categories achieve ~70% room classification accuracy on the Matterport3D dataset, exceeding pure-vision baselines and graph neural network classifiers. The structured-language approach (fine-tuned LM on serialized spatial data including room dimensions and object positions) outperforms natural-language-only methods, showing that quantitative spatial features are useful even when expressed as structured text rather than natural language.

This paper comes from the same lab as Hydra (MIT SPARK, Carlone) and directly addresses Hydra's limitation: room nodes in the scene graph lack semantic labels. It establishes the LM-based room classification approach that HOV-SG later adopts (prompting LM with contained objects to infer room type) and that MoMa-LLM refines with open-vocabulary LLM classification.

The informativeness-based object selection is a principled precursor to the retrieval optimization seen in later work — STaR's Information Bottleneck, MemoryEQA's entropy-based retrieval, and MoMa-LLM's structured knowledge extraction all address the same core problem: what information to present to a language model for optimal reasoning.

The finding that structured-language (serialized spatial data) outperforms natural-language queries anticipates MoMa-LLM's demonstration that how you encode scene information for the LLM matters as much as what information you encode.
