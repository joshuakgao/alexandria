---
title: Spatial Temporal Graph Convolutional Networks for Skeleton-Based Action Recognition
authors:
  - Sijie Yan
  - Yuanjun Xiong
  - Dahua Lin
year: 2018
venue: AAAI 2018
tags:
  - video-understanding
  - sports-analytics
url: https://arxiv.org/abs/1801.07455
date_ingested: 2026-08-01
---

# Spatial Temporal Graph Convolutional Networks for Skeleton-Based Action Recognition

![[2018-spatial-temporal-graph-convolutional-networks-thumbnail.png]]

## Research gap
Prior methods for skeleton-based action recognition relied on hand-crafted part assignments or traversal rules to model spatial relationships among body joints, resulting in limited expressive power and poor generalization across different skeleton configurations. Deep learning approaches (RNNs, temporal CNNs) operated on concatenated joint coordinates without exploiting the natural graph structure of the skeleton, while graph neural networks had not yet been applied to dynamic skeleton sequences.

## Contributions
- ST-GCN, the first application of graph convolutional networks to skeleton-based action recognition, which automatically learns spatial and temporal patterns from the natural graph structure of body joints.
- A formulation of spatial-temporal graph convolution that extends spatial graph convolution to the temporal domain by connecting the same joints across consecutive frames, with principled partitioning strategies for defining convolution kernels on irregular graph neighborhoods.
- Three partitioning strategies (uni-labeling, distance partitioning, spatial configuration partitioning) for graph convolution kernels, with spatial configuration partitioning capturing concentric/eccentric motion patterns.
- Learnable edge importance weighting that allows different layers to learn joint-specific importance, reflecting that the same joint has different relevance in different body parts.

## Method
A skeleton sequence is represented as a spatial-temporal graph G = (V, E) where nodes are joints across all frames, spatial edges follow natural body connectivity within each frame, and temporal edges connect the same joint across consecutive frames. The input features are joint coordinates (2D or 3D) with confidence scores.

Graph convolution is defined by: (1) a sampling function that selects the 1-neighborhood of each joint, and (2) a weight function implemented via a labeling map that partitions the neighborhood into K subsets, each sharing a weight vector. The spatial configuration partitioning strategy divides neighbors into three groups — the root node, centripetal nodes (closer to skeleton gravity center), and centrifugal nodes — inspired by concentric/eccentric motion patterns.

Spatial-temporal convolution extends the neighborhood to include temporally adjacent frames within a temporal kernel size Γ. Implementation uses normalized adjacency matrices multiplied with feature tensors, with 1×Γ standard 2D convolutions for temporal modeling.

The network has 9 ST-GCN layers (64→128→256 channels), batch normalization, residual connections, dropout (0.5), temporal stride-2 pooling at layers 4 and 7, followed by global average pooling and softmax classification. Data augmentation includes random affine transformations simulating camera movement.

## Datasets & evaluation
- **Kinetics** (300K video clips, 400 action classes): Skeletons extracted via OpenPose (18 joints, 2D). ST-GCN achieves 30.7% top-1 / 52.8% top-5 accuracy, substantially outperforming Deep LSTM (16.4%), Temporal ConvNet (20.3%), and feature encoding (14.9%). On the 30-class "Kinetics Motion" subset (body-motion-focused actions), ST-GCN (72.4%) matches optical flow CNN (72.8%).
- **NTU-RGB+D** (56K clips, 60 classes, 3D Kinect joints): ST-GCN achieves 81.5% (cross-subject) and 88.3% (cross-view), outperforming prior SOTA C-CNN+MTLN (79.6% / 84.8%) without data augmentation.
- Multi-modal ensemble (RGB + optical flow + ST-GCN) on Kinetics achieves 77.1% top-1, demonstrating skeleton features are complementary to appearance and motion modalities.

## Limitations
- Only uses D=1 (immediate neighbors); higher-order neighborhoods are not explored.
- Skeleton-based accuracy on full Kinetics (30.7%) is substantially below RGB-based methods (57.0%), as many action classes depend on appearance cues (e.g., playing instruments) rather than body motion alone.
- Relies on external pose estimation (OpenPose for Kinetics, Kinect for NTU-RGB+D); errors in pose estimation propagate to recognition.
- Fixed graph topology assumes a single skeleton structure; does not handle varying joint counts or missing joints gracefully.
- Partitioning strategies are manually designed; learning the partition end-to-end is left as future work.

## Key takeaways
- The natural graph structure of body skeletons provides a powerful inductive bias for action recognition — graph convolution with weight sharing on sparse skeleton connections outperforms both fully-connected temporal convolution and local convolution with unshared weights.
- Spatial configuration partitioning (centripetal vs. centrifugal grouping relative to the skeleton's gravity center) captures biomechanically meaningful motion patterns and consistently outperforms simpler partitioning strategies.
- Learnable edge importance weighting enables layers to specialize their attention to different body parts, providing an additional +1% improvement on top of strong graph convolution baselines.
- Skeleton-based features are highly complementary to RGB and optical flow — ensemble fusion yields consistent gains, suggesting that graph-based skeleton modeling captures motion dynamics that appearance-based methods miss.
- ST-GCN generalizes across vastly different input types (2D estimated poses from video vs. 3D Kinect tracking) and capture conditions (unconstrained YouTube vs. controlled lab), demonstrating the robustness of the graph-based formulation.
