---
title: "SpatialLM: Training Large Language Models for Structured Indoor Modeling"
authors: [Yongsen Mao, Junhao Zhong, Chuan Fang, Jia Zheng, Rui Tang, Hao Zhu, Ping Tan, Zihan Zhou]
year: 2025
venue: "NeurIPS 2025"
tags: [3d-scene-understanding, vision-language-models, floorplan-reconstruction, spatial-reasoning]
url: ""
pdf: "[[2025-spatiallm-structured-indoor-modeling.pdf]]"
date_ingested: 2026-06-18
---

# SpatialLM: Training Large Language Models for Structured Indoor Modeling

![[2025-spatiallm-structured-indoor-modeling-thumbnail.png]]

## Research gap

SpatialLM is a large language model trained to process 3D point cloud inputs and output structured text descriptions of indoor scenes, covering both architectural layout (walls, doors, windows) and oriented 3D object bounding boxes. Rather than treating scene understanding as a regression or detection problem with task-specific heads, the model frames it as a language generation task — a novel design choice that makes the model output interpretable, editable, and directly usable in downstream applications (AR, robotics, layout tools).

## Contributions

- **Encoder-MLP-LLM architecture** for point cloud → structured text: the first empirical study demonstrating LLMs can effectively process raw 3D point cloud data for structured indoor scene prediction
- **SpatialLM dataset**: 12,328 high-quality synthetic indoor scenes (54,778 rooms) with full human-authored 3D layout and object annotations, sourced from a professional interior design platform
- **Structured text representation** for both layout (plane segments) and objects (6-DoF bounding boxes), encoded as attribute lists parseable as code — enabling LLM compatibility without task-specific heads
- **3-stage training schedule** with empirical ablation showing single-stage fine-tuning on the target dataset outperforms all multi-stage variants
- **Zero-shot video generalisation**: SpatialLM applied to MASKOR-SLAM-reconstructed point clouds from monocular RGB video, with no fine-tuning

## Method


## Datasets & evaluation

**Layout estimation (Structured3D):**

| Method | F1@IoU0.25 | F1@IoU0.5 |
|--------|-----------|-----------|
| RoomFormer | 83.4 | 81.4 |
| SceneScript | 84.6 | 89.2 |
| **SpatialLM** | **84.9** | **93.5** |

**3D Object detection (ScanNet):**

| Method | F1@IoU0.25 | F1@IoU0.5 |
|--------|-----------|-----------|
| V-DETR | — | 4.3 |
| SceneScript | — | 2.4 |
| **SpatialLM** | **65.6** | **56.8** |

SpatialLM trained on its own dataset then fine-tuned on ScanNet substantially outperforms both V-DETR and SceneScript — the latter two struggle because ScanNet is too small to train LLMs from scratch.

![[raw/3d-scene-understanding/2025/assets/spatiallm-fig4-qualitative-results.png]]
*Figure 4: Qualitative comparisons on Structured3D — SpatialLM preserves spatial relationships between elements better than RoomFormer and SceneScript*

## Limitations

- Currently limited to indoor scenes; architectural elements specific to indoor spaces
- Does not handle open-vocabulary object categories beyond the 59 training classes
- LiDAR and structured-light scans are not evaluated — only RGBD-derived point clouds
- Zero-shot video generalisation degrades significantly relative to clean RGBD scans (noisier geometry, occlusions, scale ambiguity from monocular depth)
- Scaling beyond 0.5B parameters not explored; larger LLMs may improve spatial reasoning further

## Key takeaways

The key insight is that LLMs' strong language generation priors can be adapted for spatial structured prediction without requiring specialised decoders, as long as 3D features are projected into the token space in a spatially meaningful way. SpatialLM achieves SOTA on both layout estimation (Structured3D) and 3D object detection (ScanNet), and generalises zero-shot to video-reconstructed point clouds.

- **RoomFormer** (Yue et al., 2023): Auto-regressive Transformer predicting room polygons and corners; task-specific architecture; SpatialLM surpasses it by +12.1 F1@0.5 on layout
- **SceneScript** (Avetisyan et al., 2024): Closest conceptual predecessor — also uses LLMs for structured scene output; SpatialLM differs by processing raw point clouds rather than requiring a specialised scene encoder; outperforms it on both tasks
- **V-DETR** (Shen et al.): Strong 3D object detector; underperforms on ScanNet when SpatialLM is pre-trained on the larger SpatialLM dataset — highlights data efficiency of the LLM approach
- **Scene-LLM, EmbodiedGPT, LLaDA**: Related work grounding LLMs in 3D for QA and navigation; SpatialLM is specifically focused on structured reconstruction rather than dialogue
