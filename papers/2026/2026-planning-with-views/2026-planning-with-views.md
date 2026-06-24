---
title: "Planning with the Views"
authors: [Kangrui Wang, Linjie Li, Zhengyuan Yang, Shiqi Chen, Zihan Wang, Li Fei-Fei, Jiajun Wu, Leonidas Guibas, Lijuan Wang, Manling Li]
year: 2026
venue: ""
tags: [spatial-reasoning, vision-language-models, embodied-ai, 3d-scene-understanding, world-models]
url: "https://arxiv.org/abs/2605.29563"
pdf: "[[2026-planning-with-views.pdf]]"
date_ingested: 2026-06-18
---

# Planning with the Views

![[2026-planning-with-views-thumbnail.png]]

## Research gap
Frontier VLMs can track how individual camera actions change a view (local view transitions), but cannot compose multi-step plans to localize an unseen target view in 3D. Prior view-reasoning benchmarks are limited to 2D image regions, single-step decisions, synthetic scenes, or restricted degrees of freedom. No benchmark exists for multi-turn view planning with full 6-DoF camera control in real 3D scenes.

## Contributions
- **ViewSuite benchmark**: A view-planning environment with full 6-DoF camera control on 286 real ScanNet indoor scenes, yielding ~55K view pairs and ~165K task instances across three diagnostic tasks (Path-to-View, View-to-Path, Interactive View Planning).
- **Planning gap diagnosis**: Evaluation of 13 frontier VLMs reveals that models can track local view transitions (~70% on short-horizon P2V/V2P) but collapse on multi-turn planning (best model reaches only 21.3% on IVP, most below 10%). Over 90% of successful IVP runs occur only after the model visually encounters the target view — models succeed by view matching, not mental localization.
- **Self-exploration with view graph distillation**: An iterative training framework that constructs a view graph from on-policy exploration trajectories and distills it into supervised demonstrations via task reformulation. Every trajectory (including failures) contributes valid view transitions.
- **SOTA results**: Lifts Qwen2.5-VL-7B from 2.5% to 47.8% on IVP, surpassing GPT-5.4 Pro (19.9%) and Gemini 3.1 Pro (21.3%). Learned spatial priors transfer to other view-understanding tasks.

## Method
**Benchmark design (ViewSuite):**
- Three tasks decomposing view planning: Path-to-View (P2V, predict resulting view from action sequence), View-to-Path (V2P, infer action sequence from two views), and Interactive View Planning (IVP, compose multi-turn plans to localize unseen target view and submit 6-DoF pose estimate).
- 12 discretized 6-DoF actions (6 translations, 6 rotations) with step sizes of 0.5m / 30°. Rendered using Open3D point clouds from ScanNet.
- Success thresholds calibrated against human judgments via alignment study.

**Self-exploration with view graph distillation:**
- Iterative two-stage training alternating self-exploration (PPO with sparse outcome reward) and view graph distillation (SFT on reformulated demonstrations).
- **View graph**: Nodes are viewpoints with rendered views; directed edges store actions. Built incrementally from on-policy trajectories. Nodes/edges deduplicated by viewpoint similarity.
- **Task reformulation**: Any path in the view graph becomes a valid IVP demonstration — the end node becomes the target view, the start node the initial view, and the action chain the target sequence. Generates dense supervision from overwhelmingly unsuccessful exploration.
- Three supervision types: multi-turn view planning, view-difference estimation, and multiple-choice view-difference estimation.
- Four iterations: first three alternate 60-step exploration with 3 epochs distillation; final iteration runs exploration to convergence.

## Datasets & evaluation
- **ViewSuite-5K**: ~5K view pairs × 3 tasks = ~15K instances (test: 530 pairs × 3 = 1,590 instances) across 286 ScanNet scenes. Train/dev/test split at 8:1:1 by scene.
- **Evaluation**: P2V/V2P use accuracy; IVP uses success rate based on pose distance (translation ≤ 0.5m and rotation ≤ 30°).
- **Key results**: Best frontier model (Gemini 3.1 Pro) reaches 21.3% IVP; all open-weight models below 5%. The trained Qwen2.5-VL-7B reaches 47.8%. On Qwen3-VL-8B, reaches 32.5%.
- **Ablations**: Direct PPO (3.2%), GRPO with filtering (5.2%), success-only bootstrapping (6.2%), random-graph distillation (13.0%) — all far below the full framework (47.8%), confirming the importance of on-policy graph construction and task reformulation from failures.
- Neither increased turn budget (20→30 turns) nor higher-fidelity rendering (Gaussian splatting) closes the planning gap, placing the bottleneck in reasoning, not perception or exploration horizon.

## Limitations
- ViewSuite is limited to indoor ScanNet scenes; generalization to outdoor or large-scale environments is untested.
- Point-cloud rendering provides sparse, noisy visuals (though Gaussian splatting rendering shows only marginal IVP improvement, suggesting this isn't the bottleneck).
- The trained model still relies heavily on view matching — whether the learned spatial priors truly enable prospective mental localization (predicting camera pose without visiting the target) is unclear.
- Evaluation uses a single step-size discretization (0.5m / 30°); sensitivity to finer or coarser granularities is not explored.

## Key takeaways
- **Prospective spatial reasoning is a distinct, harder capability than view tracking.** Even with a global top-down map, frontier VLMs cannot anchor egocentric views onto it, mentally simulate camera actions, or localize a target view before seeing it. The planning gap is cognitive, not perceptual.
- **Failed trajectories contain valid supervision.** The view graph insight — that every trajectory traces valid view transitions regardless of task success — enables bootstrapping from near-zero reward. This is a general principle for sparse-reward environments where the environment's structure can be learned from any interaction.
- **On-policy graph construction is critical.** Random-trajectory graphs cover regions the model rarely visits during evaluation, yielding poor transfer. The graph must grow with the policy, echoing DAgger's on-policy advantage.
- **Test-time compute helps spatial reasoning.** GPT-5.4 Pro (believed to be test-time-scaled GPT-5.4) meaningfully outperforms GPT-5.4 on all ViewSuite tasks, suggesting additional thinking time can partially compensate for the planning gap.
