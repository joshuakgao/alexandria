---
title: "NitroGen: An Open Foundation Model for Generalist Gaming Agents"
authors: [Loïc Magne, Anas Awadalla, Guanzhi Wang, Yinzhen Xu, Joshua Belofsky, Fengyuan Hu, Joohwan Kim, Ludwig Schmidt, Georgia Gkioxari, Jan Kautz, Yisong Yue, Yejin Choi, Yuke Zhu, Linxi Fan]
year: 2026
venue: "CVPR 2026"
tags: [agentic-mllms, world-models, embodied-ai]
url: "https://arxiv.org/abs/2601.02427"
pdf: "[[2026-nitrogen.pdf]]"
date_ingested: 2026-06-18
---

# NitroGen: An Open Foundation Model for Generalist Gaming Agents

![[2026-nitrogen-thumbnail.png]]

## Research gap
Building generalist embodied agents that operate across diverse environments has been limited by the lack of large, diverse, action-labeled datasets. Prior approaches are either narrow (RL trained per-game), require expensive manual demonstrations (behavior cloning on small datasets), or depend on hand-crafted APIs and structured game state access (LLM-based agents). No open-source framework existed for training and evaluating multi-game vision-action policies at internet scale.

## Contributions
- **Internet-scale video-action dataset**: 40,000 hours of gameplay across 1,000+ games, with actions automatically extracted from on-screen gamepad overlay displays in publicly available videos — eliminating the need for costly manual demonstrations.
- **Action extraction pipeline**: A three-stage system (SIFT/XFeat template matching → SegFormer-based gamepad parsing → quality filtering) that achieves 0.84 R² for joystick positions and 0.96 button accuracy across controller families.
- **Universal simulator**: A Gymnasium API wrapper that can wrap any commercial game for programmatic control by intercepting the game engine's system clock, enabling frame-by-frame interaction without modifying game code.
- **Multi-game evaluation suite**: 30 tasks across 10 commercial games (5 2D, 5 3D) covering combat, navigation, and game-specific mechanics.
- **NitroGen foundation model**: A vision-action transformer (SigLIP encoder + diffusion transformer action head) trained via large-scale behavior cloning. Achieves non-trivial success across diverse games zero-shot, and provides up to 52% relative improvement when fine-tuned on unseen games vs. training from scratch.

## Method
**Dataset construction:**
- Source: publicly available gameplay videos featuring input overlay software that displays real-time gamepad inputs. 71,000 hours raw → 40,000 hours after quality filtering.
- Template matching: ~300 controller templates matched via SIFT + XFeat keypoints (25 sampled frames per video, minimum 20 inliers).
- Action parsing: Fine-tuned SegFormer on 8M synthetic frames with randomized overlay conditions. Processes consecutive frame pairs. Joystick positions on 11×11 discrete grid; binary button states.
- Quality filtering: Discard segments where <50% of timesteps have non-zero actions (retains 55% of data).

**Architecture:**
- Vision encoder: SigLIP 2 ViT producing 256 tokens per 256×256 RGB frame.
- Action head: Diffusion Transformer (DiT) generating 16-action chunks via flow matching. Noisy action chunks → MLP → action tokens → DiT blocks (self-attention + cross-attention over frame tokens) → MLP decoder → continuous action vectors.
- Single context frame (multi-frame showed no benefit). Unified 20-dimensional action space: 16-dim binary buttons + 4-dim continuous joysticks.

**Training:**
- Standard conditional flow-matching objective on 16-action chunks. AdamW with warmup-stable-decay schedule (lr=0.0001). EMA weights (decay 0.9999) consistently outperform non-EMA.
- Image augmentations: random brightness/contrast/saturation/hue, rotation (±5°), random crops.
- On-screen controller masked during training to prevent shortcut learning.

## Datasets & evaluation
- **Pre-training**: 40,000 hours, 1,000+ games, 38,739 videos, 818 content creators. Genre distribution: Action-RPG (34.9%), Platformer (18.4%), Action-Adventure (9.2%), plus roguelike, sports, racing, etc.
- **Evaluation suite**: 10 games × 3 tasks each = 30 tasks. 11 combat, 10 navigation, 9 game-specific. Success rates measured via human evaluation.
- **Zero-shot results**: Non-trivial success across 2D side-scrollers, 2D top-down roguelikes, and 3D games. Combat ~50-61%, navigation ~44-56%, game-specific ~38-62% depending on visual style.
- **Transfer learning**: Fine-tuning from NitroGen weights vs. training from scratch on held-out games: +10% relative improvement on isometric roguelike, +25% on 3D action-RPG. Combat tasks benefit most (+52% relative), navigation +25%, game-specific tasks show marginal gains (+5%).
- **Scaling**: Task completion scales with data quantity (60h → 240h shows consistent improvement for both fine-tuned and from-scratch).

## Limitations
- System-1 reactive model only — no long-horizon planning, no language conditioning, no explicit reasoning. Cannot follow instructions or compose complex strategies.
- Dataset biased toward action games played with gamepads; strategy, simulation, and keyboard-only games underrepresented.
- Action extraction introduces noise: overlay software delays, video compression artifacts, variable controller configurations across players.
- Universal simulator relies on system clock interception — may not work with all game engines; real-time deployment not yet addressed.
- Game-specific mechanics transfer poorly compared to generic skills (combat, navigation), suggesting the model learns transferable motor primitives but not game-specific semantics.

## Key takeaways
- **Internet-scale data from natural sources can bootstrap embodied policies.** The key insight is that input overlay software — originally a niche speedrunning tool — provides free action labels at massive scale. Despite significant noise (overlay delays, compression artifacts, variable controller configs), large-scale training yields robust multi-game policies.
- **Behavior cloning at scale produces transferable motor primitives.** Pre-training transfers best for generic gameplay patterns (combat, navigation) and worst for game-specific mechanics, suggesting the model learns reusable sensorimotor skills rather than game-specific strategies.
- **Single-frame context suffices for reactive gameplay.** Multi-frame input showed no benefit, implying that action games provide sufficient context in a single frame for reactive control — a stark contrast to embodied QA/navigation tasks that require temporal memory.
- **Flow matching for action generation.** Using a diffusion transformer to generate action chunks (rather than predicting single actions) improves temporal consistency, echoing similar findings in robotics (π0, GR00T N1).
