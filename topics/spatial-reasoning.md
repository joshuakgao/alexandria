---
topic: Spatial Reasoning
slug: spatial-reasoning
---

# Spatial Reasoning

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "spatial-reasoning")
SORT year DESC
```

## Overview

*Last updated: 2026-06-24 | Sources: 4 papers*

## Current thesis

Multimodal LLMs exhibit emerging but substantially subhuman visual-spatial intelligence. The primary bottleneck is spatial reasoning itself — not perception, language, or temporal processing. VSI-Bench shows egocentric-allocentric transformation and relational reasoning account for ~71% of errors. ViewSuite deepens this diagnosis with a sharper finding: VLMs can *track* local view transitions (~70% on short-horizon tasks) but *cannot compose* them into multi-turn plans (best frontier model reaches only 21.3% on interactive view planning). Over 90% of successful planning runs occur only after the model visually encounters the target — models succeed by view matching, not mental localization. The planning gap is cognitive, not perceptual: higher-fidelity rendering yields only marginal gains, while test-time compute (GPT-5.4 Pro over GPT-5.4) meaningfully helps. Theory of Space (ICLR 2026) introduces a complementary dimension: the **active-passive gap**. Even when models reason well over passively provided observations, performance degrades substantially when agents must autonomously explore to gather information (GPT-5.2: 57.1→46.0). Furthermore, cognitive map probing reveals **belief instability** — spatial knowledge degrades over time even with correct local observations — and **belief inertia** — agents fail to overwrite obsolete spatial priors after environment changes, especially in vision-based settings. SpinBench (ICLR 2026) provides the most fine-grained diagnostic decomposition to date, isolating seven progressive spatial skills from identity matching to perspective taking. Its evaluation of 43 VLMs reveals that **rotation is particularly catastrophic**: mental rotation and perspective taking yield near-chance scores for most models, and 41% of models perform below chance on allocentric tasks due to systematic egocentric bias. Consistency analysis shows models that cannot maintain logical equivalences (e.g., "A left of B" = "B right of A") lack genuine spatial understanding.

## Key trends

- **Benchmarking spatial intelligence:** VSI-Bench (CVPR 2025) establishes a rigorous video-based evaluation framework with 8 task types. ViewSuite (2026) extends this to multi-turn 6-DoF view planning in real 3D scenes (ScanNet), decomposing spatial reasoning into tracking (P2V/V2P) vs. planning (IVP) and revealing a sharp gap between them.
- **Tracking vs. planning decomposition:** ViewSuite shows that tracking how actions change views degrades with rotation distance, while composing plans degrades with position distance. This reveals two distinct spatial capabilities with different difficulty drivers.
- **Self-exploration bootstraps spatial reasoning:** ViewSuite's iterative view graph distillation lifts Qwen2.5-VL-7B from 2.5% to 47.8% on interactive view planning — surpassing GPT-5.4 Pro and Gemini 3.1 Pro — by distilling valid view transitions from on-policy exploration (including failed trajectories).
- **Cognitive map probing:** Prompting models to externalize spatial representations reveals that MLLMs form accurate local models (64% accuracy for adjacent objects) but accuracy degrades with distance, suggesting a fragmented rather than unified spatial understanding. Theory of Space formalizes this as a first-class evaluation dimension, requiring agents to output cognitive maps at each exploration step, enabling fine-grained diagnosis of how representations evolve (and degrade) over time.
- **Active-passive gap as a fundamental bottleneck:** Theory of Space demonstrates that all evaluated models (GPT-5.2, Gemini-3 Pro, Claude-4.5 Sonnet, open-source models) degrade when shifting from passive reasoning to active exploration. Rule-based proxy agents reach target coverage in ~9 steps; foundation models need ≥14 steps with no accuracy improvement, revealing systematic exploration inefficiency.
- **Belief inertia under environmental change:** Theory of Space's false belief paradigm shows agents persistently retain obsolete spatial priors even after directly observing updated configurations — especially severe in vision-based models. This is a new failure mode distinct from the tracking/planning gap identified in ViewSuite.
- **Failure of linguistic prompting:** Chain-of-thought, self-consistency, and tree-of-thoughts all hurt spatial performance, indicating that spatial reasoning requires fundamentally different approaches than linguistic reasoning enhancement. SpinBench confirms this: CoT prompting shows limited and inconsistent benefits on spatial tasks.
- **Test-time compute as partial remedy:** GPT-5.4 Pro outperforms GPT-5.4 across all ViewSuite tasks, suggesting additional thinking time can partially compensate for spatial reasoning limitations.
- **Quantitative physical reasoning as a new frontier:** QuantiPhy (CVPR 2026) extends spatial evaluation to quantitative kinematic inference — estimating object size, velocity, and acceleration in real-world units from video. Even the best VLMs (~53% MRA) barely match human baselines (~56%), and counterfactual analysis confirms models rely on memorized world knowledge rather than faithfully using visual input and provided priors.
- **Progressive diagnostic decomposition:** SpinBench (ICLR 2026) introduces cognitively grounded task scaffolding — from single-object perception (identity matching, canonical view selection) through dynamic reasoning (translation, rotation) to multi-object perspective taking — enabling precise identification of where spatial cognition breaks down. Controlled variations (reference frame, symmetry, syntax, premise) provide interpretive power absent from holistic benchmarks.
- **Egocentric bias as a systematic failure mode:** SpinBench shows 41% of models score below chance on allocentric reference frame tasks, indicating not random failure but systematic misinterpretation. This complements VSI-Bench's finding that egocentric-allocentric transformation drives ~71% of errors.
- **Consistency as a diagnostic signal:** SpinBench's symmetrical and syntactic augmentations reveal that accuracy and pairwise consistency correlate strongly (r = 0.891) but diverge among top models — high consistency does not guarantee accuracy and vice versa. GPT-5 leads both; Gemini 2.5 Pro ranks 2nd accuracy but only 5th consistency.

## Open problems

- How to train models that build globally consistent spatial representations from sequential visual input — Theory of Space shows that even with correct local observations, global beliefs degrade over time
- Whether spatial-specific architectures or training objectives can close the human-model gap (VSI-Bench: 34 points; SpinBench: humans 91.2% vs. most VLMs near chance on rotation/perspective tasks)
- Why rotation is disproportionately hard: SpinBench shows mental rotation and perspective taking yield near-chance kappa for most models, even when object-relation grounding (kappa > 0.6) is relatively easy — what representational capacity is missing?
- Extending spatial reasoning evaluation to outdoor, large-scale, and multi-room environments
- Integration of explicit spatial representations (3D maps, point clouds) with MLLM reasoning
- Whether spatial reasoning can be improved through spatial pretext tasks like cognitive map prediction
- Whether self-exploration and view graph distillation can scale beyond indoor scenes and transfer across environment types
- How to move from view matching (reactive) to true mental localization (prospective) — ViewSuite shows current models rarely localize without visiting the target
- How to overcome belief inertia — Theory of Space shows agents cannot revise spatial priors after environmental changes, a distinct failure from perception or reasoning errors
- Extension of Theory of Space to multi-agent exploration, where spatial belief sharing and coordination add additional challenges
- Can VLMs move beyond memorized world knowledge to perform input-faithful quantitative physical reasoning — QuantiPhy shows models ignore altered priors and anchor to real-world magnitudes

## Contradictions and debates

- Linguistic prompting techniques (CoT, ToT) are established as beneficial for reasoning tasks generally, but VSI-Bench shows they are actively harmful for spatial reasoning, and SpinBench finds inconsistent effects — the boundary between these regimes is not well understood.
- **RL vs. distillation for spatial reasoning:** Direct RL (PPO, GRPO) fails to bootstrap view planning from near-zero reward, while view graph distillation from the same exploration succeeds dramatically. This challenges the assumption that RL alone can learn spatial planning from sparse reward.
- **Perception vs. belief instability:** Theory of Space reveals that perception is an initial bottleneck, but global spatial beliefs suffer *additional* degradation from instability over time. This suggests the problem is not merely "better vision" but fundamentally about how models integrate and maintain spatial information across steps.

## Recommended reading order

1. **[[papers/2025-vsi-bench|Thinking in Space (VSI-Bench)]]** — foundational benchmark and analysis establishing that spatial reasoning is the primary bottleneck for MLLMs
2. **[[papers/2025/2025-spinbench/2025-spinbench|SpinBench (ICLR 2026)]]** — fine-grained diagnostic decomposition of spatial reasoning into 7 progressive categories; reveals rotation as the critical failure mode and egocentric bias as systematic
3. **[[papers/2026-planning-with-views|Planning with the Views (ViewSuite)]]** — decomposes spatial reasoning into tracking vs. planning; introduces self-exploration with view graph distillation to close the planning gap
4. **[[papers/2026-theory-of-space|Theory of Space (ICLR 2026)]]** — evaluates active spatial belief construction; introduces cognitive map probing and false belief paradigm revealing belief instability and inertia

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
