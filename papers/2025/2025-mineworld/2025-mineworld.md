---
title: "MineWorld: A Real-Time and Open-Source Interactive World Model on Minecraft"
authors: [Junliang Guo, Yang Ye, Tianyu He, Haoyu Wu, Yushu Jiang, Tim Pearce, Jiang Bian]
year: 2025
tags: [world-models, image-video-generation]
url: "https://arxiv.org/abs/2504.08388"
date_ingested: 2026-07-04
---

# MineWorld: A Real-Time and Open-Source Interactive World Model on Minecraft

![[2025-mineworld-thumbnail.png]]

## Research gap
Existing video-based world models suffer from two critical limitations: (1) inference is too slow for real-time interaction due to the large number of tokens required to represent video frames, and (2) there is no standardized way to evaluate whether generated scenes faithfully follow input control signals (controllability). Diffusion-based approaches like Oasis achieve reasonable visual quality but lack both speed and controllability metrics.

## Contributions
- **MineWorld**, an open-source, real-time interactive world model for Minecraft built on a visual-action autoregressive Transformer that jointly tokenizes game states and actions.
- A **parallel decoding algorithm** that exploits spatial redundancy in adjacent image tokens for >3× speedup over standard autoregressive decoding, enabling 4–7 FPS generation.
- **Controllability evaluation metrics** using an inverse dynamics model (IDM) to measure how well generated frames follow input actions, including discrete action classification (precision/recall/F1) and camera movement L1 loss.

## Method
MineWorld uses a two-component architecture: (1) **tokenizers** that convert visual frames and actions into discrete tokens, and (2) an **autoregressive Transformer decoder** (LLaMA architecture) trained with next-token prediction on interleaved visual-action token sequences. The visual tokenizer is a VQ-VAE (8K codebook, 16× spatial compression) fine-tuned on Minecraft data, converting each 224×384 frame into 336 tokens. The action tokenizer encodes Minecraft's keyboard/mouse inputs into 11 tokens per action (7 exclusive discrete action classes + 2 camera angle bins + BOS/EOS). For each state-action pair, visual and action tokens are concatenated and interleaved, producing ~5.5K tokens per 16-frame training clip. The parallel decoding algorithm generates spatially adjacent tokens simultaneously (tokens at positions (i,j+1) and (i+1,j) are decoded in parallel), achieving a theoretical speedup of h×w/(h+w−1). The model is fine-tuned with an attention mask matching the parallel decoding pattern to close the train-inference gap. Trained on 10M video clips (~160M frames, 55B tokens) from the VPT dataset on 32 A100 GPUs.

## Datasets & evaluation
Trained on the VPT dataset (Baker et al., 2022) — recorded Minecraft gameplay with paired actions. Evaluated on 1K held-out clips. Compared against Oasis (500M diffusion-based world model). MineWorld outperforms Oasis on all metrics: visual quality (FVD 227 vs. 377, PSNR 15.69 vs. 14.38 at 1.2B scale) and controllability (F1 0.73 vs. 0.41, camera L1 1.02 vs. 2.60). The 300M model achieves 5.91 FPS (sufficient for professional-level APM), while the 1.2B model achieves 3.01 FPS (sufficient for amateur play). Clear scaling behavior is observed: larger models improve on both quality and controllability.

## Limitations
- Evaluated only in Minecraft; generalization to other game environments or real-world domains is untested.
- Uses an image-level VQ tokenizer (per-frame), not a video-level tokenizer with temporal compression — leaving temporal redundancy on the table.
- Parallel decoding introduces some quality degradation compared to sequential decoding, requiring fine-tuning to mitigate.
- Resolution limited to 224×384 (downscaled from native 360×640).
- The IDM-based controllability metric depends on the accuracy of the pretrained IDM (90.6% keypress accuracy), so measurement error propagates into evaluation.

## Key takeaways
- Autoregressive Transformers significantly outperform diffusion-based approaches (Oasis) for interactive world modeling in both visual quality and action controllability at comparable model scales.
- Parallel decoding via spatial token dependencies is a practical, training-free acceleration technique that enables real-time interactive world models — achieving >3× speedup with negligible quality loss after fine-tuning.
- Joint tokenization of visual states and actions in a single autoregressive sequence naturally learns state-action correlations, enabling the model to function as both a world model (predicting states from actions) and a policy model (predicting actions from states).
- Controllability — not just visual quality — is a critical evaluation axis for world models, and IDM-based metrics provide a principled way to measure it.
