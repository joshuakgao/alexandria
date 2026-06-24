---
title: "ScanNet: Richly-annotated 3D Reconstructions of Indoor Scenes"
authors: [Angela Dai, Angel X. Chang, Manolis Savva, Maciej Halber, Thomas Funkhouser, Matthias Nießner]
year: 2017
venue: "CVPR 2017"
tags: [3d-scene-understanding, synthetic-datasets]
url: "https://arxiv.org/abs/1702.04405"
pdf: "[[2017-scannet.pdf]]"
date_ingested: 2026-06-18
---

# ScanNet: Richly-annotated 3D Reconstructions of Indoor Scenes

![[2017-scannet-thumbnail.png]]

## Research gap
Deep learning for 3D scene understanding was bottlenecked by data scarcity. Existing RGB-D datasets covered limited viewpoints, had small-scale or expert-only annotations, lacked dense 3D reconstructions, or relied on synthetic data with a significant domain gap to real-world scans. No scalable framework existed for novice users to capture and crowdsource semantic annotations of real-world 3D scenes.

## Contributions
- **ScanNet dataset**: 2.5M RGB-D views across 1,513 scans of 707 unique indoor spaces, annotated with 3D camera poses, dense surface reconstructions, textured meshes, instance-level semantic segmentations (36K+ labeled objects), and aligned CAD models — an order of magnitude larger than prior RGB-D datasets.
- **Scalable acquisition framework**: An end-to-end pipeline enabling novice users to capture RGB-D data with commodity hardware (iPad + Structure sensor), with automated surface reconstruction (BundleFusion) and crowdsourced semantic annotation via Amazon Mechanical Turk (500+ workers).
- **3D benchmarks**: New benchmarks for 3D object classification, semantic voxel labeling, and CAD model retrieval, demonstrating that training on real-world ScanNet data significantly outperforms training on synthetic data for real-world test scenarios.
- **Volumetric CNN architecture**: A column-prediction network for semantic voxel labeling that achieves state-of-the-art performance on both ScanNet and NYU v2.

## Method
**Data capture:**
- Commodity RGB-D sensor (Structure sensor) mounted on iPad Air2. RGB at 1296×968, depth at 640×480, synchronized at 30 Hz. Simple calibration via checkerboard capture.
- iOS app with "featurefulness" bar guiding untrained users toward regions with good tracking features.

**Surface reconstruction:**
- BundleFusion for pose estimation, VoxelHashing for volumetric integration, Marching Cubes for mesh extraction (4mm³ voxels). Automatic floor-plane alignment using IMU gravity vectors and PCA.
- Automated quality filtering: discard short sequences, high-error reconstructions, or low frame-alignment rates.

**Semantic annotation (crowdsourced):**
- Instance-level labeling: WebGL interface where MTurk workers paint surface segments with freeform text labels (autocomplete over prior labels for consistency). Connectedness constraint prevents cheating. Each scan annotated by ~2.3 workers.
- CAD model alignment: Workers click labeled objects to search ShapeNet, then refine placement. 681 CAD instances across 52 scans.

**Benchmarks:**
- *3D object classification*: 17 categories, 9,677 train / 2,606 test instances. 3D CNN (Network-in-Network) with known/unknown occupancy channel and rotation augmentation.
- *Semantic voxel labeling*: 20 classes + free space. Column-prediction fully convolutional network on 2×31×31×62 subvolumes (~1.5m×1.5m×3m). Inverse-log-histogram class weighting.
- *CAD model retrieval*: Joint embedding of scan regions and ShapeNet models for cross-domain retrieval.

## Datasets & evaluation
- **Scale**: 1,513 scans, 2.5M frames, 707 unique spaces, 36,213 labeled object instances. Floor area: 34,453 m² total. 15× more scans than SceneNN (100), 24× more labeled objects.
- **Object classification**: Training on ScanNet achieves 74.9% on ScanNet test and 78.8% on SceneNN test — significantly outperforming training on ShapeNet synthetic data (39.5% / 68.2%). Mixing ScanNet + ShapeNet partial further improves to 76.6% / 81.2%.
- **Semantic voxel labeling**: 73.0% voxel accuracy on ScanNet test (312 scenes). Outperforms prior methods (Hermans et al., SemanticFusion) on NYU v2 when trained on ScanNet + NYU.
- **CAD retrieval**: Joint training on ShapeNet + ScanNet reaches 77.5% top-1 accuracy, vastly outperforming either alone (10.4% / 12.7%).

## Limitations
- Capture limited to indoor scenes with commodity RGB-D sensors; outdoor and large-scale environments are out of scope.
- Crowdsourced annotations have inherent noise — freeform text labels require synonym/misspelling normalization, and annotation quality varies across workers.
- CAD model alignment limited by ShapeNet coverage — exact instance-level matches are often unavailable, particularly for rare categories.
- Reconstruction quality depends on BundleFusion tracking; challenging scenes (textureless, reflective, or fast-moving) may fail.
- Dataset snapshot is from ~1 month of collection by 20 users; coverage of scene types is not uniform.

## Key takeaways
- **Real-world data matters for real-world tasks.** Training on synthetic data transfers poorly to real-world RGB-D; ScanNet closes this gap with large-scale real-world captures. The synthetic-to-real domain gap is a fundamental bottleneck, not just a minor inconvenience.
- **Scalable annotation is a systems problem.** The key innovation is not any single algorithm but the end-to-end pipeline — commodity hardware, automated reconstruction, and crowdsourced annotation — that enables non-experts to contribute high-quality 3D data at scale. The design decisions (painting metaphor, connectedness constraints, turntable animation) are critical for crowdsourcing quality.
- **ScanNet became the standard benchmark.** The dataset's combination of scale, real-world diversity, dense 3D annotations, and open availability made it the de facto standard for 3D scene understanding research, underpinning subsequent work on instance segmentation (Mask3D), open-vocabulary understanding (OpenMask3D, OpenScene), and spatial reasoning (ViewSuite).
