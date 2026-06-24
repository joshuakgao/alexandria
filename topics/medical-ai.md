---
topic: Medical AI
slug: medical-ai
---

# Medical AI

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "medical-ai")
SORT year DESC
```

## Overview
Medical AI encompasses the application of machine learning — particularly vision-language models, computer vision, and large language models — to clinical tasks such as radiology interpretation, pathology analysis, dermatological diagnosis, and clinical question answering. The field is characterized by high-stakes deployment requirements, domain-specific evaluation challenges, and a growing tension between benchmark performance and genuine clinical utility. Recent work has exposed fundamental vulnerabilities in how multimodal AI systems are evaluated in medical contexts, revealing that textual priors and benchmark structure can inflate apparent visual understanding.

## Trends
- Frontier VLMs achieve high scores on medical visual QA benchmarks (VQA-Rad, MicroVQA, MedXpertQA-MM, ReXVQA), but much of this performance may be driven by textual cues and population-level priors rather than genuine image understanding (Mirage, 2026).
- Medical benchmarks are more susceptible to non-visual inference than general-purpose benchmarks, because medical questions are statistics-dominated and structurally patterned.
- The "mirage effect" — models confidently describing non-existent images — is pathology-biased in medical contexts, with time-sensitive diagnoses (STEMI, melanoma, carcinoma) disproportionately generated as defaults.
- Private benchmarks and post-hoc filtering frameworks (e.g., B-Clean) are emerging as necessary tools for fair evaluation of medical AI systems.

## Open questions
- How can medical AI benchmarks be designed to genuinely require visual grounding, given that population-level statistics provide strong textual priors?
- What safeguards are needed to prevent silent failures (mirage-mode reasoning) in deployed clinical AI systems?
- Can architectural interventions at inference time detect and prevent mirage behavior, complementing evaluation-level fixes?
- How should clinical trust in AI reasoning traces be calibrated, given that text-only models can produce reasoning indistinguishable from image-grounded reasoning?
