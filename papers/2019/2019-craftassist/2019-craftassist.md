---
title: "CraftAssist: A Framework for Dialogue-enabled Interactive Agents"
authors:
  - Jonathan Gray
  - Kavya Srinet
  - Yacine Jernite
  - Haonan Yu
  - Zhuoyuan Chen
  - Demi Guo
  - Siddharth Goyal
  - C. Lawrence Zitnick
  - Arthur Szlam
year: 2019
tags:
  - embodied-ai
  - multi-agent
url: https://arxiv.org/abs/1907.08584
date_ingested: 2026-07-17
---

# CraftAssist: A Framework for Dialogue-enabled Interactive Agents

![[2019-craftassist-thumbnail.png]]

## Research gap
While ML methods have achieved impressive performance on narrowly-defined tasks, building general systems that can complete a long-tailed distribution of simpler tasks specified ambiguously by humans using natural language remains an open problem. Existing Minecraft AI frameworks (MALMO, MineRL) focus on RL agents with explicit objectives, rather than dialogue-driven assistants that can interpret and execute free-form human instructions in a collaborative setting.

## Contributions
- An open-source framework for building dialogue-enabled interactive agents in Minecraft, with a modular architecture (semantic parser, dialogue manager, symbolic memory, perception, task stack) that allows independent research on each component.
- A neural semantic parser (Text-to-Action-Dictionary) that maps natural language instructions to structured logical forms (action dictionaries) interpretable by the bot, supporting commands, questions, memory updates, and multi-turn dialogue.
- Multiple released datasets: 800K generated dialogue-action pairs, 25K human rephrases, 2,586 crowdsourced house builds with action sequences, and instance segmentation labels for house sub-components (1,038 distinct labels).
- Infrastructure for recording human-bot interactions on Minecraft servers, enabling scalable data collection where players generate training data as a byproduct of gameplay.

## Method
The bot connects to a Minecraft server as a regular player using the Minecraft network protocol (no server-side mods required). The architecture follows a pipelined approach: (1) a neural semantic parser (bi-directional GRU encoder with multi-headed attention) converts chat messages into action dictionaries — structured logical forms specifying dialogue type, action type, and parameters with word-span references for generalization; (2) a dialogue manager routes action dictionaries to appropriate dialogue objects that interpret them in context of the world state; (3) a symbolic memory module (in-memory SQLite with triple store) maintains the bot's understanding of block objects, mobs, tags, relations, and task history; (4) perception modules handle 2D/3D block vision, semantic segmentation (3D CNN), relative directions, size, and color; (5) a task stack executes high-level composable tasks (Move, Build, Destroy, Dig, Fill, Spawn, Dance) with control flow (Undo, Loop) in LIFO order, where each task translates to sequences of low-level actions.

## Datasets & evaluation
The framework is released with four semantic parsing datasets of varying naturalness: generated (800K pairs from grammar templates), rephrases (25,402 crowd-worker paraphrases), prompts (2,513 free-form commands), and interactive (708 pairs from live gameplay). The house dataset contains 2,586 houses built by crowd workers in creative mode with full action sequences recorded. Instance segmentation covers 2,050 houses with 1,038 distinct sub-component labels. No formal quantitative evaluation of the bot's task completion is presented — the paper focuses on framework design and data release, positioning the bot as a baseline for future research.

## Limitations
- The semantic parser operates only on text and is not aware of world state, creating a strict separation that limits contextual understanding.
- The action dictionary specification constrains what the bot can do — adding new capabilities requires manual extension of the grammar, dialogue objects, and training data.
- Memory is purely symbolic (relational database), which is reliable for keyword lookups but brittle for fuzzy or learned representations.
- No formal evaluation of end-to-end task completion rates; the bot is presented as an initial baseline rather than a performant system.
- The pipelined approach introduces compounding errors across modules.
- Creative mode only — survival mode tasks (resource gathering, crafting, combat) are not addressed.

## Key takeaways
- Minecraft's open-ended creative mode with multiplayer chat provides a compelling testbed for studying dialogue-driven task completion, where the assistant must handle ambiguous instructions, ask clarifying questions, and maintain shared context across multi-turn interactions.
- The pipelined approach (parse → interpret → execute) trades end-to-end learning potential for modularity, debuggability, and easier data collection — the action dictionary serves as a stable interface that decouples language understanding from task execution.
- Symbolic memory with a triple store enables reliable querying and integration of external knowledge (e.g., building schematics), but limits the system to discrete representations and keyword-based retrieval.
- Crowdsourced data collection through normal gameplay (house building, chat interactions) is a scalable alternative to expensive expert demonstrations, foreshadowing MineDojo's internet-scale approach.
