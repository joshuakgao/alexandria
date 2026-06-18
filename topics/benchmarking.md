---
topic: Benchmarking
slug: benchmarking
---

# Benchmarking

## Papers

| Year | Thumbnail                         | Paper               | Venue |
| ---- | --------------------------------- | ------------------- | ----- |
| 2026 | ![[2026-gpic-thumbnail.png\|400]] | [[2026-gpic\|GPIC]] |       |

## Overview

Benchmarking in AI research involves designing evaluation protocols, metrics, and standardized datasets that enable fair comparison across methods. This topic covers metric design (FID, FD-DINOv2, precision, recall, density, coverage), evaluation pitfalls (Goodharting, metric saturation), and the properties that make a benchmark useful: reproducibility, stability, scale, and accessibility.

## Trends

- Recognition that long-running benchmarks saturate and cease to be informative (e.g., ImageNet-1K FID).
- Move toward held-out test sets for computing reference statistics, preventing memorization artifacts.
- DINOv2 features replacing Inception-v3 features for computing distribution distances.
- Compliance guidelines discouraging metric-aligned training objectives to preserve benchmark integrity.

## Open questions

- How to design metrics that resist Goodharting while remaining correlated with human perception?
- When should a benchmark be retired or replaced, and how to manage community transitions?
- How to evaluate generative models across diverse dimensions (quality, diversity, safety, fairness) in a single protocol?
