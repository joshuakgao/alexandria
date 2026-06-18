---
title: "Large Language Models as Tool Makers"
authors: [Tianle Cai, Xuezhi Wang, Tengyu Ma, Xinyun Chen, Denny Zhou]
year: 2024
venue: "ICLR"
tags: [agentic-mllms]
url: ""
pdf: "[[2024-llm-as-tool-makers.pdf]]"
date_ingested: 2026-06-18
---

# Large Language Models as Tool Makers

![[2024-llm-as-tool-makers-thumbnail.png]]

## Research gap

LATM (Large Language Models As Tool Makers) introduces a closed-loop framework where LLMs generate reusable tools for solving classes of tasks, then other LLMs (or the same model) apply those tools to new instances. The framework operates in two phases: (1) **tool making** where a powerful model (e.g., GPT-4) generates Python utility functions implementing problem-solving strategies from training examples, and (2) **tool using** where a lightweight model (e.g., GPT-3.5 Turbo) applies the generated tools to solve similar problem instances. Tool making comprises three sub-phases: tool proposing (generate Python function), tool verification (write unit tests), and tool wrapping (package for reuse). This division of labor amortizes the cost of tool-making across multiple tool-using instances, achieving GPT-4-level performance with GPT-3.5 efficiency. The framework also enables "functional caching"—storing reusable tools rather than natural language responses—extending caching applicability beyond textual similarity.

## Contributions

- **Tool-making framework**: Enables LLMs to autonomously create reusable Python tools from problem examples
- **Two-phase division of labor**: Separates expensive tool-making (powerful model) from tool-using (lightweight model), reducing average inference cost
- **Tool verification pipeline**: Includes unit test generation and validation to ensure tool correctness
- **Functional caching**: Novel caching mechanism storing tools (functionality) rather than responses (text), enabling reuse across functionally similar requests
- **Cost-efficiency gains**: Achieves GPT-4 performance with GPT-3.5 cost through strategic model allocation

## Method

**Tool-making phase**:
1. **Tool proposing**: Given training examples, LLM generates a generic Python function solving the task
2. **Tool verification**: LLM writes unit tests converting validation examples into function calls; iteratively fixes function until tests pass
3. **Tool wrapping**: Packages the function with examples showing how to convert problems to function calls; creates API specification for tool reuse

**Tool-using phase**:
- Convert new problem instances into function calls using wrapped tool's API
- Execute function to obtain solution
- Lightweight model sufficient since reasoning is pre-encoded in tool

**Functional caching**:
- Server caches generated tools indexed by task type
- When new request arrives, check if functionally similar tool exists
- Reuse tool via cached API, avoiding tool-making for subsequent requests
- Extends beyond textual similarity matching in traditional caches

**Cost-benefit model**:
- Tool-making cost (expensive): amortized over N tool-using instances (cheap)
- Average cost per instance: (cost_making + N × cost_using) / N
- Significant savings when N > 1

## Datasets & evaluation

- **Big-Bench tasks**: Strong performance across complex reasoning benchmarks
- **Cost-efficiency**: GPT-4 tool maker + GPT-3.5 Turbo tool user achieves GPT-4 performance at fraction of cost
- **Task reusability**: Single tool handles multiple problem instances without retraining
- **Generalization**: Tools generalize to similar problems not in training set
- **Functional cache performance**: Reduces cost for repeated functional queries

## Limitations

- How well do generated tools generalize to out-of-distribution problems?
- Can the tool-making process be further optimized for efficiency?
- How does tool complexity affect generalization and reusability?
- Can human feedback improve tool-making quality?
- How does this scale to multi-tool coordination problems?

## Key takeaways

LATM builds on tool-using literature (ReAct, Chameleon, etc.) by enabling autonomous tool generation rather than relying on pre-existing tools. It extends functional-cache concepts from code-generation work (ArithmeticCodeX) to broader reasoning domains. The two-model approach relates to speculative decoding's cost-performance tradeoffs but operates at the tool level rather than token level.
