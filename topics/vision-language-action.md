---
topic: Vision-Language-Action Models
slug: vision-language-action
---

# Vision-Language-Action Models

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "vision-language-action")
SORT year DESC
```

## Overview
Vision-language-action (VLA) models extend vision-language models by adding action prediction capabilities for robot control. These models leverage internet-scale pretraining on images and text, then fine-tune on robot demonstration data to output low-level motor commands. VLAs represent a convergence of foundation model scaling, imitation learning, and embodied AI, aiming to create generalist robot policies that can interpret natural language instructions and act in the physical world.

## Trends
- Scaling action experts independently from VLM backbones, rather than relying solely on larger language models.
- Curriculum learning strategies that progressively train on harder tasks across multiple embodiments.
- Integration of diffusion models as action decoders for handling multimodal action distributions.
- Sub-step reasoning annotations to enable end-to-end long-horizon task completion without external high-level planners.

## Open questions
- How to achieve true zero-shot transfer across entirely new robot morphologies without embodiment-specific heads.
- Optimal balance between VLM backbone scale and action expert scale for data-efficient learning.
- Reducing reliance on human demonstrations through simulation, self-play, or other automated data generation.
- Handling contact-rich, deformable-object manipulation reliably in long-horizon settings.
