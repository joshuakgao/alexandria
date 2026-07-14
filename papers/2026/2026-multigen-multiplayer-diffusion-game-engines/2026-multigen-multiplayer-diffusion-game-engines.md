---
title: "MultiGen: Level-Design for Editable Multiplayer Worlds in Diffusion Game Engines"
authors: [Ryan Po, David Junhao Zhang, Amir Hertz, Gordon Wetzstein, Neal Wadhwa, Nataniel Ruiz]
year: 2026
tags: [world-models, image-video-generation, multi-agent]
url: "https://arxiv.org/abs/2603.06679"
date_ingested: 2026-07-08
---

# MultiGen: Level-Design for Editable Multiplayer Worlds in Diffusion Game Engines

![[2026-multigen-multiplayer-diffusion-game-engines-thumbnail.png]]

## Research gap
Existing diffusion game engines (GameNGen, Genie, Oasis) operate as single-user next-frame predictors with implicit state stored in a finite window of recent frames. This design makes it difficult to (1) give users direct control over the environment structure (level design), since layout must be inferred from visual history, and (2) support multiplayer interaction, since there is no shared state that multiple agents can read from and write to. Long rollouts drift from the intended layout as structural cues fall out of the context window.

## Contributions
- An explicit external memory formulation for diffusion world models that persists beyond the model's context window, storing map geometry and player poses as structured state.
- A modular architecture decomposing the diffusion game engine into three specialized modules: Memory (map geometry + player poses), Observation (next-frame generation conditioned on memory readouts), and Dynamics (pose update from actions and diffusion features).
- Level-conditioned gameplay generation: users author coarse 2D level layouts (vertices and wall segments) that anchor generation over long rollouts via ray-traced disparity conditioning.
- Real-time multiplayer interaction: multiple players share the same external memory, each running independent Observation/Dynamics instances. Cross-player interactions (visibility, combat, death/respawn) emerge from the shared state without multi-view training data.

## Method
The system decomposes interactive generation into three modules operating on an explicit state S_t = (M, p_t, o_{t-L+1:t}), where M is the static level map, p_t is the player pose, and o_{t-L+1:t} is an L-frame visual context.

**Memory Module**: Maintains a 2D map M defined as vertices and line segments (walls), plus the evolving player pose (x, y, theta). The map is time-invariant and provides a persistent geometric reference. At each timestep, ray-tracing produces a 1D depth/disparity signal from the current pose, which is expanded to a spatial tensor and concatenated as input channels to the observation model.

**Observation Module**: A diffusion UNet (velocity parameterization) generates the next frame conditioned on the L-frame visual context, the ray-traced disparity from memory, and the action (injected via cross-attention). Noised-context training (corrupting context frames with random Gaussian noise during training) reduces train-test mismatch for autoregressive rollouts. History guidance at inference uses clean context for the conditional branch and noised context for the unconditional branch.

**Dynamics Module**: A lightweight transformer encoder predicts incremental pose updates from the current pose, action, disparity signal, and pooled intermediate UNet features. Supervised with L2 loss on translation and wrapped-angle error on orientation.

**Multiplayer**: All players share the same Memory (map + all player poses). Each player runs independent Observation and Dynamics instances that read from and write to the shared state. One player's actions update the shared state, affecting what other players observe. This scales to arbitrary player counts without changing the model — trained entirely on single-player data.

Training uses 10M+ frames from procedurally generated Doom maps (100 maps for level design, 1 map for multiplayer deathmatch) with a pre-trained Doom agent for data collection. Runs at ~20 FPS on a single A100 per player.

## Datasets & evaluation
- **Training data**: 10M+ gameplay frames from ViZDoom with procedurally generated maps (Obsidian generator), recorded from a pre-trained agent.
- **Baselines**: GameNGen (implicit state, no external memory), ControlNet (map-conditioned), IP-Adapter (map-conditioned), split-screen joint model (for multiplayer).
- **Level design evaluation**: SSIM/PSNR/LPIPS against ground truth over 256-step rollouts. MultiGen outperforms all baselines, with the largest gains in later rollout segments (steps 128–256) where memory-free baselines drift from the intended layout (LPIPS 0.505 vs. 0.562 for GameNGen at steps 128–256).
- **Multiplayer evaluation**: Opponent-presence detection accuracy via VLM judge. MultiGen achieves 75.38% accuracy vs. 65.31% for split-screen baseline. Recall (detecting opponents when they should be visible) is 65.07% vs. 44.59% — the key metric for cross-player consistency.
- **Context ablation**: Increasing context frames L from 2 to 32 consistently improves fidelity (SSIM 0.709 → 0.789).
- **Three-player demonstration**: Consistent views generated for three simultaneous players despite training only on single-player data.

## Limitations
- Scene properties not represented in the map M (textures, small objects) are not explicitly preserved when revisiting regions, leading to appearance inconsistencies.
- Dynamics model accumulates small pose errors over long rollouts, though actions remain aligned with plausible motion.
- Visual appearance is bounded by the VizDoom training distribution and may not generalize to other visual styles.
- Evaluated only on Doom — generalization to other game environments or open-world settings is not demonstrated.
- The map representation (2D vertices and line segments) is specific to Doom-style environments; richer environments would require more expressive memory representations.

## Key takeaways
- Explicit external memory is the missing primitive for diffusion game engines — it simultaneously enables level-design control (users edit the memory directly) and multiplayer interaction (agents share the memory). Both capabilities emerge from the same architectural choice.
- Modular decomposition (Memory/Observation/Dynamics) is more effective than monolithic next-frame prediction for long-horizon interactive generation. The Memory module provides persistent geometric reference that does not degrade with context length, while the Dynamics module enables state progression without burdening the diffusion model.
- Multiplayer emerges from single-player training: because each model instance only generates its own first-person view conditioned on shared memory, no multi-view training data is needed. Cross-player consistency arises from the shared state rather than from architectural coupling.
- The system runs at ~20 FPS per player on A100, demonstrating that memory-augmented diffusion game engines can achieve real-time interactive rates with multiple simultaneous players.
