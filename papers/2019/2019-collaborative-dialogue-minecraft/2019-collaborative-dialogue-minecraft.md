---
title: "Collaborative Dialogue in Minecraft"
authors:
  - Anjali Narayan-Chen
  - Prashant Jayannavar
  - Julia Hockenmaier
year: 2019
venue: "ACL 2019"
tags:
  - human-ai-collaboration
  - multi-agent
  - embodied-ai
url: "https://aclanthology.org/P19-1537/"
date_ingested: 2026-07-17
---

# Collaborative Dialogue in Minecraft

![[2019-collaborative-dialogue-minecraft-thumbnail.png]]

## Research gap
Prior work on grounded language and dialogue operated in either simplified 2D or fixed-perspective 3D environments with single-shot instructions. No dataset captured the full complexity of two-way, multi-turn situated dialogue for collaborative construction in a 3D world with free camera movement, floating structures, and asymmetric player roles and information access.

## Contributions
- The **Minecraft Collaborative Building Task**: a two-player asymmetric game where an Architect (who sees the target structure) must instruct a Builder (who can place blocks) via text chat to reproduce a 3D structure.
- The **Minecraft Dialogue Corpus**: 509 human-human dialogues (15,926 utterances, 113,116 tokens) with complete game logs, screenshots, and world states for 150 target structures of varying complexity.
- A data collection platform built on Microsoft's Project Malmo that captures discretized game states at meaningful events (block placement/removal, chat messages).
- Baseline seq2seq models for Architect utterance generation that incorporate global and local world state representations via block counters.

## Method
The task defines two asymmetric roles: the **Architect** sees the target structure and gives instructions but cannot place blocks; the **Builder** can place and remove blocks but cannot see the target. Both communicate via text chat. The Architect can observe the Builder's actions and move freely in the world.

For the Architect utterance generation subtask, the authors develop a seq2seq model with:
1. **Dialogue history encoder**: bidirectional RNN over the full chat history with speaker-specific tokens.
2. **World state representations**: The model computes optimal alignments between the built and target structures (accounting for translational and rotational invariance via Hamming distance), then derives:
   - **Global block counters**: 18-dimensional vectors capturing expected placements, next placements, and removals per color across the whole build region.
   - **Local block counters**: 27 sets of block counters for a 3x3x3 cube around the Builder's last action, encoded with perspective-relative spatial directions.
3. Both counter types are concatenated, passed through a fully-connected layer, and fed into the decoder at each timestep.

## Datasets & evaluation
- **Corpus**: 509 dialogues across 150 structures (6–68 blocks each, avg. 23.5). Training/test/dev splits are structure-disjoint (281/137/101 dialogues). 53.6% of structures contain floating blocks.
- **Automated metrics**: BLEU scores and term-specific precision/recall for colors, spatial relations, and dialogue act keywords. The full model (global + local counters) improves color precision/recall from 9.3/8.6 to 8.7/8.7 and 8.1/17.0 to 14.9/28.7 over the seq2seq baseline on the test set.
- **Human evaluation**: 3 evaluators assessed 100 randomly sampled scenarios. The full model generates correct or partially correct utterances more often than the baseline, but humans vastly outperform both models in utterance diversity and correctness. Models overwhelmingly generate instructions (72–76% of the time) whereas humans produce instructions only ~47% of the time, devoting more effort to descriptions, confirmations, and corrections.

## Limitations
- Only considers the Architect utterance generation subtask, not full interactive gameplay or Builder response generation.
- Models generate instructions even when inappropriate (e.g., when the Builder asks a question), lacking dialogue act diversity.
- The world state representation uses block counters that compress spatial information, losing fine-grained geometric detail about structure topology.
- The seq2seq architecture with GloVe embeddings represents a 2019-era baseline; modern approaches with pretrained language models and attention mechanisms would likely perform substantially better.
- Evaluation is limited to text generation quality; no end-to-end evaluation of whether generated instructions actually lead to successful building.

## Key takeaways
- Collaborative situated dialogue is fundamentally different from single-shot instruction following: it requires multi-turn interaction, error correction, perspective-taking, and adaptive communication strategies.
- Architects and Builders exhibit rich pragmatic behaviors — defining new terms for substructures, extrapolating from partial instructions, and adapting communication granularity to structure complexity — that current models fail to capture.
- Even simple world state representations (block counters) meaningfully improve generation quality, particularly for color accuracy, demonstrating that grounding in environment state is essential for situated dialogue.
- The asymmetric information setup (one agent sees the goal, the other acts) is a compelling testbed for studying communication under partial observability, a core challenge for collaborative embodied agents.
