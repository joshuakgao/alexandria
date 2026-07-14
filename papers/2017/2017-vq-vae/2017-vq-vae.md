---
title: "Neural Discrete Representation Learning"
authors: [Aaron van den Oord, Oriol Vinyals, Koray Kavukcuoglu]
year: 2017
venue: "NeurIPS 2017"
tags: [encoder]
url: "https://arxiv.org/abs/1711.00937"
date_ingested: 2026-06-26
---

# Neural Discrete Representation Learning

![[2017-vq-vae-thumbnail.png]]

## Research gap

Continuous latent variable models (VAEs) dominate unsupervised representation learning, but discrete representations are a more natural fit for many modalities (language, speech symbols, image descriptions) and for complex reasoning and planning. Existing approaches to discrete latent variables (NVIL, VIMCO, Gumbel-softmax) suffer from high variance gradients and cannot match the performance of continuous VAEs. Additionally, powerful autoregressive decoders cause "posterior collapse" in standard VAEs, where latent variables are ignored entirely.

## Contributions

- Introduces **VQ-VAE** (Vector Quantised-Variational AutoEncoder), a generative model that learns discrete latent representations via vector quantisation, achieving performance comparable to continuous VAEs.
- Proposes a simple training procedure using the **straight-through estimator** to copy gradients through the non-differentiable quantisation step, combined with a dictionary learning loss and commitment loss.
- Demonstrates that VQ-VAE avoids **posterior collapse** even with powerful autoregressive decoders, because discrete codes force information through the bottleneck.
- Shows that learned discrete representations capture high-level, semantically meaningful structure across images, speech, and video — including unsupervised phoneme discovery and speaker-content factorisation.

## Method

VQ-VAE consists of an encoder, a discrete embedding space (codebook), and a decoder:

1. **Encoder** maps input x to continuous output z_e(x).
2. **Vector quantisation** replaces z_e(x) with the nearest codebook vector e_k (nearest neighbour lookup), producing discrete codes.
3. **Decoder** reconstructs from the quantised representation z_q(x).

The total loss has three terms:
- **Reconstruction loss**: log p(x|z_q(x)), optimising encoder and decoder.
- **Codebook loss**: ||sg[z_e(x)] - e||², moving codebook vectors toward encoder outputs (dictionary learning).
- **Commitment loss**: β||z_e(x) - sg[e]||², preventing encoder outputs from growing unboundedly away from codebook vectors.

Gradients pass through the quantisation step via the straight-through estimator (copying gradients from decoder input to encoder output). The prior p(z) is uniform during VQ-VAE training; afterward, an autoregressive model (PixelCNN for images, WaveNet for audio) is trained over the discrete latent space to enable generation.

## Datasets & evaluation

**Images:**
- CIFAR-10: 4.67 bits/dim (vs. 4.51 for continuous VAE), first discrete latent model competitive with continuous counterparts.
- ImageNet 128×128: compressed to 32×32×1 discrete space (K=512), ~42.6× bit reduction. Reconstructions only slightly blurrier than originals; PixelCNN prior generates coherent class-conditional samples.
- DeepMind Lab 84×84: near-identical reconstructions; two-stage VQ-VAE compresses entire images to 3 latents (27 bits) while preserving scene layout and textures.

**Speech (VCTK, 109 speakers):**
- 64× temporal compression; reconstructions preserve content but alter prosody, showing the discrete space captures high-level speech content invariant to low-level waveform details.
- Unsupervised phoneme discovery: 49.3% accuracy on 41-way phoneme classification from discrete codes (vs. 7.2% random baseline).
- Speaker conversion: encoding with one speaker, decoding with another speaker ID transfers voice while preserving content.

**Video (DeepMind Lab):**
- Action-conditional video generation entirely in latent space; generates coherent frame sequences conditioned on "move forward" or "move right" without pixel-space generation during rollout.

## Limitations

- Two-stage training (VQ-VAE then prior) prevents joint optimisation, potentially leaving performance on the table.
- Reconstructions are slightly blurry compared to originals due to MSE loss in pixel space; perceptual losses (GANs) not explored.
- Codebook utilisation can be uneven — some embedding vectors may go unused, wasting capacity.
- Evaluation limited to relatively small-scale datasets and models by modern standards.
- The straight-through estimator is a biased gradient approximation; theoretical understanding of when it works well is limited.

## Key takeaways

- Discrete latent representations can match continuous VAEs in log-likelihood while providing compressed, symbolic codes that capture high-level structure — a qualitatively different kind of representation.
- The combination of vector quantisation with the straight-through estimator is remarkably simple and effective, avoiding the high-variance gradient problems of other discrete variable methods.
- VQ-VAE's resistance to posterior collapse (unlike standard VAEs with powerful decoders) makes it uniquely suitable as a tokeniser for downstream autoregressive models — a design pattern later adopted by DALL-E, Genie, and many video/world models.
- The unsupervised discovery of phoneme-like units from raw audio demonstrates that discrete bottlenecks can force models to learn linguistically meaningful structure without supervision.
- The two-stage paradigm (learn discrete codes, then model their distribution autoregressively) became a foundational recipe for generation across modalities.
