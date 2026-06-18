---
topic: Image Inpainting
slug: image-inpainting
---

# Image Inpainting

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "image-inpainting")
SORT year DESC
```

## Overview

*Last updated: 2026-04-07 | Sources: 4 papers*

## Current thesis

Diffusion-based image inpainting has established itself as the dominant paradigm through the ability to leverage powerful pre-trained generative priors without task-specific training. The field has rapidly evolved from unconditional mask-based generation (2022) toward increasingly sophisticated multimodal guidance that enables intuitive user control through text, spatial masks, color strokes, and reference images. Modern approaches unify multiple guidance modalities within single frameworks, offering unprecedented flexibility while maintaining high-quality results.

## Key trends

**2022–2025 evolution:**

1. **Foundational diffusion approach** (2022):
   - [[2022-repaint-inpainting-diffusion|RePaint]] introduces training-free diffusion inpainting using pre-trained DDPMs
   - Repainting strategy (forward-backward diffusion time) for semantic refinement
   - Generalization to arbitrary mask distributions without mask-specific training

2. **Guided generation** (2022–2023):
   - [[2022-smartbrush-text-shape-guided-inpainting|SmartBrush]] adds text+shape guidance with precision control
   - Background preservation via foreground mask prediction
   - Multi-task training combining inpainting with text-to-image generation

3. **Unified multimodal frameworks** (2023):
   - [[2023-unipaint-multimodal-inpainting|Uni-paint]] unifies unconditional, text, stroke, exemplar, and mixed modes
   - Few-shot fine-tuning on single images enables customization without large datasets
   - Masked attention control prevents boundary overflow

4. **Reference-based refinement** (2025):
   - [[2025-corrfill-reference-based-inpainting|CorrFill]] adds explicit geometric correspondence guidance
   - Training-free plug-in module applicable to multiple baseline methods
   - Cyclic refinement of correspondences and inpainting for improved faithfulness

## Open problems

- How can inpainting be extended to video with temporal consistency?
- Can semantic and spatial guidance be further decoupled for more intuitive control?
- What is the minimum computational cost for real-time inpainting?
- How do different guidance modalities interact when combined?
- Can inpainting adapt to user corrections or iterative refinement?

## Contradictions and debates

*No major contradictions—field converges toward multimodal diffusion approaches.*

## Recommended reading order

1. Start with [[papers/2022-repaint-inpainting-diffusion|RePaint]] to understand foundational diffusion-based approach
2. Then read [[papers/2022-smartbrush-text-shape-guided-inpainting|SmartBrush]] to see addition of text+shape guidance
3. Read [[papers/2023-unipaint-multimodal-inpainting|Uni-paint]] for unification of multiple modalities
4. Finish with [[papers/2025-corrfill-reference-based-inpainting|CorrFill]] for reference-based refinements
5. Explore concept pages ([[concepts/image-inpainting|Image Inpainting]], [[concepts/diffusion-based-inpainting|Diffusion-Based Inpainting]], [[concepts/multimodal-guidance|Multimodal Guidance]]) for deeper context

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
