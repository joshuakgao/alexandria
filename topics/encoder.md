---
topic: Encoder
slug: encoder
---

# Encoder

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "encoder")
SORT year DESC
```

## Overview

This topic covers encoder architectures and representation learning methods — models that compress high-dimensional inputs (images, video, audio, text) into compact latent representations suitable for downstream tasks. VQ-VAE (van den Oord et al., NeurIPS 2017) introduced vector quantisation as a mechanism for learning discrete latent codes, producing compressed symbolic representations that capture high-level semantic structure while avoiding posterior collapse. The two-stage paradigm it established — learn a discrete tokeniser, then model the token distribution autoregressively — became foundational for generation across modalities, underpinning later systems like DALL-E, Genie, and numerous video/world models. T5 (Raffel et al., JMLR 2020) provided the most systematic comparison of Transformer encoder architectures for NLP, finding that the original encoder-decoder architecture outperforms decoder-only and prefix LM variants at equivalent computational cost in a text-to-text setting, and that relative position embeddings with simplified layer normalization are effective architectural choices. V-JEPA (Bardes et al., ICLR 2025) establishes feature prediction as a stand-alone self-supervised objective for video representation learning — predicting masked video representations in latent space rather than reconstructing pixels — producing versatile frozen encoders that outperform all prior video methods on both motion and appearance tasks while training ~2× faster than pixel reconstruction approaches.

## Trends

- **Discrete over continuous latents for generation pipelines**: VQ-VAE demonstrated that discrete codes with autoregressive priors can match continuous VAEs while providing compressed, symbolic representations that naturally interface with sequence models. This design pattern became the standard tokenisation approach for image, video, and world model generation.
- **Straight-through estimation as a practical tool**: The straight-through estimator for gradient propagation through non-differentiable quantisation proved surprisingly effective, enabling simple training without the variance issues of REINFORCE-style estimators or the bias-variance trade-offs of Gumbel-softmax relaxations.
- **Encoder-decoder vs. decoder-only for NLP**: T5's systematic comparison showed that encoder-decoder Transformers outperform decoder-only models at equivalent compute (not parameter count) for downstream NLP tasks, because the encoder's fully-visible attention over the input is more efficient than the decoder's causal masking. Despite this finding, the field largely adopted decoder-only scaling (GPT-3, PaLM, LLaMA), suggesting other factors (simplicity, scaling ease, few-shot prompting) dominate architectural choice at scale.
- **Feature prediction over pixel reconstruction for video**: V-JEPA demonstrates that predicting in representation space is consistently superior to pixel-level reconstruction for self-supervised video pretraining — both in frozen evaluation quality and training efficiency. By avoiding the need to reconstruct low-level visual details, the encoder focuses capacity on semantically meaningful features. The approach is significantly more label-efficient: performance degrades gracefully with reduced supervision, suggesting the learned representations are more semantically structured than pixel-reconstructive alternatives.
- **Attentive probing reveals hidden representation quality**: V-JEPA shows that the gap between feature prediction and pixel reconstruction methods is obscured by linear probing — attentive probing (cross-attention pooling) unlocks +17pp on action recognition from the same frozen backbone. This suggests many encoders may be undervalued by overly simplistic evaluation protocols.

## Open questions

- How to jointly optimise the encoder/codebook and the autoregressive prior end-to-end, rather than the current two-stage training which may leave performance on the table.
- Optimal codebook design: how to prevent codebook collapse (unused codes) and determine the right codebook size and dimensionality for different modalities and downstream tasks.
- Whether learned discrete tokenisers can be made universal across modalities, or whether modality-specific architectural choices remain necessary.
- Why decoder-only architectures dominated NLP scaling despite T5's evidence that encoder-decoder is more compute-efficient — whether this is due to few-shot prompting advantages, simpler scaling, or other factors not captured in T5's fine-tuning-focused evaluation.
- Whether V-JEPA's feature prediction paradigm scales to larger, more diverse video datasets — the current VideoMix2M is orders of magnitude smaller than internet-scale image datasets, and V-JEPA's image task performance still trails DINOv2/OpenCLIP, suggesting data diversity may be the bottleneck rather than the objective.
- Whether feature prediction and pixel reconstruction objectives can be productively combined, or whether they represent fundamentally different representation learning trade-offs (semantic compression vs. full detail preservation).
