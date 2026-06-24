---
title: "ViperGPT: Visual Inference via Python Execution for Reasoning"
authors: [Dídac Surís, Sachit Menon, Carl Vondrick]
year: 2023
venue: "ICCV 2023"
tags: [agentic-mllms]
url: ""
pdf: "[[2023-viper-gpt.pdf]]"
date_ingested: 2026-06-18
---

# ViperGPT: Visual Inference via Python Execution for Reasoning

![[2023-viper-gpt-thumbnail.png]]

## Research gap

ViperGPT proposes a framework for solving complex visual reasoning tasks by leveraging code-generation LLMs (GPT-3 Codex) to automatically compose vision-and-language models. Instead of training end-to-end models, ViperGPT generates executable Python code that chains together vision primitives (object detection, property verification, depth estimation, spatial reasoning) to answer open-ended visual queries. The generated programs are directly executed by a Python interpreter, providing explicit interpretability and enabling systematic generalization without task-specific training.

## Contributions

- **Code-generation-based program synthesis**: leverages GPT-3 Codex's code priors to automatically generate Python programs that compose vision modules, eliminating the need for hand-designed program templates or reinforcement learning from scratch
- **Zero-shot compositional reasoning**: no task-specific training required; generalization achieved by recombining pretrained modules in new ways via generated code
- **Explicit interpretability**: generated programs are executable Python with intermediate variable inspection, enabling auditing of each reasoning step and diagnosis of failures
- **Flexible modular API**: any vision or language module can be incorporated by adding its specification to the API; improvements in modules directly improve system performance without retraining
- **Unified task framework**: single system handles visual grounding, image QA, video QA, spatial reasoning, and color reasoning via program composition

## Method

**API Design**: The framework exposes vision capabilities as a Python API with functions like `find(object_name)`, `compute_depth()`, `exists(object_name)`, `simple_query(question)`, and `llm_query(text)`. Each function is specified with a docstring explaining its behavior.

**Program Generation**: Given a natural language query and the API specification, GPT-3 Codex generates executable Python code that chains these functions together. The model outputs a complete `execute_command(image)` or `execute_command(video)` function that takes visual input and returns a result.

**Execution**: The generated code is executed by a Python interpreter with the API functions bound to actual implementations (e.g., CLIP for object detection, MiDaS for depth estimation, GPT-3 for reasoning). Intermediate variable values are logged for interpretability.

**Video Extension**: For video queries, the framework provides `VideoSegment` primitives that support frame iteration, temporal reasoning, and event detection across sequences.

## Datasets & evaluation

- **Visual Grounding**: SOTA on Flickr30K Entities and RefCOCO+ zero-shot (no training on target benchmarks)
- **Image QA**: SOTA on GQA, OK-VQA, and TextVQA in zero-shot settings; competitive on standard VQA v2
- **Video QA**: Strong zero-shot performance on VideoQA and ActivityNet-QA
- **Compositional generalization**: systematic improvements over end-to-end baselines on questions requiring multi-step reasoning (e.g., counting, comparison, property verification)

## Limitations

- API coverage: quality depends on completeness and quality of the provided API; gaps in vision capabilities limit expressiveness
- Error propagation: mistakes in intermediate steps propagate to final answer; no explicit error correction mechanism
- Latency: multiple sequential LLM calls and API invocations may be slower than end-to-end inference
- LLM-specific: approach relies on code-capable LLMs; applicability to smaller or non-code-trained models unclear
- Hallucination: generated code may call non-existent functions or make unsupported assumptions about module behavior

## Key takeaways

The core insight is that LLMs trained on vast code corpora have strong priors for logical composition and can reason about how to use a provided API of visual capabilities. A simple API specification (function signatures and docstrings) is sufficient for the model to synthesize correct programs for novel queries, from visual QA to video understanding to color mixing to architectural reasoning.

ViperGPT achieves state-of-the-art zero-shot performance across visual grounding, image QA, and video QA benchmarks, demonstrating that compositional interpretability and performance are complementary rather than antagonistic.

- **Neural Module Networks**: Similar spirit of compositional reasoning, but ViperGPT avoids hand-tuned program templates and expensive per-task supervision by leveraging LLM code priors
- **Neuro-symbolic reasoning**: Bridges neural modules and symbolic logic; achieves explicit reasoning without learning program structure end-to-end
- **End-to-end VLMs**: Concurrent to GPT-4V and multimodal foundation models; shows compositional interpretability can improve generalization
- **Tool-using LLMs**: Related to concurrent work on external tool use in NLP; ViperGPT applies this to vision with direct Python execution rather than natural language instructions
