---
title: "Theory of Mind for Multi-Agent Collaboration via Large Language Models"
authors: [Huao Li, Yu Quan Chong, Simon Stepputtis, Joseph Campbell, Dana Hughes, Michael Lewis, Katia Sycara]
year: 2023
venue: "EMNLP 2023"
tags: [theory-of-mind, multi-agent]
url: "https://arxiv.org/abs/2310.10701"
date_ingested: 2026-07-12
---

# Theory of Mind for Multi-Agent Collaboration via Large Language Models

![[2023-tom-multi-agent-collaboration-llms-thumbnail.png]]

## Research gap
While LLMs have demonstrated reasoning and planning capabilities, their ability to collaborate in multi-agent settings — particularly with Theory of Mind reasoning about partners' beliefs — remained largely unexplored. Prior ToM evaluations used static text-based tests (Sally-Anne variants), which fail to capture the dynamic belief evolution and communication that arise in interactive teamwork.

## Contributions
- Designs a multi-agent cooperative text game (bomb-defusal search and rescue) that requires coordination among three decentralized agents with partial observability.
- Evaluates LLM-based agents (GPT-4, ChatGPT) against MARL (MAPPO) and planning (CBS) baselines on collaborative task performance.
- Proposes a novel evaluation of high-order Theory of Mind (introspection, first-order, second-order) in interactive teamwork scenarios with dynamic belief states and communication.
- Introduces explicit belief state representations via prompt engineering to mitigate LLM failures in long-horizon context management, improving both task performance and ToM accuracy.

## Method
Three LLM-based agents (Alpha, Bravo, Charlie) operate in a text-based environment with 5 rooms and 5 color-coded bombs requiring sequential defusal with specific wire cutters. Each agent has partial observability (current room only) and communicates via broadcast messages. At each round, agents select an action (move, inspect, or use a tool) and send a message.

The key intervention is **explicit belief state representation**: agents are prompted to maintain and update a textual description of task-relevant beliefs (bomb locations, sequences, teammate status) after each observation. This acts as an external memory that compensates for LLMs' inability to track long-horizon context.

ToM is evaluated via three levels of inference questions posed during the task: (1) introspection — can the agent articulate its own mental state, (2) first-order ToM — can it estimate what another agent knows, (3) second-order ToM — can it infer what another agent believes about its own knowledge.

## Datasets & evaluation
Evaluation uses the custom bomb-defusal environment with randomized layouts. Key results:
- **Task performance**: GPT-4 teams achieve perfect scores (90/90); GPT-4 with belief states completes in 12.3 rounds vs. 28.3 without (compared to MAPPO's 11.0 and optimal CBS planner's 6.0). ChatGPT teams fail to complete the task.
- **ToM inference**: GPT-4 with belief states achieves the highest ToM accuracy across all levels. Introspection is easiest (>80%); second-order ToM is hardest. ChatGPT shows near-chance performance on higher-order ToM.
- **Emergent behaviors**: GPT-4 agents spontaneously exhibit leadership emergence, task allocation, and information sharing without explicit programming.
- **Systematic failures**: LLMs suffer from hallucination about task state and inability to manage long-horizon context, both partially mitigated by explicit belief representations.

## Limitations
- Small scale: only 5 rooms, 5 bombs, and 3 agents — scalability to larger teams and environments is untested.
- Fully zero-shot LLM evaluation with no fine-tuning — results are sensitive to prompt design and model version.
- ToM evaluation relies on human annotators for ground truth, with inherent ambiguity in communication-based belief attribution.
- The text-based environment abstracts away perceptual challenges of real embodied collaboration.
- Only tested with GPT-4 and ChatGPT; generalization to other LLMs is unknown.

## Key takeaways
- LLM-based agents can achieve competitive multi-agent collaboration performance in zero-shot settings, matching trained MARL agents on task completion (though not efficiency).
- Explicit belief state representations significantly improve both collaboration efficiency (2.3x fewer rounds) and ToM inference accuracy, suggesting that LLMs' ToM failures stem partly from context management limitations rather than fundamental reasoning deficits.
- Emergent collaborative behaviors (leadership, task division, information sharing) arise naturally from LLM agents without explicit coordination mechanisms — a qualitative difference from MARL agents that require reward shaping.
- Higher-order ToM (second-order beliefs about others' beliefs) remains challenging even for GPT-4, especially when communication introduces ambiguity about what information has been successfully shared.
