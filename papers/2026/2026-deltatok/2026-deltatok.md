---
title: "A Frame is Worth One Token: Efficient Generative World Modeling with Delta Tokens"
authors: [Tommie Kerssies, Gabriele Berton, Ju He, Qihang Yu, Wufei Ma, Daan de Geus, Gijs Dubbelman, Liang-Chieh Chen]
year: 2026
venue: "CVPR 2026"
tags: [world-models]
url: ""
pdf: "[[2026-deltatok.pdf]]"
date_ingested: 2026-06-18
---

# A Frame is Worth One Token: Efficient Generative World Modeling with Delta Tokens

![[2026-deltatok-thumbnail.png]]

## Research gap

DeltaTok introduces a radical compression for video world modeling: instead of representing each frame as a dense spatial feature map, it encodes only the *change* between consecutive frames as a single continuous "delta" token. This collapses video from a 3D spatiotemporal representation to a 1D temporal sequence — a 1,024× token reduction at 512×512 resolution — enabling DeltaWorld, a generative world model that produces multiple diverse plausible futures in a single forward pass.

## Contributions

- DeltaTok: a video tokenizer that compresses inter-frame VFM feature differences into a single continuous token per frame (1,024× token reduction at 512×512)
- DeltaWorld: a generative world model operating on delta token sequences that generates multiple diverse futures in a single forward pass via Best-of-Many training
- 35× fewer parameters and 2,000× fewer FLOPs than Cosmos while achieving better forecasting accuracy
- Demonstration that videos can be effectively represented using only the temporal dimension (1D delta token sequences), enabling tractable multi-hypothesis generation

## Method

**DeltaTok Tokenizer:** A continuous autoencoder where the encoder g takes the previous frame's VFM features x_{t-1} and current features x_t, compressing their difference into a single delta token z_t ∈ R^D via transformer blocks with cross-attention. The decoder h reconstructs x_t from z_t conditioned on x_{t-1}. First frame is encoded as absolute features (conditioned on a black frame). Trained with L2 reconstruction loss, frozen during world model training.

**Best-of-Many (BoM) Training:** K noise queries q^k ~ N(μ, Σ) replace the single learned query in the predictor. Each produces a different future hypothesis. Only the closest to ground truth is supervised (winner-take-all). At inference, different noise inputs map to different futures in one forward pass — no iterative denoising needed.

**DeltaWorld Predictor:** Transformer operating on sequences of delta tokens Z_{1:t}. Predicts next delta token ẑ_{t+1} conditioned on context and a noise query. Cross-attention from query to context tokens with causal masking enables parallel training across all timesteps.

**Autoregressive Rollout:** For mid-term prediction, predicted delta tokens are decoded to spatial features, re-encoded as delta tokens, and appended to context. K futures are rolled out independently.

## Datasets & evaluation

- **Cityscapes mid-term segmentation:** DeltaWorld-0.3B achieves ~65 mIoU (best) vs. Cosmos-4B ~55, Cosmos-12B ~58, with 2,000× fewer FLOPs
- **VSPW/KITTI:** Consistently surpasses generative baselines in best-sample metrics; competitive with discriminative baselines in mean metrics
- **Ablation:** Delta compression outperforms full-frame compression (frame tokens) — predicting change is easier than predicting full state; BoM with K=256 substantially improves diversity without degrading mean quality
- **Efficiency:** 31 TFLOPs vs. 60,000-64,000 TFLOPs for Cosmos variants; enables practical multi-hypothesis deployment

## Limitations

- Single delta token may be insufficient for frames with large, complex changes (scene cuts, fast camera motion); the paper acknowledges fallback to absolute encoding for low-redundancy cases
- Evaluated only on dense forecasting (segmentation, depth); applicability to planning, reinforcement learning, or long-horizon prediction is untested
- Autoregressive rollout compounds errors; longer rollouts (beyond ~0.6s) not evaluated
- The BoM objective assumes one "best" future per training example; complex multi-modal distributions may need more structured diversity mechanisms
- No action conditioning — DeltaWorld is observation-only; extending to action-conditioned world models for robotics is future work

## Key takeaways

The key insight is that consecutive video frames differ in structured, low-dimensional ways (backgrounds are static, only small portions change), so representing each frame as a full spatial feature map is massively redundant. Operating in the feature space of a vision foundation model (DINOv2) rather than pixel space further removes irrelevant detail. The DeltaTok encoder takes the previous frame's VFM features and the current frame's features, compresses their difference into a single token via a transformer autoencoder. A decoder reconstructs the full spatial feature map by applying the delta token to the previous frame's features.

DeltaWorld combines DeltaTok with a Best-of-Many (BoM) training objective: K noise queries produce K future hypotheses in parallel during training, and only the best is supervised. At inference, different noise inputs yield different plausible futures in a single forward pass — avoiding iterative diffusion denoising. The result is a generative world model with 35× fewer parameters and 2,000× fewer FLOPs than Cosmos, while producing futures that better align with real-world outcomes on dense forecasting benchmarks (semantic segmentation on VSPW/Cityscapes, depth estimation on KITTI).

DeltaTok builds on DINO-world's insight that predicting in VFM feature space (rather than pixels) enables compact world models, but makes it generative via BoM training. The delta compression exploits the same temporal redundancy principle as classic video codecs (H.264 inter-frame prediction) and optical flow, but operates in semantic feature space rather than pixel space, and is non-spatial (one token, not per-pixel vectors). Compared to SceneTok (which compresses multi-view scenes into unstructured tokens for 3D generation), DeltaTok compresses temporal dynamics into delta tokens for video prediction — complementary approaches to the same goal of extreme compression for generative modeling. The BoM objective avoids the cost of diffusion denoising (used by SceneTok's decoder) by enabling single-pass multi-hypothesis generation.
