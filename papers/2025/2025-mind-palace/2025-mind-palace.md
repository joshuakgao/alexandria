---
title: "Enter the Mind Palace: Reasoning and Planning for Long-term Active Embodied Question Answering"
authors: [Muhammad Fadhil Ginting, Sung-Kyun Kim, Kevin Meng, Cedric Reinke, Nikhil Krishna, Joshua Kayhani, Jesse Peltzer, David Fan, Ali Shaban, Mykel Kochenderfer, Ali-akbar Agha-mohammadi, Luca Omidshafiei]
year: 2025
tags: [embodied-memory, scene-graphs]
url: ""
pdf: "[[2025-mind-palace.pdf]]"
date_ingested: 2026-06-18
---

# Enter the Mind Palace: Reasoning and Planning for Long-term Active Embodied Question Answering

![[2025-mind-palace-thumbnail.png]]

## Research gap

Mind Palace Exploration addresses a new problem formulation called Long-term Active Embodied Question Answering (LA-EQA), where a robot must combine long-term memory recall with active exploration to answer complex, temporally-grounded questions. Unlike prior EQA work that focuses on either single-episode memory or present-only exploration, LA-EQA requires reasoning over past, present, and possible future states—deciding when to explore, when to consult memory, and when to stop and answer.

## Contributions

- Formalizes Long-term Active EQA (LA-EQA) as a new task combining multi-episode memory recall with active exploration
- Proposes the Robotic Mind Palace: episodic world instances as hierarchical scene graphs with temporal indexing
- Introduces a reasoning-planning loop that interleaves target object identification, hierarchical scene graph search, and working memory updates
- Develops Value-of-Information stopping criteria to reduce unnecessary memory retrieval while maintaining exploration efficiency
- Creates the first LA-EQA benchmark with 150 questions spanning 5 question types (past, present, multi-past, past-present, past-present-future)
- Demonstrates real-world deployment on a legged robot with 6 months of accumulated memory

## Method

**Mind Palace Generation**: Long-term robot history is chunked into episodes (naturally aligned with battery recharging cycles). Each episode becomes a hierarchical scene graph: viewpoints w (sampled from trajectories, with images, object lists, and captions) are clustered into area nodes v based on spatial and contextual similarity. World instances G are indexed by macro-temporal labels.

**Reasoning**: A VLM checks if the working memory h_k contains enough information to answer Q. If not, an LLM identifies a target object y (explicit or inferred from context, e.g., "something to make coffee" → coffee machine).

**Planning**: Operates hierarchically: (1) select and sequence world instances G using LLM reasoning about temporal relevance, with a heuristic prioritizing past instances over present; (2) within each G, use LLM-predicted probabilities P(y|v) and forward search to plan the optimal sequence of areas to explore; (3) within each area, select viewpoints via LLM reasoning over textual indices.

**Exploration**: For past world instances, retrieve images from memory. For the present instance G₀, navigate to viewpoints and capture new images. All images go into working memory h_k.

**Early Stopping**: A VoI criterion evaluates whether retrieving more past memories would change the planned exploration sequence. If the prediction set for object location contains only one area, or if VoI of further retrieval is below threshold, stop retrieving and proceed to physical exploration.

## Datasets & evaluation

- **Answer correctness**: 65.0% (vs. 52.9% best baseline Multi-Frame VLMs), 12–28% improvement over all baselines
- **With early stopping**: 61.8% correctness with 31% fewer retrieved images (15.73 vs. 22.86)
- **Exploration efficiency**: 0.45 (vs. 0.29 best baseline), 16% higher
- **Memory efficiency**: Uses only 22.86 images on average vs. 100 for VLM-based methods (77% fewer)
- **Scalability**: Performance gains increase with more past episodes, unlike baselines that degrade
- **Real-world**: Demonstrated on legged robot in 1,000 m² office with 10 past episodes spanning 4 days + 6 monthly inspections
- Strongest gains on past, multi-past, and past-present question types requiring cross-episode reasoning

## Limitations

- Scene graph construction relies on pre-computed area clustering; how to handle environments that change drastically between episodes (e.g., rooms repurposed) is not addressed
- The macro-temporal chunking into episodes is natural for battery-charging robots but may not generalize to continuous-operation systems
- Forward search planning over areas assumes a known spatial layout; handling partially known or significantly changed environments requires more robust replanning
- Question types involving future prediction (past-present-future) remain the most challenging, with unclear how to improve temporal extrapolation
- The VoI stopping criterion assumes calibrated LLM probability estimates, which may not hold across different environments or LLM versions
- Scalability beyond hundreds of episodes and thousands of viewpoints is untested

## Key takeaways

The core idea is the **Robotic Mind Palace**, inspired by the cognitive science mnemonic technique. The system structures a robot's accumulated observations into a series of episodic **world instances**, each represented as a hierarchical scene graph with three levels: world instances G (indexed by macro-temporal labels like "yesterday", "last week"), area nodes v (spatial clusters like "dining room", "entrance"), and viewpoints w (individual images with object lists and captions). This creates a unified representation spanning past memories and the current environment.

During LA-EQA, the agent follows a reasoning-planning loop: (1) **Mind Palace Reasoning** determines whether sufficient information exists to answer or identifies a target object to search for; (2) **Mind Palace Planning** selects which world instances and areas to search, using LLM-predicted object probabilities and forward search planning; (3) the agent either retrieves past images or physically explores the current environment; (4) **working memory** h_k accumulates retrieved and observed images. A **Value-of-Information (VoI)** stopping criterion determines when further memory retrieval is unlikely to improve the next exploration action, enabling early termination.

Evaluated on a new LA-EQA benchmark (150 questions across 5 simulated and real-world scenes spanning days to months), Mind Palace outperforms all baselines by 12–28% in answer correctness while using 77% fewer retrieved images. Real-world deployment on a legged robot in a 1,000 m² office with 6 months of inspection data demonstrates practical feasibility.

Mind Palace directly addresses limitations of both [[2024-remembr|ReMEmbR]] and standard EQA approaches. ReMEmbR is used as a baseline and performs steadily but lacks active exploration capability and structured spatial search. Unlike [[2025-embodied-rag|Embodied-RAG]]'s semantic forest which clusters based on spatial-semantic similarity within a single episode, Mind Palace maintains separate episodic world instances indexed temporally, preserving the distinction between observations from different time periods. Compared to [[2025-r4-retrieval-augmented-reasoning-4d|R⁴]]'s continuous 4D database, Mind Palace takes a more structured, planning-oriented approach—using hierarchical scene graphs with explicit forward search planning rather than flat retrieval with iterative reasoning. The VoI stopping criterion is unique among embodied memory systems, introducing decision-theoretic rigor to the explore-vs-recall trade-off.
