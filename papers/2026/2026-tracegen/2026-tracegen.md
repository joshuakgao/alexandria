---
title: "TraceGen: World Modeling in 3D Trace Space Enables Learning from Cross-Embodiment Videos"
authors: [Seungjae Lee, Yoonkyo Jung, Inkook Chun, Yao-Chih Lee, Zikui Cai, Hongjia Huang, Aayush Talreja, Tan Dat Dao, Yongyuan Liang, Jia-Bin Huang, Furong Huang]
year: 2026
venue: "CVPR 2026"
tags: [world-models, embodied-ai, point-tracking]
url: "https://tracegen.github.io"
date_ingested: 2026-07-04
---

# TraceGen: World Modeling in 3D Trace Space Enables Learning from Cross-Embodiment Videos

![[2026-tracegen-thumbnail.png]]

## Research gap
Existing world models for robot manipulation operate in pixel space (video generation) or language token space (VLMs), both of which waste capacity on appearance details irrelevant to control and suffer from slow inference. Prior trace-based approaches are limited to 2D, trained only on static in-lab data, or require object detectors and heuristic filtering. No existing method provides a scalable, unified 3D motion representation that enables learning from heterogeneous cross-embodiment videos — humans and diverse robots — for few-shot transfer to new platforms and tasks.

## Contributions
- **TraceGen**: A world model that predicts future motion in compact 3D trace space (scene-level 3D keypoint trajectories) rather than pixels, abstracting away appearance and camera variation while retaining geometric structure needed for manipulation.
- **TraceForge**: A data-curation pipeline that converts heterogeneous human and robot videos into consistent 3D traces via camera-motion compensation, depth estimation, and speed retargeting.
- **TraceForge-123K corpus**: 123K videos yielding 1.8M observation–trace–language triplets from eight sources spanning human demos, single-arm robots, and bimanual robots — 15× larger than prior trace datasets.
- **Few-shot adaptation**: 80% success across four real-world tasks with only five in-domain robot videos; 67.5% success from five uncalibrated handheld human demo videos (human→robot transfer), with 50–600× faster inference than video-based world models.

## Method
**Trace representation.** A "trace" is a sequence of 3D trajectories over a 20×20 uniform keypoint grid tracked across 32 future timesteps, represented as screen-aligned (x, y, z) coordinates in a fixed reference camera frame. This captures scene-level motion (both robot and objects) while discarding appearance.

**TraceForge pipeline.** (1) Event chunking segments task-relevant spans and generates diverse language instructions via a VLM. (2) 3D point tracking uses TAPIP3D with CoTracker3 for tracking and VGGT for depth/pose estimation. (3) World-to-camera transformation aligns all traces to a reference camera frame, compensating for camera motion. (4) Speed retargeting normalizes trace duration across embodiments via cumulative arc-length reparameterization.

**TraceGen architecture.** Multi-encoder feature extraction uses frozen DINOv3 (geometric features), SigLIP (semantic features), and a SigLIP-based depth encoder with learnable stem adapter. Features are concatenated and projected into unified visual tokens, combined with T5-encoded text tokens as conditioning. A flow-based trace decoder adapted from CogVideoX's 3D transformer operates in trace space using the Stochastic Interpolant framework with a linear interpolation ODE, trained to predict velocity-like 3D keypoint increments. At inference, 100-step ODE integration generates trajectories, which are converted to robot joint commands via inverse kinematics.

**Warm-up.** The pretrained model is adapted to a specific robot via lightweight fine-tuning on a small set (5–15) of target demonstrations in trace space.

## Datasets & evaluation
**Training data:** TraceForge-123K — 123K episodes, 1.8M triplets from eight sources including Something-Something V2, EPIC-Kitchens, DROID, AgiBotWorld, and others. Approximately 20% of traces are 2D-only.

**Evaluation:** Four real-world manipulation tasks on a Franka Research 3 robot — folding clothes, inserting a ball into a box, sweeping with a brush, placing a block.

**Key results:**
- Robot→Robot (5-video warm-up): 80% overall success (Clothes 10/10, Ball 6/10, Brush 8/10, Block 8/10).
- Robot→Robot (15-video warm-up): 82.5% overall success.
- Human→Robot (5 handheld phone videos, no robot data): 67.5% success.
- From-scratch baselines achieve only 0–25% under the same conditions.
- 3.8× faster than trace-based baselines; 50–600× faster than video-generation world models.
- Cross-embodiment pretraining (70% on Ball+Block) substantially outperforms single-source pretraining on SSV2-only (25%) or AgiBotWorld-only (45%).

## Limitations
- Evaluation limited to four tabletop manipulation tasks on a single robot platform (Franka Research 3).
- Uses a basic tracking controller for trace execution; more sophisticated policies are left for future work.
- 3D trace accuracy depends on the quality of depth and camera pose estimation, which can degrade on challenging in-the-wild footage.
- The 20×20 keypoint grid and fixed trace length may not capture fine-grained or long-horizon manipulation.
- Human→robot transfer still shows a performance gap compared to robot→robot warm-up (67.5% vs. 80%).

## Key takeaways
- Operating in 3D trace space rather than pixel space provides an effective inductive bias for cross-embodiment world modeling, offering both computational efficiency (50–600× speedup) and sample efficiency (5-shot adaptation).
- A unified, embodiment-agnostic 3D motion representation makes it possible to pool supervision from humans and diverse robots, yielding a transferable motion prior that single-source pretraining cannot match.
- The combination of camera-motion compensation, speed retargeting, and scene-level (not object-centric) trace prediction eliminates the need for object detectors or heuristic filtering, enabling scalable data curation from in-the-wild videos.
