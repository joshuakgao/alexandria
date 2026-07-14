---
topic: Theory of Mind
slug: theory-of-mind
---

# Theory of Mind

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "theory-of-mind")
SORT year DESC
```

## Overview
Theory of Mind (ToM) in AI research refers to the ability of artificial agents to model, predict, and reason about the mental states of other agents — including their beliefs, desires, intentions, and knowledge. Inspired by the cognitive science concept describing humans' capacity to attribute mental states to others, machine ToM aims to build systems that can understand and anticipate the behavior of other agents without direct access to their internal representations.

## Trends
- Shift from hand-crafted Bayesian models of agent reasoning toward learned, neural approaches that acquire ToM capabilities through meta-learning — ToMnet (Rabinowitz et al., ICML 2018) demonstrated that a neural observer can learn to model agents from behavior alone, performing few-shot inverse RL and passing false-belief tests without explicit programming.
- Growing interest in using ToM for multi-agent coordination, human-AI interaction, and interpretability of AI systems.
- Integration of ToM reasoning into embodied agents that must collaborate or compete with others in complex environments.
- **Grounding ToM in situated, multimodal interaction**: MindCraft (Bara et al., EMNLP 2021) moves ToM from abstract agent modeling to situated collaborative dialogue, showing that visual observation of a shared environment is as important as language for maintaining common ground about task completion, while language becomes critical for inferring non-observable mental states (partner goals and knowledge).
- **Self-reported vs. inferred belief states**: MindCraft introduces self-reported belief annotations during interaction as ground truth, contrasting with ToMnet's approach of inferring mental states purely from behavioral observation — raising the question of what constitutes the right supervision signal for ToM training.
- **LLMs as zero-shot ToM reasoners**: Li et al. (EMNLP 2023) show that GPT-4 agents exhibit emergent ToM capabilities in interactive multi-agent collaboration — performing introspection, first-order, and second-order belief inference during a cooperative bomb-defusal task. Explicit belief state representations via prompt engineering significantly improve both ToM accuracy and task efficiency, suggesting LLM ToM failures stem partly from context management rather than fundamental reasoning deficits. This shifts the ToM paradigm from training specialized models (ToMnet) to prompting general-purpose LLMs.
- **Organizational structure as implicit ToM**: Guo et al. (2024) show that imposing hierarchical organization on LLM agent teams channels information flow and reduces the coordination burden that arises from agents' inability to model each other's states — leadership structure substitutes for explicit ToM by making communication patterns predictable. Their Criticize-Reflect framework discovers novel organizational prompts automatically, suggesting that meta-level reasoning about team dynamics can compensate for individual agents' ToM limitations.
- **Formalizing ToM complexity via state tracking**: Huang et al. (EMNLP 2024 Findings) propose that ToM task difficulty is quantifiable as the number of state events that must be tracked, drawing on cognitive load theory. Their Discrete World Models (DWM) prompting technique makes state transitions explicit, yielding the largest gains on high-statefulness benchmarks (ToMi, MindGames) and minimal gains on low-statefulness tasks (SocialIQA). This provides a principled explanation for why explicit belief representations (Li et al.) help: ToM failures in LLMs are often state-tracking bottlenecks rather than social reasoning deficits.
- **Proactive intention inference with belief correction**: ProAgent (Zhang et al., AAAI 2024) operationalizes ToM for cooperative tasks by having an LLM agent predict teammate intentions, then correcting beliefs by comparing predictions against observed behavior. This lightweight online ToM — predict, observe, update — outperforms trained MARL methods (SP, PBT, FCP) at zero-shot coordination in Overcooked-AI, demonstrating that LLM common-sense reasoning can substitute for learned coordination when paired with explicit belief tracking.

## Open questions
- How to scale learned ToM to high-dimensional, real-world environments beyond gridworlds and blocks worlds.
- Whether learned ToM models can generalize across fundamentally different agent architectures and domains.
- How to ground machine ToM in ways that align with human intuitions about mental states — MindCraft shows that even human partners struggle to infer each other's current goals, suggesting fundamental limits on observational ToM.
- The role of ToM in enabling safe and aligned AI systems that must model human intentions and values.
- Whether agents should passively infer partner mental states (ToMnet) or actively probe through dialogue (as MindCraft's analysis suggests is necessary for goal inference) — and how to integrate both capabilities.
- How asymmetric knowledge and skills affect the difficulty of ToM modeling and the communication strategies required to maintain common ground.
- Whether LLMs' ToM capabilities are genuine reasoning or pattern matching on linguistic cues — Huang et al.'s complexity framework suggests many failures are state-tracking bottlenecks (fixable by DWM prompting), but does not resolve whether LLMs truly model others' mental states vs. exploiting surface heuristics on low-statefulness tasks.
- How to scale LLM-based multi-agent ToM beyond small teams (3 agents) and simple environments to settings with many agents and complex partial observability.
