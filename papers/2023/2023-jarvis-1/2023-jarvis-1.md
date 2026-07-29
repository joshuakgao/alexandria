---
title: "JARVIS-1: Open-World Multi-task Agents with Memory-Augmented Multimodal Language Models"
authors: [Zihao Wang, Shaofei Cai, Anji Liu, Yonggang Jin, Jinbing Hou, Bowei Zhang, Haowei Lin, Zhaofeng He, Zilong Zheng, Yaodong Yang, Xiaojian Ma, Yitao Liang]
year: 2023
venue: "IEEE TPAMI 2025"
tags: [embodied-ai, multi-agent]
url: "https://arxiv.org/abs/2311.05997"
date_ingested: 2026-07-19
---

# JARVIS-1: Open-World Multi-task Agents with Memory-Augmented Multimodal Language Models

![[2023-jarvis-1-thumbnail.png]]

## Research gap
Existing open-world agents built with LLM-based planners struggle with three major issues: (1) inability to perceive multimodal sensory observations for planning (LLM planners are "blind"), (2) inconsistent and inaccurate long-horizon planning requiring multi-round knowledge-intensive reasoning, and (3) lack of life-long learning capability — agents cannot progressively improve task completion as game time progresses. No prior agent could robustly complete the full Minecraft technology tree across 200+ tasks.

## Contributions
- **JARVIS-1 agent**: A memory-augmented multimodal language model (MLM) agent capable of perceiving visual observations, generating situation-aware plans, and performing embodied control across 200+ Minecraft tasks — the most general Minecraft agent at time of publication.
- **Interactive planning with self-check and self-explain**: A two-layer error correction mechanism where the MLM first simulates plan execution to catch bugs before acting (self-check), then reasons about environmental feedback to recover from execution failures (self-explain).
- **Multimodal memory for in-context life-long learning**: A key-value memory storing successful plans indexed by both task and visual observation, enabling retrieval-augmented planning that improves with experience — without any gradient updates.
- **Self-instruct and self-improve**: A mechanism where distributed JARVIS-1 agents autonomously propose tasks for exploration, collect experiences into shared memory, and progressively sharpen planning skills across parallel instances.

## Method
JARVIS-1 uses a hierarchical architecture with three components:

**Multimodal language model (MLM) planner**: Chains a multimodal foundation model (MineCLIP) with an LLM (GPT-4) to enable situation-aware planning. Visual observations are translated to text descriptions via MineCLIP keyword extraction and GPT-generated situation sentences (biome, inventory, visible entities). The planner produces a sequence of short-horizon goals dispatched to a goal-conditioned controller.

**Interactive planning**: Two error-correction mechanisms:
- *Self-check*: Before execution, the MLM simulates plan steps, predicts resulting states (primarily inventory), and verifies precondition satisfaction — catching flaws like insufficient materials before the agent enters a hard-to-recover state (e.g., underground without wood).
- *Self-explain*: During execution, environmental feedback from failures is fed back to the MLM, which reasons about error causes and produces corrected plans in a closed-loop fashion.

**Multimodal memory**: A key-value store where keys are multimodal (task + observation at planning time) and values are successfully executed plans. Query generation uses LLM reasoning to decompose new tasks into related sub-tasks for retrieval. Multiple entries can exist for the same task with different observations and plans, capturing situation-dependent planning.

**Self-improving loop**: Multiple JARVIS-1 instances run in parallel, proposing tasks via self-instruct, executing them, and saving successful experiences to a shared memory — enabling cross-task knowledge transfer (e.g., diamond pickaxe experiences help diamond axe planning).

**Controller**: Goal-conditioned low-level controller based on VPT, executing short-horizon goals via keyboard-and-mouse actions.

## Datasets & evaluation
- **200+ tasks** from the Minecraft Universe Benchmark, spanning short-horizon (e.g., ObtainCraftingTable) to long-horizon tasks (e.g., ObtainDiamondPickaxe).
- **ObtainDiamondPickaxe**: JARVIS-1 achieves 12.5% success rate, a 5x improvement over VPT (2.5%). First agent to robustly obtain diamond pickaxe and craft nearly all diamond items in the overworld.
- **Short-horizon tasks**: Near-perfect performance across basic tasks.
- **Life-long learning**: Demonstrated continuous performance improvement as game time increases and memory accumulates, with cross-task experience transfer showing significant gains on related tasks.
- **Ablations**: Confirm contributions of interactive planning (self-check + self-explain), multimodal memory retrieval, and situation-aware observation.

## Limitations
- Relies on composing existing foundation models (MineCLIP, GPT-4) rather than end-to-end training, inheriting their individual limitations.
- Visual perception pipeline (keyword extraction → sentence generation → text planning) is indirect and may lose fine-grained visual information compared to end-to-end multimodal approaches.
- Goal-conditioned controller (VPT-based) has its own failure modes that propagate to overall task success.
- Memory grows without curation — no mechanism for forgetting irrelevant or outdated experiences.
- Self-instruct task proposal quality is limited by the LLM's knowledge of Minecraft's technology tree.

## Key takeaways
- Multimodal perception is critical for open-world planning — "blind" LLM planners that ignore visual observations fail to adapt to dynamic situations (biome changes, tool breakage, day/night cycles).
- In-context life-long learning via retrieval-augmented memory provides an alternative to gradient-based learning for accumulating experience, with the key advantage that no model update is needed — experiences are leveraged purely through in-context prompting.
- Interactive planning with self-check (pre-execution verification) and self-explain (post-failure reasoning) provides two complementary layers of error correction that become increasingly important as task complexity grows.
- Cross-task experience transfer through shared memory enables positive transfer between related tasks (e.g., diamond pickaxe ↔ diamond axe), demonstrating that open-world planning benefits from breadth of experience, not just depth.
