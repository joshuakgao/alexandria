---
title: "NExT-GPT: Any-to-Any Multimodal LLM"
authors: [Shengqiong Wu, Hao Fei, Leigang Qu, Wei Ji, Tat-Seng Chua]
year: 2023
venue: "ICML 2024"
tags: [vision-language-models, diffusion-models]
url: ""
pdf: "[[2023-nextgpt-any-to-any-multimodal.pdf]]"
date_ingested: 2026-06-18
---

# NExT-GPT: Any-to-Any Multimodal LLM

![[2023-nextgpt-any-to-any-multimodal-thumbnail.png]]

## Research gap

NExT-GPT is an end-to-end general-purpose any-to-any multimodal LLM system that connects an LLM with multimodal adaptors and different diffusion decoders, enabling perception and generation across arbitrary combinations of text, images, videos, and audio. The system comprises three stages: multimodal encoding (using ImageBind to encode diverse inputs), LLM understanding and reasoning (Vicuna-based processing with modality signal tokens), and multimodal generation (using specialized diffusion models like Stable Diffusion, Zeroscope, and AudioLDM). The approach is highly parameter-efficient, fine-tuning only 1% of parameters in projection layers, facilitating convenient expansion and low-cost training while maintaining strong capabilities across modalities.

## Contributions

- First framework enabling truly any-to-any multimodal generation (any combination of input and output modalities)
- Introduces Modality-switching Instruction Tuning (MosIT) with carefully curated multimodal interaction datasets
- Achieves parameter efficiency by tuning only 1% of parameters while maintaining strong cross-modal performance
- Demonstrates that unified multimodal understanding can guide generation across diverse output modalities

## Method

Three-stage pipeline: (1) ImageBind encoders for six modalities converted to language-like tokens via projection, (2) Vicuna LLM for reasoning producing both text and modality signal tokens, (3) Specialized diffusion models for each output modality guided by signal tokens from the LLM.

## Datasets & evaluation

Shows strong capabilities in multimodal understanding and generation across diverse modality combinations on multiple benchmarks, with particular strength in understanding complex multimodal instructions.

## Limitations

Reliance on pre-trained ImageBind may limit novel modality combinations not well-represented in ImageBind's training. Generation quality depends heavily on quality of underlying diffusion models. Limited evaluation on highly complex cross-modal reasoning tasks.

---

## Key takeaways

Builds on ImageBind unified embedding space and extends prior single-modality generation work (like GILL) to full multimodal any-to-any capability. Demonstrates scalability of the unified embedding approach.
