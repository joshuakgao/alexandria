---
title: "Agentic Keyframe Search for Video Question Answering"
authors: [Sunqi Fan, Meng-Hao Guo, Shuojin Yang]
year: 2025
venue: ""
tags: [video-understanding, agentic-mllms]
url: ""
pdf: "[[2025-agentic-keyframe-search.pdf]]"
date_ingested: 2026-06-18
---

# Agentic Keyframe Search for Video Question Answering

![[2025-agentic-keyframe-search-thumbnail.png]]

## Research gap

This paper proposes AKEYS (Agentic Keyframe Search), an algorithm that leverages language agents to intelligently search for keyframes in videos for question answering tasks. Rather than processing all video frames (Video-LLM approach) or uniform sampling, AKEYS combines classical search algorithms with language agent heuristics to identify only the frames essential for answering a given question. The method segments video into a tree structure, uses agents to estimate search heuristics and costs while dynamically expanding nodes, and terminates when sufficient keyframes are collected. This achieves both high accuracy and exceptional frame efficiency—on EgoSchema, AKEYS reaches 63.1% accuracy while processing only 43.5% of frames compared to VideoTree, and uses roughly 1/4 the frames of VideoTree to reach equivalent accuracy levels.

## Contributions

- AKEYS algorithm combining classical search algorithms (DFS, BFS, Dijkstra's, A*) with language agent guidance for temporal navigation
- Dynamic, question-adaptive video tree structure versus static preprocessing approaches like VideoTree
- Only processes visible frames without pre-computing features for all frames, reducing computational overhead
- Achieves state-of-the-art results on EgoSchema (63.1%, +2.0%) and NExT-QA (77.4%, +1.8%) benchmarks
- Demonstrates highest frame efficiency among comparable methods

## Method

AKEYS operates on three core ideas:
1. **Tree structure**: Video is segmented and organized hierarchically, with representative frames extracted and captioned
2. **Search algorithm**: Uses a generic search framework with priority-based node expansion. The algorithm supports variants: DFS (depth-priority), BFS (breadth-priority), Greedy Best-First Search (heuristic h(n)), Dijkstra's (cost g(n)), and A* (combined f(n) = g(n) + h(n))
3. **Language agent guidance**: The agent estimates both heuristic costs (h(n)—estimated distance to goal) and movement costs (g(n)—cost from start to current node), dynamically guiding which branches to expand. The agent also determines termination by evaluating confidence in the answer

The agent-guided approach allows the tree structure to be adaptive per-question, unlike VideoTree's static preprocessing.

## Datasets & evaluation

- **EgoSchema fullset**: 63.1% accuracy (baseline 61.1%), processing 43.5% of frames vs VideoTree
- **NExT-QA**: 77.4% average accuracy (baseline 75.6%), ~25% of frames of VideoTree for equivalent accuracy
- Significantly outperforms uniform sampling and Video-LLM baselines in frame efficiency
- Matches or exceeds accuracy of methods that use all frame information

## Limitations

- Scalability to very long videos (hundreds of thousands of frames)
- Performance on videos with subtle, distributed relevant information requiring many scattered keyframes
- Sensitivity to question phrasing and agent prompting
- Generalization across diverse video domains and question types beyond EgoSchema and NExT-QA

## Key takeaways

Builds on VideoQA literature (neural networks, LLMs, agent-based approaches). Most directly extends VideoTree's keyframe search paradigm but addresses two key limitations: (1) avoids computing CLIP features for all frames upfront, reducing overhead, and (2) adapts tree structure per-question rather than using static clustering. Relates to broader work on agent-based video understanding (VideoAgent) and keyframe extraction (LVNet, VCA, IG-VLM).
