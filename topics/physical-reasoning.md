---
topic: Physical Reasoning
slug: physical-reasoning
---

# Physical Reasoning

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "physical-reasoning")
SORT year DESC
```

## Overview
Physical reasoning in AI concerns whether models can understand and predict the behavior of objects governed by physical laws — including kinematics (size, velocity, acceleration), dynamics (forces, mass, friction), and causal interactions (collisions, deformations). The field spans qualitative understanding (predicting what will happen) and quantitative inference (estimating numerical physical quantities). Recent work reveals that even frontier VLMs largely rely on memorized world knowledge rather than genuinely reasoning from visual input and provided priors, highlighting a fundamental gap between verbal plausibility and numerical correctness.

## Trends
- Benchmarks are shifting from qualitative VQA (multiple-choice about physical events) to quantitative evaluation requiring numerical estimates in real-world units (QuantiPhy, CVPR 2026).
- Frontier VLMs achieve near-human performance on aggregate metrics but fail counterfactual tests — when provided priors are altered, predictions remain anchored to memorized real-world magnitudes rather than tracking the inputs.
- Model scale improves quantitative reasoning (especially for dynamic/temporal tasks) but does not close the gap to input-faithful physical reasoning.
- Chain-of-thought prompting is largely ineffective for quantitative physical tasks, as decomposition amplifies early numerical errors.

## Open questions
- Can VLMs be trained to perform input-faithful quantitative reasoning rather than relying on parametric priors?
- How should physical reasoning benchmarks extend beyond translational kinematics to rotational dynamics, deformable objects, and complex multi-body interactions?
- What training objectives or architectures would enable VLMs to genuinely exploit pixel-level precision for physical inference?
- How does physical reasoning capability relate to embodied AI performance — does improving quantitative accuracy on benchmarks translate to better robot behavior?
