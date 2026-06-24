---
title: "Uni-paint: A Unified Framework for Multimodal Image Inpainting with Pretrained Diffusion Model"
authors: [Shiyuan Yang, Xiaodong Chen, Jing Liao]
year: 2023
venue: "ACM MM 2023"
tags: [image-inpainting, diffusion-models, vision-language-models]
url: ""
pdf: "[[2023-unipaint-multimodal-inpainting.pdf]]"
date_ingested: 2026-06-18
---

# Uni-paint: A Unified Framework for Multimodal Image Inpainting with Pretrained Diffusion Model

![[2023-unipaint-multimodal-inpainting-thumbnail.png]]

## Research gap

Uni-paint is the first unified framework for multimodal image inpainting, supporting unconditional, text-guided, stroke-guided, exemplar-guided, and mixed-mode inpainting within a single pretrained Stable Diffusion model. The key innovation is recognition that different guidance modalities require different conditioning interfaces: semantic guidance (text, exemplars) flows through cross-attention mechanisms, while spatial guidance (strokes) uses image blending. Uni-paint employs few-shot learning—fine-tuning only on the single target image—eliminating dataset dependencies and enabling generalization to customized images. A masked finetuning strategy trains the model to generate context-plausible content in unknown regions unconditionally, while masked attention control mechanisms restrict generated content to inpainted areas, preventing boundary overflow. This framework achieves comparable performance to single-modal methods while offering unmatched flexibility through multimodal combination.

## Contributions

- **Unified multimodal framework**: First to support unconditional, text, stroke, exemplar, and mixed-mode inpainting in one model
- **Few-shot masked finetuning**: Fine-tune only on single target image; eliminates large-scale dataset requirements
- **Dual conditioning interfaces**: Semantic (cross-attention) and spatial (image blending) interfaces for different modality types
- **Masked attention control**: Novel mechanism restricting cross-attention and self-attention to prevent content overflow beyond mask boundaries
- **Flexible mode combination**: Users can freely combine multiple guidance modalities for desired control

## Method

**Architecture design**:
- Base: Pretrained Stable Diffusion (text-to-image)
- Two conditional interfaces:
  1. **Textual interface**: Cross-attention for semantic guidance (text prompts, exemplar embeddings)
  2. **Spatial interface**: Image blending for spatial guidance (stroke masks, spatial layouts)

**Few-shot masked finetuning**:
1. Fine-tune on single target image with null conditioning (unconditional generation)
2. Use masked loss: enforce model to generate valid content only in unknown (masked) regions
3. Leverages Stable Diffusion's pre-trained semantic understanding of known regions
4. Model learns image-specific context while respecting mask boundaries

**Masked attention control**:
1. Augment cross-attention layers with mask-aware computations
2. Augment self-attention layers to respect spatial boundaries
3. Restrict attention scope to unknown regions during generation
4. Prevents inpainted content from leaking into known areas

**Multimodal conditioning**:
- **Text**: Standard CLIP embedding through cross-attention
- **Stroke**: Encode as sparse spatial mask, blend during sampling
- **Exemplar**: Extract CLIP embedding from reference image
- **Mixed**: Combine embeddings (e.g., text + exemplar features)

## Datasets & evaluation

- **Unconditional**: Comparable to RePaint on zero-shot inpainting
- **Text-guided**: Competitive with SmartBrush and Stable Inpainting
- **Exemplar-guided**: Strong results for appearance/identity transfer
- **Stroke-guided**: Effective spatial control with coarse strokes
- **Mixed-mode**: Enables intuitive multimodal control (text + stroke + exemplar)
- **Generalization**: Demonstrates good zero-shot transfer to unseen images without retraining

## Limitations

- How does few-shot finetuning affect model's ability to generalize to diverse content?
- Can masked attention control be extended to video inpainting with temporal consistency?
- What is the computational overhead of per-image finetuning?
- How does performance degrade with more aggressive or complex masks?
- Can the framework handle sequential inpainting operations on the same image?

## Key takeaways

Uni-paint builds on recent diffusion-based inpainting work (RePaint, Stable Inpainting, Blended Diffusion, SmartBrush) but is first to unify multiple modalities. The few-shot finetuning approach extends text-driven editing work (Textual Inversion, DreamBooth, InstructPix2Pix) to the inpainting domain. Masked attention control relates to attention manipulation techniques (Prompt-to-Prompt, shape-guided editing) adapted for boundary-aware inpainting.
