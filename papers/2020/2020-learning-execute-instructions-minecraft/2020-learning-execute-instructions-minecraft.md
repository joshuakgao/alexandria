---
title: "Learning to Execute Instructions in a Minecraft Dialogue"
authors: [Prashant Jayannavar, Anjali Narayan-Chen, Julia Hockenmaier]
year: 2020
venue: "ACL 2020"
tags: [embodied-ai, multi-agent, human-ai-collaboration]
url: "https://aclanthology.org/2020.acl-main.232/"
date_ingested: 2026-07-18
---

# Learning to Execute Instructions in a Minecraft Dialogue

![[2020-learning-execute-instructions-minecraft-thumbnail.png]]

## Research gap
Prior work on the Minecraft Collaborative Building Task (Narayan-Chen et al., 2019) focused on generating Architect utterances, but no models existed for the Builder role — the agent that must interpret natural language instructions and execute block placements/removals in a 3D environment. Existing instruction-following benchmarks (SCONE, Vision-and-Language Navigation) have simpler action spaces and less complex dialogue; Minecraft construction requires understanding multi-turn asynchronous dialogue, perspective-dependent spatial references, and planning non-monotonic action sequences (e.g., placing temporary scaffold blocks for floating structures).

## Contributions
- Defines the **Builder Action Prediction (BAP) task**: given a game context (dialogue history, world state, Builder perspective), predict the sequence of block placements and removals a human Builder performed.
- Proposes an **encoder-decoder architecture** combining a GRU-based game history encoder with a CNN-based 3D world state encoder, where the decoder predicts variable-length action sequences over 7,623 possible actions per step.
- Introduces two key world state augmentations: **action history weights** (recency-weighted encoding of recent actions in the grid) and **perspective coordinates** (transforming absolute cell coordinates into the Builder's egocentric reference frame).
- Demonstrates that **data augmentation** (utterance paraphrasing, color permutation, spatial rotation) substantially improves performance on the small dataset (3,709 → 22,254 training examples).
- Achieves 21.2 F1 (best model) vs. 11.8 F1 (baseline), nearly doubling performance through the combination of richer representations and augmented data.

## Method
The model is a recurrent encoder-decoder:

**Game history encoder**: A GRU processes the dialogue as a token sequence with speaker-specific markers (〈A〉...〈/A〉, 〈B〉...〈/B〉) and Builder actions represented as tokens (e.g., "builder putdown red"). Three history schemes tested: H1 (last Architect utterance only), H2 (all utterances after Builder's penultimate action), H3 (H2 + token representation of Builder's last actions). GloVe embeddings (300-dim) for words; random embeddings for action/speaker tokens.

**World state encoder**: A multi-layer 3D CNN over an 11×9×11×D grid tensor. Each cell is represented by a 7-dim one-hot (block color or empty), augmented with: (1) action history weights (integer recency weights 1–5 for the last 5 actions), and (2) perspective coordinates (cell positions transformed into Builder's egocentric frame via rotation matrices accounting for yaw and pitch). Total 11-dim per cell.

**Action sequence decoder**: A GRU backbone initialized from the history encoder, producing one action per step. At each step, the decoder GRU hidden state is concatenated to CNN cell representations and passed through 1×1×1 conv layers to score all 7,623 block actions. A separate STOP predictor uses max-pooled cell features to decide when to terminate. Greedy decoding with max sequence length 10.

**Data augmentation**: For each training game log, 20 synthetic variants are generated via random combinations of utterance paraphrasing (synonym substitution), color permutation (one of 720 permutations applied consistently), and spatial rotation (0°/90°/-90°/180° in the ground plane).

## Datasets & evaluation
**Dataset**: Minecraft Dialogue Corpus (Narayan-Chen et al., 2019) — 509 human-human dialogues, 150 target structures. Train/dev/test splits ensure disjoint target structures. 3,709 training action sequences (avg. length 4.3), 1,616 test sequences.

**Metric**: Net action F1 — comparing predicted vs. human action sequences after removing self-undone actions (to handle placeholder blocks and minor mistakes). Micro-averaged across all sequences.

**Key results**:
- BAP-base with H1 history: 11.8 F1
- Adding action history weights + H3: 19.7 F1
- Adding perspective coordinates + H3: 18.8 F1 (but 21.2 with 4x augmented data)
- Best model (full features, H3, 4x augmentation): **21.2 F1**
- Less than 1% of generated sequences contain infeasible actions; constrained decoding does not change F1
- Mean predicted sequence length (2.66) remains well below gold mean (4.3), indicating models stop too early

**Qualitative findings**: The model correctly identifies block colors, can interpret simple spatial relations ("on top of," "do it one more time"), handles corrective dialogue (triggering removals after "sorry, my mistake"), but struggles with vague quantity references and occasionally over-removes blocks.

## Limitations
- Low absolute F1 (21.2) indicates the task remains largely unsolved — models predict reasonable ballpark locations but struggle with exact placements, especially for complex spatial instructions.
- Evaluation against human action sequences provides only a lower bound, since multiple valid action sequences may exist for a given instruction.
- No back-and-forth dialogue capability — the model only predicts actions, not when/how to ask clarification questions or respond to the Architect.
- Small dataset (509 dialogues) constrains model capacity despite augmentation.
- Builder's movement is not modeled — the model assumes access to the Builder's position/orientation but does not predict how the Builder navigates.
- Perspective coordinates assume spatial references are relative to Builder's position at utterance time, which may not always hold.

## Key takeaways
- The Builder's perspective matters: transforming world coordinates into the Builder's egocentric frame improves spatial relation understanding, confirming that Architect instructions are grounded in the Builder's viewpoint rather than absolute coordinates.
- Action history is a surprisingly strong signal — encoding which cells were recently modified provides implicit context about the current sub-task, compensating for the model's limited ability to parse complex multi-utterance instructions.
- Data augmentation is essential for this low-resource setting: color permutation and spatial rotation exploit the task's inherent symmetries to provide meaningful diversity, yielding consistent improvements across model variants.
- The gap between predicted and gold sequence lengths (2.66 vs. 4.3) suggests the model's primary failure mode is under-generation — it learns to place a few correct blocks but lacks the planning capability to complete longer construction sequences.
