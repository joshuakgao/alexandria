---
title: "RTMPose: Real-Time Multi-Person Pose Estimation based on MMPose"
authors: [Tao Jiang, Peng Lu, Li Zhang, Ningsheng Ma, Rui Han, Chengqi Lyu, Yining Li, Kai Chen]
year: 2023
tags: [pose-estimation]
url: "https://arxiv.org/abs/2303.07399"
date_ingested: 2026-08-02
---

# RTMPose: Real-Time Multi-Person Pose Estimation based on MMPose

![[2023-rtmpose-thumbnail.png]]

## Research gap
Despite strong results on academic benchmarks, existing 2D pose estimation methods suffer from heavy parameters and high latency that prevent real-time deployment in industrial applications. Efficient architectures and detection-free paradigms have narrowed but not closed this gap, particularly on resource-constrained devices like mobile phones and CPUs.

## Contributions
- A systematic empirical study of key factors in pose estimation: paradigm choice, backbone architecture, localization method, training strategy, and deployment optimization.
- RTMPose, a high-performance real-time multi-person pose estimation framework built on MMPose, offering models at five scales (t/s/m/l/x) for different speed–accuracy trade-offs.
- Demonstration that top-down pipelines with real-time detectors are competitive in speed with bottom-up methods for typical scene densities (≤6 persons).
- Comprehensive deployment benchmarks across CPU (Intel i7-11700), GPU (GTX 1660 Ti), and mobile (Snapdragon 865) platforms using multiple inference backends.

## Method
RTMPose adopts a top-down paradigm, using an off-the-shelf real-time detector (RTMDet) to obtain person bounding boxes. Each cropped person image is processed by:

1. **Backbone**: CSPNeXt, originally designed for object detection, which balances speed, accuracy, and deployment friendliness over classification-oriented backbones.
2. **Head**: A SimCC-based localization head that treats keypoint prediction as two independent 1D classification tasks (x-axis and y-axis coordinates) using sub-pixel bins. This replaces costly heatmap decoding with two simple fully-connected layers.
3. **Gated Attention Unit (GAU)**: A self-attention module inserted between the backbone and the classification head to capture inter-keypoint dependencies with minimal overhead.

Training optimizations include Gaussian label smoothing for SimCC, large-scale data augmentation, and strategies borrowed from RTMDet (e.g., exponential moving average, flat-cosine annealing schedule). The inference pipeline adds skip-frame detection, pose NMS, and temporal smoothing for robustness in video applications.

## Datasets & evaluation
- **COCO val2017**: RTMPose-m achieves 75.8% AP with 90+ FPS on CPU and 430+ FPS on GPU; RTMPose-s achieves 72.2% AP at 70+ FPS on Snapdragon 865.
- **COCO-WholeBody**: RTMPose-x achieves 65.3% AP for whole-body (body + hands + face + feet) estimation.
- **CrowdPose**: Evaluated to demonstrate robustness in crowded scenes.
- **MPII**, **AP-10K** (animal pose), **AIC**: Additional benchmarks showing generalization across datasets and species.
- Extensive latency profiling across PyTorch, ONNX Runtime, TensorRT, and ncnn backends.

## Limitations
- Top-down paradigm scales linearly with person count; performance degrades in extreme crowd scenarios (>6 persons per image).
- Relies on a separate person detector, so pose accuracy is bounded by detection quality.
- Evaluated primarily on standard benchmarks with relatively clean data; robustness to heavy occlusion and unusual poses is not deeply explored.
- The architectural innovations (CSPNeXt + SimCC + GAU) are largely engineering combinations of existing techniques rather than fundamentally new methods.

## Key takeaways
- Top-down pose estimation with modern real-time detectors is no longer the bottleneck it was assumed to be — lightweight pose networks can run multiple forward passes per frame in real time.
- SimCC-based coordinate classification is a strong alternative to heatmap regression, offering competitive accuracy with simpler architecture and easier deployment.
- Training strategy (augmentation, scheduling, label smoothing) contributes as much to performance as architectural choices, making these optimizations transferable to other pose models.
- RTMPose establishes a practical Pareto frontier for real-time pose estimation, making it a strong baseline for industrial deployment.
