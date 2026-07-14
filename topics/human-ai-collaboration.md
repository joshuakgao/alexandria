---
topic: Human-AI Collaboration
slug: human-ai-collaboration
---

# Human-AI Collaboration

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "human-ai-collaboration")
SORT year DESC
```

## Overview
Human-AI collaboration research studies how to build AI agents that work effectively with human partners in shared tasks. Unlike purely competitive or self-play settings, collaboration requires agents to adapt to the diverse strategies, skill levels, and preferences of human co-players — often in a zero-shot manner without prior interaction. The central challenge is that standard multi-agent training (self-play, population play) produces agents that overfit to their training partners, while human-data-driven approaches (behavioral cloning play) are expensive and fragile.

## Trends
- **Diversity over data**: Fictitious Co-Play (Strouse et al., NeurIPS 2021) shows that training against a diverse pool of self-play agents and their checkpoints produces better human collaborators than training with actual human data, establishing partner diversity as the key ingredient for zero-shot coordination.
- **Adaptive convention following**: Effective human-AI collaboration requires agents that detect and follow human conventions rather than imposing their own — a qualitative behavioral difference from agents optimized purely for task performance.
- **Situated ToM for collaboration**: MindCraft (Bara et al., EMNLP 2021) demonstrates that effective collaboration under asymmetric knowledge and skills requires agents to model partners' beliefs, knowledge, and goals — and that different belief types require different modalities (visual observation for task completion, dialogue for partner goals). This grounds human-AI collaboration in theory-of-mind capabilities beyond simple behavioral matching.

## Open questions
- Whether FCP-style diversity training scales beyond gridworld to continuous, high-dimensional, or real-world collaborative tasks.
- How to combine offline diversity training (FCP) with online adaptation to specific human partners during interaction.
- Whether theory-of-mind architectures can further improve zero-shot human-AI coordination by explicitly modeling partner goals and capabilities — MindCraft shows that passive observation is insufficient for goal inference, suggesting agents need active dialogue strategies.
- How to evaluate collaborative AI beyond task performance — subjective human preference, trust, and communication quality remain underexplored metrics.
- How to build agents that actively manage common ground through dialogue rather than passively observing — MindCraft's finding that less communication leads to more disagreement highlights dialogue initiative as a key capability.
