---
title: "Chameleon: Episodic Memory for Long-Horizon Robotic Manipulation"
authors: [Robotic Manipulation Xinying Guo, Chenxi Jiang, Hyun Bin Kim, Ying Sun, Yang Xiao, Yuhang Han, Jianfei Yang]
year: 2026
venue: ""
tags: [embodied-ai]
url: ""
pdf: "[[2026-chameleon.pdf]]"
date_ingested: 2026-06-18
---

# Chameleon: Episodic Memory for Long-Horizon Robotic Manipulation

![[2026-chameleon-thumbnail.png]]

## Research gap

Chameleon addresses a fundamental problem in robotic manipulation that the other embodied memory papers in this topic largely ignore: **perceptual aliasing**—when identical observations at decision time require different actions because the disambiguating context was established through earlier interactions. The classic example is the shell game: identical cups look the same, but the correct grasp depends on swap history. Current systems using semantic compression (text captions, embeddings) discard the fine-grained perceptual cues needed for disambiguation, and similarity-based retrieval returns perceptually similar but decision-irrelevant episodes.

## Contributions

- Formalizes robotic memory-intensive tasks as decision problems under perceptual aliasing, where observation-level non-Markovianity requires episodic memory
- Proposes a bio-inspired hierarchical differentiable memory architecture mapping EC (encoding), HC (episodic storage with pattern separation/completion), and PFC (goal-directed working memory)
- Introduces geometry-grounded perception with ventral/dorsal streams and epipolar-constrained cross-view attention for disambiguating encoding
- Develops HoloHead: a latent imagination objective that trains the decision state to maintain and recall task-relevant memories through future-state prediction
- Creates Camo-Dataset: a real UR5e robot benchmark for memory-intensive manipulation under perceptual aliasing
- Demonstrates learned representation separation between perceptually similar but memory-distinct states

## Method

**Perception (EC-inspired)**: Two-stream architecture—ventral (DINO patch tokens for appearance) and dorsal (end-effector-anchored geometric codes including ray direction, EE proximity, and angular alignment). Geometry-biased bidirectional cross-view attention uses epipolar feasibility bias and learned unary patch biases for view fusion. FiLM-style modulation produces fused evidence tokens x_t.

**Memory (HC/PFC-inspired)**: Multimodal fusion combines visual tokens with proprioception and optional phase tokens via additive modality/view embeddings. A hierarchical differentiable memory stack operates at multiple timescales: episodic state retains long-range traces (slow, DG/CA3-inspired), working state integrates recalled context for immediate control (fast, PFC-inspired). Dynamic context modulation broadcasts the working state to all tokens at each layer. HoloHead trains the decision state h_t to predict/imagine near-future states, enforcing goal-directed recall.

**Policy**: Conditional flow matching parameterizes the velocity field for end-effector pose trajectories. During inference, an ODE solver generates the trajectory from noise in K steps, conditioned on h_t. Standard inverse kinematics controllers execute the trajectory.

## Datasets & evaluation

- Consistently outperforms Diffusion Policy, Flow Matching, and ACT across episodic recall, spatial tracking, and sequential manipulation tasks
- Representation analysis shows clear separation between perceptually similar but memory-distinct states in the learned decision state
- Pattern completion behavior observed: the memory can reconstruct task-relevant information from partial cues
- Ablation confirms all components contribute: geometry-grounded perception, episodic/working memory coupling, HoloHead imagination objective, and flow matching policy

## Limitations

- Evaluated only on tabletop manipulation with a UR5e; scalability to more complex manipulation scenarios and mobile robots is unclear
- The fully differentiable architecture requires end-to-end training on task-specific data, unlike the training-free LLM/VLM approaches (R⁴, Embodied-RAG) that generalize zero-shot
- Camo-Dataset is relatively small and focused; generalization to diverse manipulation environments untested
- The bio-inspired design is functionally motivated but not a faithful model of the neuroscience—the mapping from EC/HC/PFC to neural network layers is approximate
- Memory capacity is bounded by the hidden state dimension; how the system scales to very long manipulation sequences (hundreds of steps) is not fully characterized
- No mechanism for cross-task memory transfer; each task requires its own trained model

## Key takeaways

Inspired by the human Entorhinal Cortex–Hippocampus–Prefrontal Cortex (EC–HC–PFC) episodic memory system, Chameleon proposes a fully differentiable, end-to-end trained architecture with three stages:

**Perception** produces geometry-grounded, view-consistent patch tokens from two camera views (fixed front + hand-mounted). A ventral stream (frozen DINO encoder) captures appearance, while a dorsal stream computes end-effector-anchored geometric codes. Geometry-biased cross-view attention uses epipolar constraints to fuse views without mixing incompatible evidence.

**Memory** couples a slow episodic state with a fast working state through continuous dynamics. Spatial and temporal anchors encode evidence at multiple effective half-lives. A **HoloHead** objective trains the decision state via latent imagination—compelling it to be predictive of near-future state evolution, enforcing goal-directed recall rather than similarity-based retrieval. This mirrors CA3 pattern completion in the hippocampus.

**Policy** uses conditional flow matching (rectified flow) to generate an end-effector pose trajectory in one shot, conditioned solely on the memory readout state h_t.

The paper also introduces **Camo-Dataset**, a real-world UR5e robot dataset for episodic recall, spatial tracking, and sequential manipulation under perceptual aliasing. Chameleon consistently outperforms Diffusion Policy, Flow Matching, and ACT baselines on decision reliability and long-horizon task completion.

Chameleon represents a fundamentally different approach to embodied memory compared to [[2025-embodied-rag|Embodied-RAG]], [[2024-remembr|ReMEmbR]], [[2025-r4-retrieval-augmented-reasoning-4d|R⁴]], [[2025-mind-palace|Mind Palace]], and [[2024-snapmem|SnapMem]]. While those systems build non-parametric memory stores (vector databases, scene graphs, semantic forests, image snapshots) and use LLM/VLM pipelines for retrieval and reasoning, Chameleon uses a fully differentiable, end-to-end trained parametric memory architecture. It directly critiques the semantic compression and similarity-based retrieval paradigm shared by all other papers in this topic, arguing that these approaches discard fine-grained perceptual cues needed for disambiguation in manipulation tasks. The bio-inspired design (EC→HC→PFC) provides a neuroscience-grounded alternative to the engineering-driven architectures of the other systems. Chameleon also operates at a different task granularity—manipulation under perceptual aliasing rather than navigation or QA—highlighting that different embodied tasks may require fundamentally different memory architectures.
