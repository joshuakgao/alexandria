---
topic: Reinforcement Learning
slug: reinforcement-learning
---

# Reinforcement Learning

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "reinforcement-learning")
SORT year DESC
```

## Overview

*Last updated: 2026-06-18 | Sources: 1 paper*

This topic covers reinforcement learning methodology, infrastructure, and training paradigms as they appear in the wiki's research landscape. Gymnasium (Towers et al., NeurIPS 2025) provides the foundational environment API — the maintained successor to OpenAI Gym with 18M+ downloads — standardizing how agents interact with environments via the Env/VectorEnv abstractions. Its separation of termination from truncation fixed widespread value estimation bugs across major RL libraries (Stable Baselines3, CleanRL, RLlib), and its functional API (FuncEnv) bridges theoretical POMDP formalism with practical JAX-accelerated implementations.

RL methods also appear as components in other papers in this wiki: ViewSuite uses PPO and GRPO for self-exploration in view planning, and NitroGen uses behavior cloning (a supervised alternative to RL) trained on Gymnasium-compatible environments.

## Trends

- **Infrastructure matters**: Gymnasium demonstrates that API design choices (termination vs. truncation, functional vs. object-oriented) directly impact training correctness and research reproducibility across the entire RL ecosystem.
- **RL as component, not end goal**: In the wiki's papers, RL increasingly appears as one stage in a larger pipeline — e.g., ViewSuite's PPO for exploration followed by view graph distillation, or NitroGen's behavior cloning pre-training followed by RL fine-tuning.
- **Sparse reward remains a bottleneck**: ViewSuite shows that direct RL (PPO, GRPO) fails to bootstrap from near-zero reward (~2.5% success), requiring alternative supervision (view graph distillation). This is a general challenge for RL in complex reasoning tasks.

## Open questions

- Whether hardware-accelerated functional environments (JAX/GPU) will fundamentally change RL scaling laws by removing the environment simulation bottleneck
- How to bridge the gap between standardized discrete/continuous action spaces (Gymnasium) and the richer action semantics needed for embodied agents (language-conditioned, multi-modal)
- Whether behavior cloning at internet scale (NitroGen) can eventually match or surpass RL for embodied policy learning
