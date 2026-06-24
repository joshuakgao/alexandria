---
topic: Agentic MLLMs
slug: agentic-mllms
---

# Agentic MLLMs

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "agentic-mllms")
SORT year DESC
```

## Overview

# Agentic Multimodal LLMs — Overview

*Last updated: 2026-04-07 | Sources: 5 papers*

## Current thesis

Agentic multimodal systems leverage LLMs to orchestrate diverse tools and modules—via code generation, explicit planning, or multi-agent collaboration—achieving zero-shot compositional reasoning while maintaining interpretability. The field has evolved from vision-specific program synthesis (2022–2023) toward general agent frameworks that combine code generation with tool use, task planning, and multi-agent collaboration. Core insight: systematic decomposition of complex reasoning into tool invocations amplifies model capabilities and enables specialization (cheap models for tool use, expensive models for tool making/planning).

## Key trends

**2022 – Neuro-symbolic foundation**: [[papers/2022-visual-programming-compositional-visual-reasoning|VisProg]] demonstrates that LLMs (GPT-3) can generate Python programs composing vision/language modules without task-specific training. Handles diverse tasks (VQA, NLVR, knowledge tagging, image editing) in single framework; emphasizes interpretability.

**2023 – Vision-focused program synthesis**: [[papers/2023-viper-gpt|ViperGPT]] advances program synthesis with GPT-3 Codex, achieving SOTA zero-shot performance on grounding, image QA, video QA through compositional reasoning. Demonstrates that code priors enable flexible module composition.

**2024 – Tool generation and cost optimization**: [[papers/2024-llm-as-tool-makers|LATM]] introduces closed-loop tool-making framework where LLMs generate reusable Python tools. Key innovation: division of labor between expensive tool maker (GPT-4) and cheap tool user (GPT-3.5), amortizing tool-making cost over multiple uses. Enables functional caching beyond text similarity.

**2025 – Multi-agent orchestration and embodied reasoning**:
- [[papers/2025-tooleqa-embodied-question-answering|ToolEQA]]: Combines structured planning with tool-augmented reasoning for embodied QA; demonstrates tool-augmented agents navigate 3D environments more efficiently
- [[papers/2025-vipact-visual-perception-agent|VipAct]]: Multi-agent VLM framework with orchestrator, specialized agents, and vision experts; shows systematic collaboration overcomes monolithic VLM limitations for fine-grained perception

**2026 – Vision-action foundation agents**:
- [[papers/2026-nitrogen|NitroGen]]: Shifts from LLM-as-orchestrator to end-to-end vision-action models trained via behavior cloning on 40,000 hours of internet gameplay across 1,000+ games. Demonstrates that internet-scale data with automatically extracted action labels can bootstrap generalist agents without RL or manual demonstrations. Transfers to unseen games with up to 52% relative improvement over training from scratch, though limited to reactive (system-1) behavior without planning or language conditioning.

**Evolution summary**: From single-model code generation → specialized tool generation → orchestrated multi-agent systems with division of labor → end-to-end vision-action foundation models at internet scale

## Open problems

- **Tool design and API ergonomics**: How to design tool APIs that balance expressiveness and composability? Can LLMs learn to compose unfamiliar tools?
- **Error propagation and recovery**: How can agents detect and recover from tool failures? Can intermediate validation improve reliability?
- **Efficiency and latency**: Multiple LLM calls and tool invocations may bottleneck real-time applications; can caching or speculative execution help?
- **Generalization to new tools**: Do tool compositions and agent strategies generalize when new tools are added? How to handle tool failures gracefully?
- **Scalability of multi-agent systems**: As agent count grows, does coordination overhead become prohibitive? Can emergent communication reduce coordination cost?

## Contradictions and debates

- **Interpretability vs. performance**: Earlier debate assumed interpretability trades off accuracy. Evidence suggests compositional approaches can exceed monolithic baselines while remaining interpretable.
- **Specialized vs. general agents**: Should we build task-specific agents or general-purpose orchestrators? Trend shows general orchestrators with task-specific tool selection.
- **Tool vs. module abstraction**: Should systems expose raw modules (detection, classification) or higher-level tools? Recent work suggests both are useful at different levels of abstraction.
- **Orchestration vs. end-to-end**: NitroGen bypasses LLM reasoning and tool composition entirely, learning direct vision-to-action mapping. This trades interpretability and planning for scalability and latency. Whether end-to-end agents can eventually match the compositional reasoning of orchestrator-based systems remains open.

## Recommended reading order

1. Start with [[papers/2022-visual-programming-compositional-visual-reasoning|VisProg]] to understand foundational neuro-symbolic approach
2. Read [[papers/2023-viper-gpt|ViperGPT]] for vision-specific program synthesis insights
3. Read [[papers/2024-llm-as-tool-makers|LATM]] to understand tool generation and cost optimization
4. Read [[papers/2025-tooleqa-embodied-question-answering|ToolEQA]] and [[papers/2025-vipact-visual-perception-agent|VipAct]] to see multi-agent evolution
5. Explore concept pages on [[concepts/program-synthesis-for-vision|program synthesis]], [[concepts/modular-composition|modular composition]], and [[concepts/code-generation-llms|code-generation LLMs]]

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
