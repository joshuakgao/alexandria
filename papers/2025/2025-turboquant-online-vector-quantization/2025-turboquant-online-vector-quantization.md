---
title: "TurboQuant: Online Vector Quantization with Near-optimal Distortion Rate"
authors: [Amir Zandieh, Majid Daliri]
year: 2025
venue: "ICLR 2026"
tags: [quantization]
url: ""
pdf: "[[2025-turboquant-online-vector-quantization.pdf]]"
date_ingested: 2026-06-18
---

# TurboQuant: Online Vector Quantization with Near-optimal Distortion Rate

![[2025-turboquant-online-vector-quantization-thumbnail.png]]

## Research gap

TurboQuant proposes a theoretically-grounded vector quantization method achieving near-optimal distortion rates for both mean-squared error (MSE) and inner product preservation. The method is designed for online applications (e.g., KV cache quantization in LLMs) and accelerator-friendly implementation. TurboQuant uses random rotation to induce concentrated Beta distributions on coordinates, applies per-coordinate Lloyd-Max quantization for MSE optimality, then composes with a Quantized Johnson-Lindenstrauss (QJL) transform to achieve unbiased inner product estimation. The approach proves information-theoretic lower bounds and demonstrates practical performance: 3.5 bits per channel with no quality loss and 2.5 bits with marginal degradation for KV cache compression.

## Contributions

- Two-stage vector quantizer combining MSE optimization with unbiased inner product preservation
- Data-oblivious, online algorithm suitable for streaming applications
- Formal information-theoretic bounds: achieves near-optimal distortion within ~2.7× factor
- Practical KV cache quantization: 3.5 bits/channel with full quality, 2.5 bits with minimal degradation
- Superior performance on nearest neighbor search benchmarks with near-zero indexing overhead
- Accelerator-friendly design enabling real-time deployment in LLM inference

## Method

**Random Rotation & Beta Distribution**: Input vectors are randomly rotated to induce independent, concentrated Beta distributions on each coordinate. This enables decomposition into per-coordinate quantization problems.

**MSE-Optimal Stage**: For each rotated coordinate, apply optimal Lloyd-Max quantizer (solving continuous k-means) to minimize L2 norm and achieve MSE distortion bounds.

**Inner Product Correction**: MSE-optimal quantizers introduce bias in inner product estimates. Apply 1-bit Quantized Johnson-Lindenstrauss (QJL) transform to residuals to achieve unbiased, low-distortion inner product quantization.

**Complexity & Speed**: Per-coordinate quantization enables trivial parallelization. Random rotation and QJL transform have minimal computational overhead, making the method suitable for online KV cache quantization during inference.

## Datasets & evaluation

- **KV Cache Quantization**: Absolute quality neutrality (0% degradation) at 3.5 bits per channel; 2.5 bits achieves <1% degradation
- **Needle-In-A-Haystack Test**: Achieves 0.997 score (matching full-precision) despite 4× quantization; other methods struggle with recall at these depths
- **Nearest Neighbor Search**: Outperforms product quantization techniques in recall while reducing indexing time to near-zero
- **Theoretical**: Proven within ~2.7× factor of information-theoretic lower bounds, matching Shannon's distortion-rate limits

## Limitations

- Extension to learned/adaptive quantization for domain-specific applications
- Multi-stage refinement: can further stages improve distortion beyond two-stage approach?
- Application to sparse vectors and structured quantization
- Comparison with recent neural quantization methods and learned codebooks
- Extension to other similarity metrics (cosine, L1, etc.)

## Key takeaways

Advances beyond product quantization (PQ) and other VQ methods by providing formal optimality guarantees and addressing both MSE and inner product distortion simultaneously. Previous methods either optimized MSE (introducing inner product bias) or lacked optimal distortion bounds. Practical improvements over prior KV cache quantization methods (SnapKV, PyramidKV, KIVI, PolarQuant) demonstrated in evaluations.
