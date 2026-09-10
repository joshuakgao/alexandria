---
title: "SAM 3D Body: Robust Full-Body Human Mesh Recovery"
authors: [Xitong Yang, Devansh Kukreja, Don Pinkus, Anushka Sagar, Taosha Fan, Jinhyung Park, Soyong Shin, Jinkun Cao, Jiawei Liu, Nicolas Ugrinovic, Matt Feiszli, Jitendra Malik, Piotr Dollar, Kris Kitani]
year: 2026
venue: "CVPR 2026"
tags: [pose-estimation]
url: "https://arxiv.org/abs/2602.15989"
date_ingested: 2026-07-29
---

# SAM 3D Body: Robust Full-Body Human Mesh Recovery

![[2026-sam-3d-body-thumbnail.png]]

## Research gap
Existing human mesh recovery (HMR) methods exhibit poor robustness on in-the-wild images, failing on challenging poses, severe occlusion, uncommon viewpoints, and unified body-hand estimation. Training data suffers from either low diversity (lab capture) or low annotation quality (pseudo-labeling from monocular fitting). No prior single model achieves state-of-the-art on both full-body and hand pose estimation while supporting interactive user guidance.

## Contributions
- A promptable encoder-decoder architecture for full-body 3D HMR that accepts optional 2D keypoints and masks for user-guided inference, following the SAM paradigm.
- First HMR model built on Momentum Human Rig (MHR), a new parametric mesh representation that decouples skeletal structure from surface shape, replacing SMPL.
- A two-decoder design (body + hand) that resolves optimization conflicts between body and hand pose estimation while enabling unified full-body output.
- A VLM-driven data engine that mines challenging in-the-wild images for annotation, ensuring coverage of rare poses, viewpoints, and appearances across 7M images.
- A multi-stage annotation pipeline combining manual keypoint annotation, dense keypoint detection, differentiable optimization, and multi-view geometry for high-quality pseudo ground truth.
- A new categorical evaluation dataset (SA1B-Hard, 24 categories) enabling nuanced analysis of HMR model behavior.

## Method
3DB uses a shared vision backbone (ViT-H or DINOv3) to encode a human-cropped image into dense features. Two separate decoders attend to these features via cross-attention:

1. **Body decoder**: Takes concatenated query tokens (MHR+camera, optional 2D keypoint prompts, auxiliary 2D/3D keypoint tokens, hand position tokens) and produces full-body MHR parameters (pose, shape, camera, skeleton) via MLP regression.

2. **Hand decoder**: Optionally processes hand crops with dedicated features, producing hand-specific MHR parameters. At inference, hand decoder outputs are merged into the body prediction, with a re-prompting strategy using predicted wrist/elbow locations to refine the full-body result.

Training uses multi-task losses (2D/3D keypoint L1 with learnable per-joint uncertainty, MHR parameter L2, hand detection GIoU/L1) with warm-up scheduling. Interactive training simulates prompt availability by randomly sampling prompts across multiple rounds per sample.

The data engine uses a VLM to mine challenging images from large repositories (SA-1B, stock photos), iteratively updated based on failure analysis of the current model. Annotation combines manual sparse keypoints, a 595-point dense keypoint detector, single-image differentiable MHR fitting, and multi-view fitting with triangulation and temporal smoothness.

## Datasets & evaluation
- **Training**: 7M images from 13 datasets — single-view in-the-wild (COCO, MPII, AIChallenger, 3DPW, SA-1B), multi-view (Ego-Exo4D, Harmony4D, EgoHumans, InterHand, Re:Interhand, DexYCB, Goliath), and synthetic data.
- **Standard benchmarks** (Table 2): 3DB outperforms all single-image methods and competes with video-based approaches. On 3DPW: 33.2 PA-MPJPE (vs. 33.6 NLF), on EMDB: 38.2 PA-MPJPE (vs. 40.9 CameraHMR), on RICH: 30.9 PA-MPJPE (vs. 34.0 CameraHMR).
- **New benchmarks** (Table 3): On five new datasets (Ego-Exo4D, Harmony4D, Goliath, Synthetic, SA1B-Hard), 3DB shows strong generalization even in leave-one-out training, while baselines exhibit dataset-specific biases.
- **Hand estimation** (Table 4): On FreiHand (not in training), 3DB achieves 5.5 PA-MPJPE — comparable to SOTA hand-only methods that train on FreiHand.
- **User study**: 7,800 participants, 5:1 win rate over prior methods in visual quality preference.
- **Categorical analysis**: 24-category evaluation on SA1B-Hard shows 3DB outperforms all baselines on every category, with largest gains on truncation, inverted poses, and extreme limb positions.

## Limitations
- Relies on an external field-of-view estimator (MoGe-2) for camera intrinsics at inference, inheriting its errors.
- Hand decoder re-prompting can introduce elbow artifacts that require an additional refinement pass.
- Performance gap remains compared to hand-specialized methods that train on in-domain hand datasets.
- The MHR representation, while more interpretable than SMPL, is new and less widely adopted, limiting direct comparability with existing SMPL-based methods.
- Data engine requires semi-manual failure analysis to update VLM mining rules.

## Key takeaways
- Decoupling skeletal structure from body shape (MHR vs. SMPL) improves interpretability and controllability for full-body mesh recovery.
- The SAM-style promptable paradigm transfers effectively to HMR — optional 2D keypoints and masks enable interactive guidance for ambiguous poses and provide a natural mechanism for body-hand alignment.
- Separate body and hand decoders with a shared encoder resolve fundamental optimization conflicts in full-body estimation, achieving competitive performance with both body-specialized and hand-specialized models.
- A VLM-driven data engine that iteratively mines hard cases is more effective than random sampling for building diverse, robust HMR training sets — the diversity of training data matters as much as its scale.
- 3DB is the first single model to achieve SOTA on full-body benchmarks while being comparable to hand-specialized models, with strong out-of-domain generalization.
