---
title: "SmartBrush: Text and Shape Guided Object Inpainting with Diffusion Model"
authors: [Shaoan Xie, Zhifei Zhang, Zhe Lin, Tobias Hinz, Kun Zhang]
year: 2022
tags: [image-inpainting, diffusion-models, vision-language-models]
url: ""
pdf: "[[2022-smartbrush-text-shape-guided-inpainting.pdf]]"
date_ingested: 2026-06-18
---

# SmartBrush: Text and Shape Guided Object Inpainting with Diffusion Model

![[2022-smartbrush-text-shape-guided-inpainting-thumbnail.png]]

## Research gap

SmartBrush advances text-guided inpainting by incorporating explicit shape guidance and precision control, addressing key limitations of existing methods. The core innovation is recognition that text descriptions and mask shapes serve different purposes: text specifies semantic content while masks constrain spatial placement. SmartBrush decouples these through a precision factor, allowing users to specify how strictly to follow mask boundaries (precise masks → exact placement, coarse masks → rough containment). To preserve background in coarse inpainting, the model predicts instance masks during sampling, enabling selective inpainting of foreground while preserving background. A novel multi-task training strategy jointly trains object inpainting with text-to-image generation, leveraging larger training data and improving semantic understanding. The method addresses text-misalignment (model ignores spatial constraints) and mask-misalignment (model ignores text guidance) problems in prior approaches.

## Contributions

- **Text+shape multimodal guidance**: First to combine text and shape guidance with variable precision control for object inpainting
- **Precision-aware masking**: Introduces precision factor allowing users to control mask adherence through mask blurring
- **Foreground-background separation**: Predicts instance masks during sampling to preserve original background with coarse masks
- **Local text descriptions**: Uses instance segmentation masks with localized text descriptions rather than full-image captions
- **Multi-task training**: Jointly trains object inpainting with text-to-image generation to leverage more data

## Method

**Architecture and conditioning**:
- Base: Stable Diffusion fine-tuned for inpainting
- Text guidance: Implemented through cross-attention mechanisms (CLIP embedding of text prompt)
- Shape guidance: Encoded as input mask conditioned through concatenation with noised image
- Instance prediction: Augmented UNet predicts foreground mask as auxiliary output

**Training strategy**:
1. Generate masks at multiple precision levels (sharp → blurred) from instance masks
2. Use localized text descriptions of inpainted objects (not full-image captions)
3. Multi-task loss combining inpainting and text-to-image generation
4. Joint training improves semantic understanding and background preservation

**Inference procedure**:
1. Forward diffusion with masking and text prompt
2. At each reverse step:
   - Generate content using both text and shape guidance
   - Predict instance mask of generated content
   - Use predicted mask to preserve original background (for coarse inputs)
   - Refine both content and mask prediction iteratively

**Key innovations**:
- Precision factor enables flexible mask interpretation
- Background preservation via predicted mask replacement
- Multi-task training leverages diverse data sources

## Datasets & evaluation

- **Visual quality**: Outperforms DALL-E 2, Stable Inpainting, Blended Diffusion, GLIDE
- **Mask adherence**: Better shape control than text-only methods
- **Background preservation**: Superior for coarse masks (bounding boxes, rough strokes)
- **Text alignment**: Improved semantic matching vs. mask misalignment baselines
- **User studies**: Users prefer SmartBrush across shape, text alignment, and realism axes

## Limitations

- How does precision control affect user experience and learning curves?
- Can the approach extend to multiple objects or sequential inpainting?
- How sensitive is background preservation to predicted mask quality?
- Can style transfer or appearance customization be incorporated?

## Key takeaways

SmartBrush extends diffusion-based inpainting work (GLIDE, Stable Inpainting, Blended Diffusion) by introducing shape guidance and precision control. While prior methods focus on either text guidance or mask-based conditioning, SmartBrush recognizes these as complementary modalities requiring distinct handling. The multi-task training strategy builds on text-to-image generation advances (CLIP guidance, fine-tuning strategies) adapted for the inpainting domain.
