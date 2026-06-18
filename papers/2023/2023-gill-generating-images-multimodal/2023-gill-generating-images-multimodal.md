---
title: "Generating Images with Multimodal Language Models"
authors: [Jing Yu Koh, Daniel Fried, Ruslan Salakhutdinov]
year: 2023
venue: "NeurIPS"
tags: [vision-language-models, image-generation]
url: ""
pdf: "[[2023-gill-generating-images-multimodal.pdf]]"
date_ingested: 2026-06-18
---

# Generating Images with Multimodal Language Models

![[2023-gill-generating-images-multimodal-thumbnail.png]]

## Research gap

GILL demonstrates the first approach capable of conditioning on arbitrarily interleaved image and text inputs to generate coherent multimodal outputs. By carefully fusing a frozen text-only LLM with pre-trained image encoders and decoders through efficient mapping networks, GILL enables a single model to perform image retrieval, novel image generation, and multimodal dialogue. The approach leverages an efficient mapping network that grounds the LLM to an off-the-shelf text-to-image generation model by translating hidden representations into the visual model's embedding space. GILL outperforms Stable Diffusion on longer-form text understanding, including dialogue and discourse, demonstrating superior performance on dialogue-conditioned image generation tasks.

## Contributions

- First multimodal LLM capable of accepting and generating both images and text in arbitrary interleaved sequences
- Introduces efficient mapping networks to bridge frozen LLMs with visual generation models without full retraining
- Demonstrates superior performance to Stable Diffusion on dialogue-conditioned image generation and extended text understanding
- Enables novel multimodal dialogue capabilities with joint image-text reasoning

## Method

Employs frozen components throughout: a pre-trained LLM (e.g., GPT-2), a pre-trained vision encoder (CLIP), and pre-trained diffusion-based image generation models. The key innovation is mapping networks that translate between embedding spaces, allowing the LLM to control image generation through learned transformations.

## Datasets & evaluation

Superior performance compared to Stable Diffusion on dialogue-conditioned image generation. Demonstrates strong multimodal understanding and generation capabilities on tasks involving complex discourse and interleaved instructions.

## Limitations

Relies on frozen pre-trained models which may limit adaptability. Performance on very complex multimodal reasoning tasks not extensively evaluated. Scalability to larger language models not thoroughly explored.

---

## Key takeaways

Extends prior work on vision-language grounding (CLIP) by adding generative capabilities. Precedes later models like NExT-GPT in showing how to effectively combine frozen pretrained models for multimodal tasks.
