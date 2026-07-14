---
title: "Collaborating with Humans without Human Data"
authors: [DJ Strouse, Kevin McKee, Matt Botvinick, Edward Hughes, Richard Everett]
year: 2021
venue: "NeurIPS 2021"
tags: [multi-agent, reinforcement-learning, human-ai-collaboration]
url: "https://arxiv.org/abs/2110.08176"
date_ingested: 2026-07-04
---

# Collaborating with Humans without Human Data

![[2021-fcp-collaborating-humans-without-human-data-thumbnail.png]]

## Research gap
Standard multi-agent RL methods — self-play (SP) and population play (PP) — produce agents that overfit to their training partners and fail when paired with novel humans. The alternative, behavioral cloning play (BCP), requires expensive and privacy-sensitive human data collection. No method existed to train agents that collaborate well with humans zero-shot without requiring any human data.

## Contributions
- Proposes **Fictitious Co-Play (FCP)**, a two-stage method for training collaborative agents without human data: (1) train N independent self-play agents with checkpoints, (2) train a partner agent as the best response to the full pool of agents and checkpoints.
- Demonstrates that FCP significantly outperforms SP, PP, and BCP in zero-shot coordination with both novel agents and novel human partners.
- Introduces a rigorous human-agent interaction study with behavioral analysis and subjective preference elicitation.
- Shows that humans express a significant subjective preference for FCP partners over all baselines, including the human-data-trained BCP.

## Method
FCP has two stages. In **Stage 1**, N self-play agents are trained independently (varying only random seed), with periodic checkpoints saved throughout training. The different seeds produce agents that break coordination symmetries in different ways, while checkpoints at different training stages represent different skill levels. In **Stage 2**, an FCP agent is trained via RL (VMPO) as the best response to the entire pool of fully-trained agents and their past checkpoints, with partner parameters frozen. This forces the FCP agent to adapt to diverse partner conventions and skill levels rather than expecting partners to adapt to it. The method uses egocentric observations (enabling a single agent across all layouts) rather than the top-down full-state representation used in prior work.

## Datasets & evaluation
Evaluated in the Overcooked collaborative cooking simulator (two-player, fully-observable, common-payoff), implemented in DMLab2D. Five kitchen layouts of varying difficulty were used. Agent evaluation involved zero-shot pairing with held-out agents. Human evaluation (N participants) measured both objective task performance (number of deliveries) and subjective preference (pairwise comparisons). FCP achieved significantly higher scores than SP, PP, and BCP with both novel agents and human partners. Humans reported a strong subjective preference for FCP partners over all baselines.

## Limitations
- Evaluated only in the Overcooked gridworld domain — generalization to more complex, continuous, or real-world tasks is untested.
- The diversity of the training pool depends on the self-play agents finding meaningfully different conventions, which may not scale to environments with fewer natural symmetry-breaking opportunities.
- FCP's best-response training assumes a fixed partner pool; it does not perform online adaptation to a specific human during interaction.
- BC agents were trained on fewer human data steps (12,000 vs. 18,000 per layout in prior work), which may understate BCP's potential.

## Key takeaways
- A surprisingly simple approach — best-responding to a diverse pool of self-play agents and their checkpoints — can produce agents that collaborate with humans better than methods trained on actual human data.
- Partner diversity during training is the key ingredient for zero-shot human-AI coordination, not human data itself.
- The two sources of diversity in FCP (random seed variation for convention breaking, checkpoints for skill variation) are complementary and the checkpoint diversity comes at zero extra training cost.
- Humans not only perform better with FCP agents but actively prefer them, suggesting FCP agents exhibit qualitatively different collaborative behavior (adaptability, role flexibility) that humans value.
