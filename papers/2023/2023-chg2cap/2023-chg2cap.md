---
title: "Changes to Captions: An Attentive Network for Remote Sensing Change Captioning"
authors: [Shizhen Chang, Pedram Ghamisi]
year: 2023
venue: "IEEE Transactions on Image Processing"
tags: [change-detection, remote-sensing]
url: ""
pdf: "[[2023-chg2cap.pdf]]"
date_ingested: 2026-06-18
---

# Changes to Captions: An Attentive Network for Remote Sensing Change Captioning

![[2023-chg2cap-thumbnail.png]]

## Research gap

Chg2Cap is an attentive encoder-decoder network specifically designed for remote sensing change captioning (RSCC) — generating natural language descriptions of land cover changes from bitemporal satellite/aerial image pairs. The paper highlights that remote sensing change captioning presents unique challenges compared to natural image change captioning: longer time intervals between acquisitions (months to years), complex distribution gaps from illumination and seasonal effects, compressed 3D-to-2D information from top-down views, and more complex multi-object change descriptions.

## Contributions

- Systematic comparison of change captioning challenges across natural images, synthetic images, and remote sensing images, establishing RSCC as a distinct task with unique requirements
- Hierarchical self-attention (HSA) block combining dual self-attention (per-image) and joint self-attention (cross-image) for progressive change feature extraction
- Residual block with cosine mask that enhances both consistency and inconsistency features between bitemporal image pairs
- State-of-the-art results on Dubai-CC and LEVIR-CC remote sensing change captioning benchmarks

## Method

**Feature Extractor:** Siamese ResNet-101 (pretrained on ImageNet, final FC layer removed) extracts 2048-channel features from each bitemporal image independently.

**Attentive Encoder:** The HSA block first applies positional embedding, then stacks dual self-attention (DSA) units that process each image's features independently, followed by joint self-attention (JSA) units that process concatenated features from both images. This hierarchical design enables the model to first understand intra-image structure before capturing inter-image differences. The residual block applies a cosine mask to weight the element-wise difference between feature pairs, enhancing change-relevant signals while suppressing noise.

**Caption Decoder:** Transformer-based decoder with residual connections takes the image embedding and word embedding as input, modeling their inter-relationship to generate change descriptions autoregressively.

**Training:** Adam optimizer, beam search (size 3) for inference, evaluated with BLEU-1/2/3/4, METEOR, ROUGE-L, CIDEr-D metrics. Best model selected by BLEU-4 on validation set.

## Datasets & evaluation

- Achieves best performance on both Dubai-CC and LEVIR-CC across all standard captioning metrics compared to prior RSCC methods and natural image change captioning methods adapted to remote sensing
- Ablation shows both HSA block and ResBlock contribute independently; combining them yields the best results
- Shallow decoder (1-2 layers) outperforms deeper decoders, suggesting overly complex decoders can hurt word generation
- Attention weight visualization confirms the model correctly focuses on changed regions (buildings, roads, vegetation transitions)

## Limitations

- Evaluated only on remote sensing datasets (Dubai-CC, LEVIR-CC); generalization to other domains (surveillance, medical) untested
- The cosine mask in the residual block is hand-designed; learnable masking or adaptive fusion strategies could improve performance
- Limited to bitemporal (two-image) input; multi-temporal sequences with progressive changes not supported
- Does not spatially localize changes in the output (no bounding boxes or change masks alongside captions)
- Caption quality is bounded by the quality and diversity of reference annotations in the relatively small datasets (500 and 10,077 image pairs)

## Key takeaways

The architecture has three components: (1) a Siamese ResNet-101 feature extractor for bitemporal image pairs, (2) an attentive encoder with a hierarchical self-attention (HSA) block that captures inter- and intra-feature relationships through stacked dual and joint self-attention units, plus a residual block with cosine masking to enhance consistency/inconsistency between feature pairs, and (3) a transformer-based caption decoder with residual connections. The HSA block first processes each image's features independently via dual self-attention, then combines them via joint self-attention — enabling the model to first understand each time step individually before comparing them.

Evaluated on two remote sensing datasets (Dubai-CC and LEVIR-CC), Chg2Cap achieves state-of-the-art performance over both remote sensing and natural image change captioning methods, demonstrating that domain-specific attention design matters for remote sensing change description.

Chg2Cap bridges the gap between natural image change captioning (Spot-the-Diff, CLEVR-Change) and remote sensing image analysis. Prior RSCC works (Hoxha et al., Liu et al.) used simpler fusion strategies or dual transformers without hierarchical attention. Natural image methods (DUDA, M-VAM, VACC) handle viewpoint distraction but don't address the distribution gaps and complex land cover changes specific to remote sensing. The hierarchical self-attention design is inspired by but distinct from standard transformer self-attention — the dual-then-joint processing order is specific to the bitemporal comparison task. Compared to the later OneDiff (2025), Chg2Cap is a specialist model optimized for remote sensing rather than a generalist VLM.
