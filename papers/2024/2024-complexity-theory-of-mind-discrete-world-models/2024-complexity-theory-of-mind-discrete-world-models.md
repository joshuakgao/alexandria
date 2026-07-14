---
title: "A Notion of Complexity for Theory of Mind via Discrete World Models"
authors: [X. Angelo Huang, Emanuele La Malfa, Samuele Marro, Andrea Asperti, Anthony G. Cohn, Michael Wooldridge]
year: 2024
venue: "EMNLP 2024 Findings"
tags: [theory-of-mind]
url: "https://arxiv.org/abs/2406.11911"
date_ingested: 2026-07-12
---

# A Notion of Complexity for Theory of Mind via Discrete World Models

![[2024-complexity-theory-of-mind-discrete-world-models-thumbnail.png]]

## Research gap
ToM benchmarks for LLMs vary widely in difficulty, but there is no formal framework to quantify or compare their complexity. Without a principled measure, it is unclear whether LLM failures reflect genuine ToM limitations or simply task-specific confounds. Additionally, existing prompting techniques (CoT, ToT) do not explicitly leverage the state-tracking structure inherent in ToM problems.

## Contributions
- Proposes a formal complexity framework for ToM tasks, inspired by cognitive load theory, that quantifies problem difficulty as the number of state events sufficient to track for a correct answer.
- Introduces the distinction between stateful complexity (states that must be tracked for the target object/belief) and stateless complexity (spurious states designed to distract).
- Introduces Discrete World Models (DWM), a prompting technique that makes implicit state transitions explicit by splitting ToM narratives into sequential state events and prompting the LLM to describe each state.
- Characterizes the complexity of five standard ToM benchmarks (ToMi, MindGames, Adv-CSFB, SocialIQA, FANToM) and shows DWM outperforms CoT and ToT on problems with informative state spaces.

## Method
The complexity framework defines a ToM task's difficulty via:
1. **State events**: interactions in the narrative that modify the configuration of a tracked object (including k-th order beliefs about that object).
2. **Statefulness**: the number of distinct, non-mergeable state events for the target object — states that cannot be collapsed because they introduce new information (e.g., partial observability changes an agent's belief).
3. **Statelessness**: states involving irrelevant objects that serve as distractors.
4. **Total complexity**: combines stateful and discounted stateless components (Eq. 2).

The **DWM prompting technique** operationalizes this framework: given a ToM narrative, it segments the text into state events, prompts the LLM to describe the environment state after each event (tracking object positions and agent beliefs), then poses the final question. This makes implicit state transitions explicit without requiring external knowledge or fine-tuning.

## Datasets & evaluation
Tested on five ToM benchmarks with GPT-3.5-Turbo, GPT-4, LLaMA3-70B, and Mixtral 8x7B:
- **ToMi**: DWM improves performance over CoT and ToT, particularly on higher-order belief questions. Evidence of memorization found (training examples retrievable word-for-word) but memorization does not strongly correlate with performance drops.
- **MindGames**: DWM shows strongest gains on problems with high statefulness (many belief-relevant state transitions).
- **Adv-CSFB**: Adversarially constructed false-belief tasks; DWM helps but gains are smaller, suggesting adversarial distractors remain challenging.
- **SocialIQA**: Commonsense social reasoning; DWM provides minimal benefit — consistent with the framework's prediction that low-statefulness problems don't benefit from explicit state tracking.
- **FANToM**: Multi-party conversation ToM; DWM improves performance where tracking who-heard-what is complex.

Key finding: DWM's benefit correlates with a benchmark's statefulness — it helps most when many state transitions must be tracked, and least when the task is primarily commonsense inference rather than state tracking.

## Limitations
- The complexity framework applies most naturally to narrative-based ToM tasks with discrete events; less clear how to extend to continuous or embodied ToM settings.
- DWM increases token usage (more verbose prompts); cost-benefit tradeoff may not favor it for simple tasks.
- Complexity measure is computed analytically per-problem — no automated method to estimate complexity of arbitrary natural-language ToM problems.
- Only tested on text-based benchmarks; does not address multimodal or interactive ToM.

## Key takeaways
- ToM task difficulty can be formally quantified as the number of state events that must be tracked, providing a principled way to compare benchmarks and predict where LLMs will struggle.
- Making state transitions explicit via DWM prompting compensates for LLMs' implicit state-tracking limitations — consistent with Li et al. (EMNLP 2023)'s finding that explicit belief representations improve ToM performance.
- The framework reveals that many "ToM failures" in LLMs may reflect state-tracking bottlenecks rather than fundamental social reasoning deficits — problems with low statefulness (SocialIQA) show minimal benefit from DWM, while high-statefulness problems (ToMi, MindGames) show large gains.
- Memorization of benchmark training data is detectable but does not strongly predict ToM performance, suggesting LLMs are not simply pattern-matching on memorized examples.
