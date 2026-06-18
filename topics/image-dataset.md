---
topic: Image Datasets
slug: image-dataset
---

# Image Datasets

## Papers

| Year | Thumbnail                         | Paper               | Venue |
| ---- | --------------------------------- | ------------------- | ----- |
| 2026 | ![[2026-gpic-thumbnail.png\|400]] | [[2026-gpic\|GPIC]] |       |

## Overview

Image datasets are foundational to progress in computer vision and visual generative modeling. This topic covers large-scale image corpora, their construction pipelines (crawling, filtering, deduplication, captioning), licensing considerations, and their role as benchmarks. As models scale, so do their data requirements — moving from class-conditional datasets like ImageNet-1K to internet-scale, text-captioned corpora with hundreds of millions of images.

## Trends

- Shift from URL-index distribution to centrally hosted, frozen shards for reproducibility and stability.
- Increasing emphasis on permissive licensing (CC BY, CC0, Public Domain) to enable both academic and commercial use.
- Synthetic captioning via VLMs replacing noisy alt-text or metadata-based annotations.
- Multi-granularity captions (tags, short, medium, long) rather than single-format descriptions.

## Open questions

- How to efficiently deduplicate at billion-scale without removing visually distinct but related images?
- What is the optimal captioning granularity mix for training generative models?
- How to mitigate demographic and content biases inherited from source platforms (Flickr, Wikimedia)?
- Can frozen benchmarks remain relevant as model capabilities rapidly evolve, or will GPIC also saturate?
