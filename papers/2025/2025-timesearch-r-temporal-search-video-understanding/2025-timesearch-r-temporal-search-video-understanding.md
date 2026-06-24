---
title: "TimeSearch-R: Adaptive Temporal Search for Long-Form Video Understanding via Self-Verification Reinforcement Learning"
authors: [Junwen Pan, Qizhe Zhang, Rui Zhang, Ming Lu, Xin Wan, Yuan Zhang, Chang Liu, Qi She]
year: 2025
venue: ""
tags: [video-understanding, world-models, vision-language-models]
url: ""
pdf: "[[2025-timesearch-r-temporal-search-video-understanding.pdf]]"
date_ingested: 2026-06-18
---

# TimeSearch-R: Adaptive Temporal Search for Long-Form Video Understanding via Self-Verification Reinforcement Learning

![[2025-timesearch-r-temporal-search-video-understanding-thumbnail.png]]

## Research gap

Long-form video understanding requires a model to navigate thousands of frames to identify the minimal set relevant to a question. Prior VLMs rely on static frame sampling — selecting a fixed set of frames before reasoning begins — which is both inefficient and brittle. VideoAgent-style approaches use LLMs as interactive agents with hand-crafted retrieval workflows, but lack end-to-end optimisation for learning *when* to search and *what* to look for.

## Contributions

- **TimeSearch-R framework**: temporal search as a multi-turn thinking process; the model emits `<tool_call>` instructions to a search function mid-reasoning, interleaving visual evidence acquisition with textual logic
- **GRPO-CSV**: extends GRPO by adding a CSV rollout phase that re-prompts the model to answer using only its gathered frames, generating a completeness reward independent of trajectory length — provides learning signal even when the search fails
- **"Thinking with Videos" paradigm**: distinct from prior RL-for-reasoning work (DeepSeek-R1, Video-R1) which apply GRPO to text-only chains or static image grids; TimeSearch-R is the first to integrate dynamic frame retrieval into the RL reasoning loop
- **Two-stage training data pipeline**: filters long-video QA pairs for (1) visual dependency (question must require video, not just text knowledge) and (2) search usefulness (increasing frames from 4→64 must improve accuracy), yielding high-quality temporal reasoning training data
- SOTA on Haystack-LVBench, Haystack-Ego4D, VideoMME, and LongVideoBench

## Method


## Datasets & evaluation

**Temporal search (Haystack-LVBench):**

| Method | P | R | F₁ | Comp. | Acc. |
|--------|---|---|----|-------|------|
| Qwen2.5-VL w/ search | 0 | 0 | 0 | 44.2 | 59.4 |
| SFT | 1.4 | 4.5 | 2.2 | 56.0 | 72.0 |
| TimeSearch-R (Ours) | **5.4** | **8.4** | **6.4** | **63.8** | **81.0** |

**Long-form video understanding (VideoMME / LongVideoBench):**

| Method | VideoMME | LongVideoBench |
|--------|----------|----------------|
| Qwen2.5-VL-7B (base) | 68.4 | 56.4 |
| Video-R1 | 59.9 | 58.1 |
| TimeSearch-R | **71.5** | **60.1** |

**End-to-end latency (Haystack-Ego4D, A100):** 13.4s vs. VideoAgent 34.9s, T* 33.8s

![[raw/video-understanding/2025/assets/timesearch-fig4-ablation-results.png]]
*Figure 4: Ablation — removing CSV collapses completeness and RL training stability; GRPO-CSV achieves best overall performance*

Key ablation findings:
- Removing CSV: completeness drops from 60.5% → 57.2%, training collapses around step 300
- RL alone (without SFT warm-up): adequate for temporal search, critical for long-video tasks
- GRPO-CSV with accuracy reward achieves best QA performance (66.6%)
- Data composition: including egocentric (Ego4D) data improves temporal (+7.4%) and action reasoning (+5.7%) significantly

## Limitations

- Evaluated only on QA benchmarks; temporal grounding (localisation) precision for open-ended events remains unmeasured
- CSV reward requires an additional LLM forward pass per rollout, increasing training compute
- Max 768 frames per video limits applicability to very long content (>3 hours) without further hierarchical search
- Search function is not learned — DPP is a fixed heuristic; end-to-end learned retrieval could yield further gains
- LLM usage disclaimer: authors note LLMs were used for writing polish/fluency only, not for literature review or experimental analysis

## Key takeaways

TimeSearch-R reformulates temporal search as an interleaved text-video thinking process trained via reinforcement learning. The model alternates between textual reasoning (`<think>`) and calling a search tool that retrieves new video frames, progressively narrowing its temporal window before committing to a final answer. A novel RL algorithm, **GRPO-CSV**, extends standard GRPO with a Completeness Self-Verification phase that provides supervision on both successful and failed search trajectories, improving training stability and search completeness.

- **VideoAgent** (Wang et al., 2024): LLM-based interactive video agent with handcrafted tool workflows; TimeSearch-R surpasses it while achieving 2.6× lower latency and learning search strategies end-to-end rather than through engineered pipelines
- **T*** (Ye et al., 2025): Prior SOTA on Haystack benchmarks using detector-based frame retrieval; TimeSearch-R surpasses F₁ by 2.5× and QA accuracy by 25+ points on Ego4D
- **Video-R1** (Feng et al., 2025): Applies GRPO to video reasoning but with static frame sampling; TimeSearch-R adds adaptive search and outperforms it on all benchmarks including a +11.6% gap on VideoMME
- **DeepSeek-R1**: Motivates the RL-for-reasoning paradigm; TimeSearch-R extends it to the dynamic visual domain where reasoning must interleave with active perception
