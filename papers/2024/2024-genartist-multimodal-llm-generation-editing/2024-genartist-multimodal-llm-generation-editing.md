---
title: "GenArtist: Multimodal LLM as an Agent for Unified Image Generation and Editing"
authors: [Zhenyu Wang, Aoxue Li, Zhenguo Li, Xihui Liu]
year: 2024
venue: "NeurIPS 2024"
tags: [vision-language-models, image-generation, agentic-mllms]
url: ""
pdf: "[[2024-genartist-multimodal-llm-generation-editing.pdf]]"
date_ingested: 2026-06-18
---

# GenArtist: Multimodal LLM as an Agent for Unified Image Generation and Editing

![[2024-genartist-multimodal-llm-generation-editing-thumbnail.png]]

## Research gap

GenArtist reimagines image generation and editing as an agent problem, using a multimodal LLM as an "artist" that decomposes complex user requirements into simpler sub-problems, plans operations, executes them through tool invocation, and performs verification and self-correction. Rather than relying on a single specialized model, GenArtist coordinates diverse existing generation and editing models through LLM reasoning. For complex problems, the MLLM decomposes them into a tree-structured plan with step-by-step verification and correction mechanisms, enabling handling of intricate text prompts and complex editing requirements that individual models struggle with. The system integrates a comprehensive tool library and leverages the agent's reasoning for tool selection and execution, achieving greater accuracy than SDXL and DALL-E 3 on text-to-image generation and excelling at complex image editing tasks.

## Contributions

- Introduces agent-based framework for image generation/editing with LLM-driven planning and reasoning
- Proposes tree-structured decomposition of complex generation tasks into verifiable sub-operations
- Integrates verification and self-correction mechanisms for reliable image generation
- Achieves superior performance to SDXL and DALL-E 3 on complex generation tasks

## Method

MLLM agent framework with planning, tool selection, execution, and verification stages. For complex problems, constructs tree-structured plans decomposing tasks into simpler components with step-by-step verification.

## Datasets & evaluation

Superior performance on complex text-to-image generation compared to SDXL and DALL-E 3, and excellent results on image editing tasks requiring multi-step reasoning and modification.

## Limitations

Approach depends on quality of underlying generation models; limited to current tool library. Computational cost of planning and verification stages may be significant. Evaluation on subjective quality metrics of generated images limited compared to automatic metrics.

---

## Key takeaways

Extends vision-language models to agent-based reasoning for creative tasks, building on instruction-tuning approaches while introducing planning and verification mechanisms inspired by reasoning in language models.
