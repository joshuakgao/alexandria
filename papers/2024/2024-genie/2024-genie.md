---
title: "Genie: Generative Interactive Environments"
authors: [Jake Bruce, Michael Dennis, Ashley Edwards, Jack Parker-Holder, Yuge Shi, Edward Hughes, Matthew Lai, Aditi Mavalankar, Richie Steigerwald, Chris Apps, Yusuf Aytar, Sarah Bechtle, Feryal Behbahani, Stephanie Chan, Nicolas Heess, Lucy Gonzalez, Simon Osindero, Sherjil Ozair, Scott Reed, Jingwei Zhang, Konrad Zolna, Jeff Clune, Nando de Freitas, Satinder Singh, Tim Rocktäschel]
year: 2024
venue: "ICML 2024"
tags: [world-models, image-video-generation, embodied-ai, reinforcement-learning]
url: "https://arxiv.org/abs/2402.15391"
date_ingested: 2026-06-25
---

# Genie: Generative Interactive Environments

![[2024-genie-thumbnail.png]]

## Research gap

Prior generative video models produce non-interactive video clips, while world models require action-labelled training data (action-state pairs). There is no model class that can learn frame-level interactive control from unlabelled internet video alone — the gulf between passive video generation and interactive, controllable environments remains unbridged.

## Contributions

- Introduces **generative interactive environments**, a new paradigm where interactive, playable environments are generated from a single image or text prompt.
- Proposes Genie, an 11B-parameter foundation world model trained entirely from unlabelled internet video (200K+ hours of 2D platformer gameplay), requiring no action annotations.
- Designs a **latent action model (LAM)** that discovers a discrete set of consistent, human-interpretable actions in a fully unsupervised manner using VQ-VAE.
- Demonstrates that the learned latent action space transfers to unseen environments: policies trained via behavioral cloning with latent actions match oracle performance (with ground-truth actions) given as few as 200 expert samples.
- Provides scaling analysis showing graceful improvement from 40M to 11B parameters across both model size and batch size.

## Method

Genie consists of three components, all built on spatiotemporal (ST) transformers that scale linearly with frame count:

1. **Video tokenizer** — A VQ-VAE with ST-transformer encoder/decoder (ST-ViViT) that compresses video frames into discrete tokens. Unlike spatial-only tokenizers, it incorporates temporal dynamics via causal temporal attention, improving video generation quality.
2. **Latent action model (LAM)** — Takes raw pixel frames as input and infers a discrete latent action between each consecutive frame pair. Uses a VQ-VAE with a small codebook (|A|=8 actions) to force the model to capture only the most meaningful inter-frame changes. The encoder sees past frames plus the next frame; the decoder reconstructs the next frame from past frames and the latent action. At inference, only the codebook is retained — users select actions directly.
3. **Dynamics model** — A decoder-only MaskGIT transformer that autoregressively predicts next-frame tokens conditioned on past video tokens and latent actions. Latent actions are added as embeddings (not concatenated), improving controllability. Uses 25 MaskGIT sampling steps per frame at inference.

Training is two-phase: the video tokenizer is trained first, then the LAM and dynamics model are co-trained (LAM on pixels, dynamics model on video tokens with stop-gradient latent actions).

At inference, a user provides a single prompt image, selects a latent action (integer 0–7), and the model generates the next frame. This repeats autoregressively for interactive play.

## Datasets & evaluation

**Datasets:**
- **Platformers**: 6.8M 16-second video clips (30K hours) at 10 FPS, 160×90 resolution, filtered from publicly available internet gaming videos of 2D platformer games.
- **Robotics**: ~130K robot demonstrations from RT1 combined with simulation data and 209K episodes of real robot data. No action labels used — treated purely as video.

**Metrics:**
- **FVD (Fréchet Video Distance)**: measures video generation fidelity.
- **Δt PSNR**: novel controllability metric measuring the difference in PSNR between videos generated from ground-truth-inferred latent actions vs. random actions. Higher values indicate greater action controllability.

**Key results:**
- Scaling from 40M to 2.7B parameters shows consistent training loss reduction; final 11B model trained on 942B tokens.
- Pixel-input LAM outperforms token-input LAM on controllability (Δ4 PSNR: 1.91 vs. 1.33 on Platformers, 2.07 vs. 1.65 on Robotics).
- ST-ViViT tokenizer outperforms both spatial-only ViT and C-ViViT on both FVD and controllability while using less memory.
- Robotics model (2.5B) achieves FVD of 82.7 with consistent, semantically meaningful latent actions (up, down, left).
- LAM-based behavioral cloning policy matches oracle performance on CoinRun with only 200 expert action-mapping samples.

## Limitations

- Low resolution (160×90 for Platformers) and short generation horizons (16 frames at 10 FPS = 1.6 seconds).
- Inference is slow — 1 FPS, far from real-time interactive play.
- Limited to 8 discrete latent actions, which constrains expressiveness for complex environments.
- Trained only on 2D platformers and simple robotics domains; generalization to 3D environments or complex real-world scenes is undemonstrated.
- The dynamics model suffers from typical autoregressive degradation over longer horizons.
- No text conditioning — relies entirely on image prompts for environment specification.

## Key takeaways

- Action labels are not necessary to learn interactive world models — unsupervised latent action discovery from video alone produces consistent, transferable controls.
- The latent action space is not just an internal mechanism but a useful interface: actions remain consistent across different inputs (like learning a new game controller), and they transfer to unseen environments for behavioral cloning.
- ST-transformers with linear scaling in frame count (via factored spatial/temporal attention) are an effective architecture for video world models, enabling scaling to 11B parameters.
- Genie occupies a unique position between video models (video-level control from text) and world models (frame-level control from actions): it achieves frame-level controllability like world models but requires only video data like video models.
- The demonstration that a foundation world model can be prompted with arbitrary images (text-to-image outputs, sketches, photographs) and produce interactive environments opens a path toward unlimited environment generation for agent training.
