---
title: "ViewDelta: Scaling Scene Change Detection through Text-Conditioning"
authors: [Subin Varghese, Joshua Gao, Vedhus Hoskere]
year: 2025
tags: [change-detection, synthetic-datasets]
url: ""
pdf: "[[2025-viewdelta.pdf]]"
date_ingested: 2026-06-18
---

# ViewDelta: Scaling Scene Change Detection through Text-Conditioning

![[2025-viewdelta-thumbnail.png]]

## Research gap

ViewDelta addresses a fundamental ambiguity in scene change detection: what counts as a "relevant" change depends on the application. Vegetation growth might be labeled as a change in one dataset but ignored in another, making joint training across datasets contradictory. ViewDelta resolves this by conditioning change detection on natural language prompts — users specify what kind of change matters (e.g., "vehicle", "bulldozer, sign", or "all changes") and the model produces a binary segmentation mask for only the specified changes.

## Contributions

- Text-conditioned change detection framework that resolves the relevance ambiguity, enabling unambiguous joint training across heterogeneous SCD datasets
- CSeg dataset: 500K+ synthetic image pairs with 300K+ unique prompts, 24K unique classes, and red herring masks to combat inpainting artifacts (94% label accuracy)
- Single jointly-trained model competitive with or superior to dataset-specific models across street-view (PSCD), satellite (SYSU-CD), and urban (VL-CMU-CD) domains
- Robust to viewpoint variations without spatial alignment assumptions in the segmentation head

## Method

**Architecture:** Frozen SigLIP text encoder produces text tokens; frozen DINOv2 image encoder produces patch tokens for both images. All tokens (image A, image B, text, learnable segmentation query tokens) are concatenated with positional embeddings and processed by a 12-layer ViT backbone. Only the segmentation query tokens are passed to a lightweight 5-layer convolutional decoder to produce the binary change mask. No feature aggregation (addition, subtraction, concatenation) between image tokens — the ViT learns cross-image reasoning through self-attention.

**CSeg generation:** Select SA-1B images → LLaVA-Next proposes up to 10 object classes → select 1-5 least-represented classes → Grounded SAM 2 extracts instance masks → randomly select subset to inpaint with LaMa → add "red herring" inpainted regions (non-target SA-1B masks) → apply random affine transformation for view variation. "All changes" pairs inpaint 5-10 random SA-1B masks with generic prompts.

**Training:** Joint training on CSeg + PSCD (binary + multi-class) + SYSU-CD + VL-CMU-CD + Diff-1/Diff-2 variants. Adam optimizer, lr=2e-5, batch size 16, DeepSpeed ZeRO Stage 2, mixed precision on 4× A100 GPUs.

## Datasets & evaluation

**CSeg:** 83.80% IoU, vastly outperforming Gemini 2.5 Pro (37.15% IoU). **PSCD semantic:** General model 51.2% IoU (vs. fine-tuned 55.5%). **SYSU-CD:** General model 67.05% IoU, fine-tuned 70.09% (2nd only to MambaBCD). **Unaligned PSCD:** F1 63.6 (Diff-1) and 62.5 (Diff-2), dramatically outperforming Dinov2 RSCD (28.4/19.1) and C-3PO (24.6/16.5). **Unaligned VL-CMU-CD:** General model F1 81.8 (aligned), 76.1 (Diff-2), competitive with fine-tuned version. Ablation shows DINOv2 embeddings and segmentation query tokens are critical; text prompts most impactful for semantic tasks (PSCD).

## Limitations

- CSeg is synthetic (inpainting-based); no large-scale real dataset with varying views and varying text prompts exists for evaluation
- Performance gap between general and fine-tuned models (e.g., 4.3% IoU on PSCD) suggests joint training hasn't fully closed the generalization gap
- Text conditioning relies on users knowing what to look for; does not address discovery of unexpected changes (though "all changes" prompt partially covers this)
- Limited to image pairs; no video or multi-view extension
- 94% CSeg label accuracy means 6% noise from Grounded SAM 2 misclassifications, especially on challenging classes
- Fine-tuned VL-CMU-CD model actually decreases performance relative to general model due to label noise overfitting — highlighting dataset quality issues

## Key takeaways

The framework uses frozen DINOv2 image embeddings and frozen SigLIP text embeddings fed into a ViT backbone with learnable segmentation query tokens (inspired by DETR). Crucially, the segmentation head operates only on segmentation query tokens, not directly on image tokens, removing spatial alignment assumptions and enabling robustness to viewpoint changes. A single ViewDelta model is jointly trained on CSeg (their new synthetic dataset), PSCD, SYSU-CD, VL-CMU-CD, and unaligned variants of PSCD and VL-CMU-CD.

To enable training, the authors release CSeg (Conditional change Segmentation), the first large-scale dataset for text-conditioned SCD: 500K+ image pairs with 300K+ unique textual prompts, generated using LLaVA-Next for class proposals, Grounded SAM 2 for instance masks, and LaMa for inpainting. CSeg includes "red herring" inpainted regions to prevent learning inpainting artifacts.

ViewDelta is the first text-conditioned SCD method, adding a semantic control dimension absent from all other approaches in this wiki. It directly builds on GeSCF (evaluated as baseline on VL-CMU-CD), extending its foundation-model-based approach with language conditioning. The DINOv2 backbone connects to the Dinov2 RSCD baseline. The synthetic data generation (CSeg) parallels Changen2's philosophy but targets ground-level SCD with language grounding rather than remote sensing. Unlike DynamicEarth's post-hoc open-vocabulary labeling (which detects all changes then describes them), ViewDelta conditions detection itself on text, enabling the model to ignore irrelevant changes during inference. The viewpoint robustness from removing spatial alignment assumptions relates to VSCD's unconstrained motion handling but through architectural design rather than temporal reasoning.
