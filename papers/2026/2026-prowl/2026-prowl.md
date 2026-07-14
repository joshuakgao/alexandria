---
title: "PROWL: Prioritized Regret-Driven Optimization for World Model Learning"
authors: [Ahmet H. Güzel, Jonathan Sadeghi, Jenny Seidenschwarz, Jeffrey Hawke, Benjamin Graham, Ilija Bogunovic]
year: 2026
tags: [world-models, reinforcement-learning]
url: "https://arxiv.org/abs/2605.18803"
date_ingested: 2026-07-05
---

# PROWL: Prioritized Regret-Driven Optimization for World Model Learning

![[2026-prowl-thumbnail.png]]

## Research gap
Action-conditioned video world models achieve strong visual realism but fail on rare, interaction-critical transitions that dominate downstream planning performance. Passive demonstration datasets systematically under-sample these high-impact regimes, and simply collecting more data does not target the model's specific failure modes. No prior work actively discovers and prioritizes world model failures through adversarial curriculum learning while ensuring discovered failures remain behaviorally realistic and learnable.

## Contributions
- **Constrained adversarial curriculum for world model training**: A co-evolved min-max framework where a KL-anchored adversarial policy actively discovers failure-inducing trajectories in the real environment, while the world model is iteratively refined on these examples. Uses direct prediction regret against ground-truth rollouts (no ensemble needed), unlike prior methods based on ensemble disagreement.
- **Prioritized Adversarial Trajectory (PAT) buffer**: Ranks trajectories using a structured scoring objective combining latent prediction error, pixel-space action fidelity (via optical flow), and learning progress — focusing training on unresolved failures while deprioritizing solved cases.
- **Co-evolutionary expansion of interaction regimes**: Demonstrates that adversarial training discovers novel composite action modes absent from both passive datasets and non-adversarial fine-tuning, expanding the world model's effective training distribution toward hard-to-learn regimes.
- **Empirical analysis of behavioral anchoring**: Characterizes the trade-off between failure discovery and reward hacking — weakly constrained adversaries (low KL penalty) degenerate into camera-thrashing shortcuts, while properly anchored adversaries expose learnable failures.

## Method
**Two-phase framework.** Phase 1 pretrains a 1.3B-parameter diffusion transformer (Wan2.1-T2V backbone) world model on passive BASALT FindCave demonstrations using chunk-level diffusion forcing (K=3 latent frames per chunk, ~0.6s). Phase 2 introduces the adversarial curriculum.

**Adversarial policy.** Initialized from pretrained VPT-2x foundation model, optimized with PPO using the world model's prediction error as terminal reward. A forward-KL penalty to the frozen VPT reference policy prevents drift into unrealistic action sequences. The policy generates trajectories in the real MineRL environment; ground-truth observations are compared against the world model's predictions to compute per-trajectory scores.

**Trajectory scoring.** Combines three complementary signals: (1) latent regret — L2 distance between predicted and ground-truth VAE latents, (2) Action-Follow Score (AFS) — optical flow discrepancy between predicted and real pixel frames via SEA-RAFT, and (3) learning progress — signed change in latent regret across buffer rescore cycles, so solved trajectories lose priority. Scores are z-normalized over the PAT buffer.

**World model update.** During Phase 2, only the action-conditioning subset (~280M of ~1.3B parameters: cross-attention layers + UMT5 action-text adapter) is updated, preserving the visual prior from pretraining. Updates use a 50/50 mixture of PAT buffer and passive data samples.

**Regime spectrum.** Two parameters define qualitatively distinct operating points: KL penalty strength (c_kl) controls behavioral anchoring, and AFS weight (λ_AFS) biases toward motion-sensitive failures. This yields: (1) unanchored regime (c_kl=0.5) — reward hacking via camera-thrashing; (2) broad-exploration regime (lam010) — widest variety of action compositions, best generalization; (3) focused-specialist regime (kl150) — tightest anchor, best on hard in-distribution cases and long-horizon prediction.

## Datasets & evaluation
**Training data:** BASALT FindCave human demonstrations for Phase 1 pretraining; adversarially generated trajectories in MineRL for Phase 2.

**Evaluation protocol:** Four evaluation settings, all using held-out data:
1. **Zero-shot generalization** — 3 BASALT tasks disjoint from training (MakeWaterfall, BuildVillageHouse, CreateVillageAnimalPen), n=300 clips.
2. **Cross-buffer adversarial transfer** — each checkpoint evaluated on PAT buffers from other adversaries (off-diagonal), n=384 trajectories.
3. **In-distribution stability** — held-out FindCave clips, n=64.
4. **Long-horizon compounding** — 18-chunk autoregressive rollout to 10.8s, n=30 clips.

**Key results:**
- Held-out BASALT tasks: PROWL (lam010) reduces latent regret by 3.5%, AFS-EPE by 12.6%, LPIPS by 2.7% vs. Phase 1; gains widen on hardest tertile (up to -20.9% AFS-EPE).
- vs. matched-compute baseline (VPT-frozen Phase 2): -2.5% latent regret, -3.9% AFS-EPE — isolating the contribution of adversarial discovery from additional fine-tuning compute.
- Cross-buffer adversarial: PROWL (kl150) achieves up to 8.9% AFS-EPE reduction over matched-compute baseline.
- 27 strictly novel composite action modes: -5.5%/-11.5%/-3.4% (latent/AFS/LPIPS) vs. Phase 1.
- Long-horizon: local-dynamics improvements compound through autoregressive rollout; matched-compute baseline alone barely improves over Phase 1.
- Weakly constrained adversary (c_kl=0.5) degenerates into camera-thrashing reward hacking, confirming that behavioral anchoring is essential.

## Limitations
- Single-seed runs per configuration due to compute cost — multi-seed variance estimation is future work.
- Evaluated only in MineRL/BASALT; cross-environment generalization untested.
- Long-horizon evaluation lies outside the training horizon where PROWL applies adversarial pressure — improvements are indirect compounding of short-horizon gains.
- Phase 2 freezes the spatial-temporal backbone and updates only action-conditioning parameters; whether full fine-tuning or larger backbones would change the regime dynamics is unexplored.
- The adversarial policy requires access to the real environment for ground-truth rollouts, limiting applicability to settings where environment interaction is available.

## Key takeaways
- World models benefit not only from larger datasets but from selectively generating informative training data — adversarial discovery targets the specific failure modes that passive data systematically misses.
- Prediction error alone is not a sufficient adversarial reward signal; behavioral anchoring via KL regularization is critical to ensure discovered failures are learnable rather than degenerate (camera-thrashing reward hacking).
- The PAT buffer's learning-progress signal creates an adaptive curriculum: trajectories that the model has learned to handle automatically lose priority, maintaining pressure on unresolved weaknesses.
- Short-horizon action-fidelity improvements compound through autoregressive rollout into long-horizon gains, confirming that local dynamics accuracy is foundational to long-term robustness.
- The framework inverts the typical RL-world-model relationship: instead of training a policy inside a world model, it trains a policy to break the world model so the world model can improve.
