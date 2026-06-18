---
topic: Image Captioning
slug: image-captioning
---

# Image Captioning

## Papers

| Year | Thumbnail                         | Paper               | Venue |
| ---- | --------------------------------- | ------------------- | ----- |
| 2026 | ![[2026-gpic-thumbnail.png\|400]] | [[2026-gpic\|GPIC]] |       |

## Overview

Image captioning generates natural-language descriptions of images. This topic covers both captioning as a standalone task and its role as a data-preparation step for training generative models. Modern approaches use vision-language models (VLMs) to produce synthetic captions at scale, replacing noisy alt-text or metadata. Caption granularity (tags, short, medium, long descriptions) and quality-throughput tradeoffs are active considerations.

## Trends

- Synthetic captioning via open-source VLMs (e.g., Qwen3-VL) replacing scraped alt-text for large-scale dataset construction.
- Multi-granularity caption formats to support diverse downstream conditioning needs.
- LLM-as-a-judge pipelines for evaluating caption quality across multiple axes (spatial understanding, attribute binding, counting, OCR).
- Quality-throughput tradeoff analysis driving selection of smaller, efficient models for corpus-scale captioning.

## Open questions

- How to reduce hallucination in VLM-generated captions at scale?
- What is the optimal caption granularity distribution for training text-to-image models?
- How to evaluate caption quality without expensive human annotation at scale?
- Can caption diversity (style, detail level) improve downstream model robustness?
