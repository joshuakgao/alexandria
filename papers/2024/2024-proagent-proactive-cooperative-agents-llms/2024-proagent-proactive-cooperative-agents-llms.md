---
title: "ProAgent: Building Proactive Cooperative Agents with Large Language Models"
authors: [Ceyao Zhang, Kaijie Yang, Siyi Hu, Zihao Wang, Guanghe Li, Yihang Sun, Cheng Zhang, Zhaowei Zhang, Anji Liu, Song-Chun Zhu, Xiaojun Chang, Junge Zhang, Feng Yin, Yitao Liang, Yaodong Yang]
year: 2024
venue: "AAAI 2024"
tags: [multi-agent, theory-of-mind]
url: "https://arxiv.org/abs/2308.11339"
date_ingested: 2026-07-12
---

# ProAgent: Building Proactive Cooperative Agents with Large Language Models

![[2024-proagent-proactive-cooperative-agents-llms-thumbnail.png]]

## Research gap
Learning-based cooperative agents (self-play, population-based training) depend heavily on the diversity of training partners, limiting their adaptability to unfamiliar teammates in zero-shot coordination. These methods also lack interpretability — their coordination strategies are opaque. Prior LLM-based agent work focused on single-agent settings, leaving the potential of LLMs for multi-agent cooperation largely unexplored.

## Contributions
- Introduces ProAgent, a modular LLM-based framework for proactive cooperative behavior that infers teammate intentions from observations and dynamically adapts its own behavior.
- Proposes a Belief Correction mechanism that updates predicted teammate intentions by comparing them against actual observed behavior, closing the loop between prediction and reality.
- Demonstrates that ProAgent outperforms five self-play and population-based training methods when cooperating with AI agents in Overcooked-AI, and exceeds state-of-the-art by >10% with human proxy models.
- Provides a fully interpretable coordination pipeline — every reasoning step (analysis, intention inference, skill selection) is expressed in natural language.

## Method
ProAgent consists of five components:

1. **Knowledge Library & State Grounding**: Task rules, skill definitions, and demonstrations are provided as structured prompts. Raw environment state (grid layout, player positions, object states) is translated into natural language descriptions.

2. **Planner**: Uses Chain-of-Thought reasoning to analyze the current scene, predict the teammate's intention, and select a high-level skill. Planning considers both the agent's own goals and the inferred teammate behavior.

3. **Belief Correction**: After the teammate acts, ProAgent compares the predicted intention against the actual observed behavior. If they diverge, the belief is updated, and subsequent planning incorporates the corrected understanding. This iterative correction improves prediction accuracy over time.

4. **Verificator**: Validates whether the planned skill is executable given current preconditions. If not, it triggers a re-plan loop with detailed failure analysis (preconditions check, double-check, error conclusion).

5. **Controller & Memory**: The Controller decomposes high-level skills into low-level atomic actions. The Memory module stores trajectory history (states, analyses, beliefs, skills) using a FIFO buffer with recent-K retrieval.

## Datasets & evaluation
Evaluated on five Overcooked-AI layouts with varying difficulty:
- **vs. AI agents**: ProAgent outperforms Self-Play (SP), Population-Based Training (PBT), Fictitious Co-Play (FCP), Maximum Entropy Population-Based Training (MEP), and Cooperative Open-ended Learning (COLE) across most layouts.
- **vs. human proxy models**: ProAgent achieves >10% average improvement over the state-of-the-art, demonstrating stronger zero-shot coordination with human-like partners.
- **Ablation studies**: Removing Belief Correction degrades performance, confirming that iterative intention refinement is critical. Removing the analysis step (direct planning without CoT) also hurts, validating the Chain-of-Thought approach.
- ProAgent shows a preference for cooperating with rational teammates — performance improves more with human proxy models than with suboptimal AI agents, suggesting it actively leverages teammate predictability.

## Limitations
- Only tested in Overcooked-AI — a 2D gridworld with a small discrete action space. Scalability to complex, continuous, or high-dimensional environments is undemonstrated.
- Relies on accurate state grounding (symbolic state → language); environments without clean symbolic state representations would require additional perception modules.
- LLM inference latency makes real-time coordination impractical for fast-paced environments.
- Two-player cooperation only — scaling to larger teams introduces communication and coordination challenges not addressed here.
- The Knowledge Library and state grounding templates are hand-crafted per environment.

## Key takeaways
- LLM-based agents can outperform trained MARL methods at zero-shot coordination by leveraging common-sense reasoning and explicit intention inference — without any task-specific training or fine-tuning.
- The Belief Correction mechanism is a lightweight but effective form of online Theory of Mind: predict teammate intentions, observe actual behavior, update beliefs. This mirrors the ToMnet's character/mental-state separation but operates through natural language rather than learned embeddings.
- ProAgent's success with human proxy models (>10% over SOTA) validates the hypothesis that proactive intention modeling is more effective than reactive coordination for human-AI teaming — directly relevant to the partner modeling problem identified by Carroll et al.
- Modularity and interpretability are practical advantages of the LLM-based approach: every coordination decision can be inspected and understood, unlike black-box MARL policies.
