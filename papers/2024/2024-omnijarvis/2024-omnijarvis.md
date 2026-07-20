---
title: "OmniJARVIS: Unified Vision-Language-Action Tokenization Enables Open-World Instruction Following Agents"
authors: [Zihao Wang, Shaofei Cai, Zhancun Mu, Haowei Lin, Ceyao Zhang, Xuejie Liu, Qing Li, Anji Liu, Xiaojian Ma, Yitao Liang]
year: 2024
venue: "NeurIPS 2024"
tags: [vision-language-action, embodied-ai]
url: "https://arxiv.org/abs/2407.00114"
date_ingested: 2026-07-18
---

# OmniJARVIS: Unified Vision-Language-Action Tokenization Enables Open-World Instruction Following Agents

![[2024-omnijarvis-thumbnail.png]]

## Research gap
Existing VLA approaches for open-world environments face a fundamental dilemma. Pipeline approaches (e.g., DEPS, JARVIS-1, Voyager) use LLMs to emit textual goals to separate controllers, but text cannot fully capture the complexity and context-dependence of open-world tasks, leading to communication failures. Direct-control approaches (e.g., RT-2, LEO) output low-level actions from VLMs, but the long-horizon nature of open-world tasks makes this impractical due to context length, computation cost, and inference efficiency. Neither approach jointly models the reasoning, planning, and acting that characterize human decision-making in open worlds.

## Contributions
- Introduces **OmniJARVIS**, a VLA model that jointly models vision, language, and actions via unified tokenization of multimodal interaction data, enabling reasoning (chain-of-thought), planning, question answering, and acting within a single autoregressive model.
- Proposes a **self-supervised behavior tokenizer** using Finite Scalar Quantization (FSQ) that compresses behavior trajectories into discrete tokens, paired with an imitation learning policy decoder that converts behavior tokens back into motor control.
- Designs a **multimodal interaction data format** with instruction, memory, thought, observation, and behavior segments, synthesized from existing gameplay datasets using LLMs.
- Demonstrates strong performance across atomic, programmatic, and open-ended tasks in Minecraft, and shows generalization to Atari (Montezuma's Revenge).
- Provides scaling analysis showing evaluation loss decreases consistently with both data and model size (2B, 7B, 13B).

## Method
OmniJARVIS has two training stages:

**Stage 1 — Behavior Tokenizer**: A VAE-based encoder-decoder trained with self-supervised learning on behavior trajectories from the OpenAI contractor dataset. The encoder (non-causal transformer) maps trajectory segments (128 steps of observations + actions) to discrete tokens via FSQ with codebook configuration [8,8,8,6,5] (codebook size 15,360). The decoder (causal transformer) is an imitation learning policy that reproduces actions conditioned on behavior tokens and current observations. Only 35 new tokens (8+8+8+6+5) are added to the MLM vocabulary, with each behavior represented by 5 tokens (one per FSQ level).

**Stage 2 — Unified Autoregressive Training**: A pretrained MLM (LLaVA-7B) is fine-tuned on unified token sequences following the format: instruction → memory → [observation → thought → behavior tokens]* (repeating for each sub-task). Instructions, memory, and thoughts are synthesized from raw gameplay data using GPT-3.5. Training mixes embodied instruction-following data (600K trajectories, ~900M tokens) with Minecraft QA data (300K conversations, ~90M tokens), totaling ~1T tokens. Prefix language modeling is used: context segments (instruction, memory, observation) serve as prefix, while thought and behavior tokens are predicted autoregressively.

**Inference**: Given task instruction, empty memory, and initial observation, OmniJARVIS generates chain-of-thought reasoning then emits behavior tokens. The policy decoder executes the behavior. Every 32 steps, OmniJARVIS re-reasons with the latest observation and produces new behavior tokens.

## Datasets & evaluation
**Training data**: OpenAI Minecraft contractor dataset, augmented with LLM-synthesized instructions, memory, and thoughts. 300K Minecraft wiki QA conversations.

**Benchmarks**:
- **Atomic tasks** (chopping trees, digging dirt, mining stones, collecting seeds): OmniJARVIS matches or exceeds GROOT and STEVE-1 baselines.
- **Programmatic tasks** (30 tasks across wooden/food/stone/iron/diamond tiers, starting from empty inventory): OmniJARVIS achieves 0.59 average success rate vs. 0.43 for DEPS (best baseline) and 0.02 for GROOT. Notably, OmniJARVIS is the only method to succeed on diamond-tier tasks (0.08 success rate).
- **Open-ended instruction following** (FSD metric against human gameplay): OmniJARVIS achieves 886.2 FSD vs. 929.5 for DEPS and 932.1 for Voyager (lower is better).
- **Open-ended QA** (LLM-as-judge): OmniJARVIS scores 8.40 vs. GPT-3.5's 7.50, outperforming all LLM baselines.
- **Atari generalization**: OmniJARVIS achieves 3600 score on Montezuma's Revenge after adaptation.

**Ablations**: Instruction and thought are the most critical data segments for behavior token prediction. FSQ tokenizer outperforms VQ-VAE (which collapses during training) and language goals. Larger codebooks improve performance. LLaVA-7B architecture yields lowest eval loss among tested vision tokenizations. Scaling shows consistent improvement from 2B to 13B parameters with Pearson coefficients >0.99.

## Limitations
- Relies on LLM-synthesized interaction data (instructions, thoughts, memory) rather than real human annotations, which may introduce synthesis artifacts.
- Behavior tokenizer trunk size is fixed at 128 steps; handling much longer or shorter behaviors requires re-reasoning at fixed intervals.
- Diamond-tier programmatic task success rate remains low (0.08), indicating long-horizon compositional planning is still challenging.
- Evaluation is primarily in Minecraft; Atari generalization is shown only on a single game.
- No visual perception from raw pixels at the behavior tokenizer level — relies on Mineflayer-style structured observations for the encoder.

## Key takeaways
- Behavior tokenization via FSQ provides a compact, semantically meaningful intermediate representation that bridges the gap between high-level language reasoning and low-level motor control — more expressive than text goals (which lose context-dependent nuance) and more efficient than direct action prediction (which requires impractical sequence lengths).
- The interaction data format matters as much as the model: jointly modeling instruction, memory, chain-of-thought, and behavior in a single autoregressive sequence enables reasoning-to-action within one model, rather than requiring separate planner and controller modules.
- Thought synthesis is critical — adding LLM-generated chain-of-thought reasoning to training data significantly reduces behavior prediction loss, confirming that intermediate reasoning representations help bridge observations to actions.
- The approach scales predictably: evaluation loss decreases linearly (in log-space) with both data volume and model parameters, suggesting that larger models and more interaction data will continue to improve open-world decision-making performance.
