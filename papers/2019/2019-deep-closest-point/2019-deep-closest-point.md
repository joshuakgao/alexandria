---
title: "Deep Closest Point: Learning Representations for Point Cloud Registration"
authors: [Yue Wang, Justin Solomon]
year: 2019
venue: "ICCV 2019"
tags: [point-cloud-registration]
url: ""
pdf: "[[2019-deep-closest-point.pdf]]"
date_ingested: 2026-06-18
---

# Deep Closest Point: Learning Representations for Point Cloud Registration

![[2019-deep-closest-point-thumbnail.png]]

## Research gap

Deep Closest Point (DCP) addresses the fundamental problem of point cloud registration by reimagining the classical Iterative Closest Point (ICP) algorithm through the lens of deep learning. Rather than the hand-crafted iterative optimization approach of ICP, DCP proposes an end-to-end learnable pipeline that avoids converging to spurious local optima. The method combines point cloud embeddings, attention-based soft matching (inspired by pointer networks), and differentiable SVD to predict rigid transformations. Critically, DCP succeeds where traditional methods fail: it handles objects with sharp features and large transformations. The learned features generalize well to unseen objects, suggesting that the model captures salient geometric properties beyond simple distance-based matching.

## Contributions

- **End-to-end learnable registration**: Replaces ICP's iterative local optimization with a single forward pass through a trainable network
- **Soft matching with attention**: Uses pointer networks combined with attention to predict probabilistic correspondences rather than hard nearest-neighbor matching
- **Differentiable SVD**: Incorporates differentiable singular value decomposition to extract optimal rigid transformations in a learnable way
- **Robustness to large transformations**: Outperforms ICP, Go-ICP, FGR, and PointNetLK on cases with sharp features and large rigid motions
- **Feature generalization**: Demonstrates that learned representations transfer to unseen object categories (ModelNet40 generalization study)

## Method

DCP consists of three main components:

1. **Embedding network**: Maps input point clouds to permutation and rigid-invariant embeddings using PointNet or DGCNN, capturing local geometric context
2. **Soft matching module**: Combines attention mechanisms with pointer networks to predict a soft correspondence matrix between source and target point clouds (probabilistic instead of hard matching)
3. **Differentiable SVD**: Given the soft matching, extracts the optimal rigid transformation via differentiable singular value decomposition

Training is end-to-end on ModelNet40 with a registration loss (alignment error). The soft matching approach is key—it allows gradients to flow through the correspondence prediction unlike hard nearest-neighbor assignments.

## Datasets & evaluation

- **ModelNet40 clean**: Outperforms all baselines (ICP, Go-ICP, FGR, PointNetLK)
- **With noise and partial overlaps**: Maintains robustness to realistic perturbations
- **Large transformation scenarios**: Uniquely succeeds in aligning objects with sharp features and rotations > 45°
- **Generalization**: Learned features transfer well to unseen object categories, suggesting domain-general feature learning

## Limitations

- Scalability to very large point clouds (ModelNet40 objects are relatively small)
- Performance on real-world noisy and partial scans beyond synthetic datasets
- Extension to non-rigid registration or dynamic objects
- Computational cost comparison with ICP on practical deployment scenarios
- Analysis of failure modes: which geometric configurations does DCP struggle with?

## Key takeaways

Foundational work in learning-based point cloud registration. Builds on PointNet for embedding learning and incorporates attention/pointer networks from NLP. Compared to PointNetLK, DCP adds explicit attention-based matching and improves robustness. Precedes later work like GeoTransformer (2023) which adds geometric invariants and enables RANSAC-free matching at scale.
