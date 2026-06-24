---
title: "Worth Remembering: Surprise-Gated Robot Episodic Memory"
authors: [Nicolas Gorlo, Derek Wise, Alberto Speranzon, Luca Carlone]
year: 2026
venue: ""
tags: [embodied-ai, scene-graphs]
url: ""
pdf: "[[2026-worth-remembering.pdf]]"
date_ingested: 2026-06-18
---

# Worth Remembering: Surprise-Gated Robot Episodic Memory

![[2026-worth-remembering-thumbnail.png]]

## Research gap

This paper addresses a fundamental question in robot memory: *what is worth remembering?* Current embodied memory systems either store observations at fixed intervals (wasting budget on redundant data) or indiscriminately (accumulating thousands of irrelevant episodes). Inspired by cognitive science findings that mammalian memory formation is gated by prediction error, the authors propose Bayesian surprise as a principled mechanism for deciding when to store episodic memories.

## Contributions

- Formalization of surprise-gated episodic memory for robotics, grounding the "what and when to store" decision in Bayesian prediction error from cognitive science (CLS theory, event segmentation theory, predictive coding)
- Online causal architecture computing Bayesian surprise from V-JEPA-2 latent embeddings, storing sparse memorable episodes without per-deployment training
- Integration with DAAAM's 4D scene graph as an additional episodic layer, enabling agentic retrieval of event-specific visual memories
- ≥12% improvement on all QA metrics on OC-NaVQA and SOTA on GEBD (Kinetics-400), outperforming supervised/offline methods with an unsupervised causal approach

## Method

**V-JEPA-2 Embedding:** Each frame window (M=64 frames) is encoded by V-JEPA-2 into a pooled feature z_t ∈ R^1024. Consecutive windows share M−1 frames, so shifts in z_t track arrival of unpredictable content. V-JEPA-2 is chosen because it ignores stochastic sources of prediction error (rustling leaves) and is deployment-agnostic (trained on large-scale real-world video).

**Bayesian Surprise:** A diagonal Gaussian fitted on a sliding window (W=64) of past z-values provides a local predictive distribution. Per-frame surprise s(t) is the average absolute z-score across dimensions — equivalent (to leading order) to the KL divergence between consecutive predictive distributions. Episodes are stored at local maxima of s(t) exceeding a robust threshold: median + γ · 1.4826 · MAD(s), followed by non-maximum suppression.

**Memory Integration:** Stored episodes (8 frames each) are encoded with Perception-Encoder (PE-Core-L14) for text-image retrieval. They augment DAAAM's 4D scene graph as an additional data layer, enabling the LLM agent to retrieve visual episodes by query similarity, location, or observation time.

**Storage Efficiency:** With γ=1.0, the system stores ~1.28 episodes/minute, retaining only 1.7% of frames — compared to fixed-interval or uniform approaches that store far more while performing worse.

## Datasets & evaluation

- **OC-NaVQA:** 79.6% question accuracy (vs. 71.1% DAAAM, 76.1% DAAAM + uniform EM); 36.57m positional error (vs. 41.75m); 1.51 min temporal error (vs. 1.79 min)
- **Ablation:** Surprise-gated EM outperforms both uniform and random EM given the same memory budget; uniform/random EM help with binary questions but degrade temporal/spatial queries by adding redundant visual noise
- **GEBD (Kinetics-400):** State-of-the-art F1 score, outperforming prior supervised and offline methods with an unsupervised causal approach
- **Qualitative:** Successfully captures high-utility events (person opening door, golf cart stopping) that enable answering detail-rich queries requiring specific visual evidence

## Limitations

- V-JEPA-2 is trained for noncausal prediction; using a causal video world model (V-JEPA-2-AC) could provide a better surprise signal
- Text-image embeddings for retrieval don't capture temporal evolution of events; text-to-video embeddings could improve retrieval quality
- Episodes are stored as raw frames; compressed representations or replay-based distillation of statistical regularities across episodes is unexplored
- The Gaussian predictive model is less expressive than a learned causal prediction model; more powerful predictive families could improve surprise estimation
- Increases token usage by ~13% and requires a multimodal LLM for reasoning
- Evaluated only on outdoor robot sequences; applicability to indoor manipulation or multi-room household scenarios is untested

## Key takeaways

The approach computes surprise in the latent space of V-JEPA-2, a self-supervised video encoder trained to predict held-out spatiotemporal features. At each timestep, a sliding-window Gaussian model provides a local predictive distribution over V-JEPA-2 embeddings. Bayesian surprise — the KL divergence between consecutive predictive distributions (equivalently, a normalized z-score in the Gaussian regime) — measures how much a new observation shifts the model's expectations. Frames exceeding a robust MAD-based threshold are stored as episodic memories, with non-maximum suppression to deduplicate clustered peaks.

The surprise-gated episodes augment DAAAM's 4D scene graph spatial memory as an additional retrieval layer. On the OC-NaVQA benchmark (long-horizon outdoor robot QA, >30 min sequences), the system improves question accuracy by +12% over the base DAAAM system, while storing only 1.7% of all frames (~1.28 episodes/minute). It also achieves state-of-the-art on Generic Event Boundary Detection (Kinetics-400), outperforming supervised and offline (noncausal) methods with an unsupervised causal approach.

Worth Remembering directly extends DAAAM (Carlone group, MIT SPARK) by adding a principled episodic layer to its 4D scene graph. It addresses the "when to store" gap identified across all prior episodic memory systems — ReMEmbR, Embodied-RAG, Mind Palace, SnapMem all use fixed-interval or task-specific triggers. The V-JEPA-2 latent space choice connects to the world models literature (V-JEPA intuitive physics paper in this wiki) — the same predictive features that enable physics understanding also provide a deployment-agnostic surprise signal. The Bayesian surprise formulation draws on cognitive science (CLS, event segmentation theory, predictive coding) in a way that parallels BabyZWM's developmental learning approach. Compared to curiosity-driven RL (which uses prediction error to shape exploration policy), this work uses it to shape memory formation.
