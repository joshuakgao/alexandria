---
topic: Claim Extraction
slug: claim-extraction
---

# Claim Extraction

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "claim-extraction")
SORT year DESC
```

## Overview

*Last updated: 2026-04-22 | Sources: 1 paper*

## Current thesis

Effective claim extraction requires not just decomposing text into claims, but ensuring those claims are verifiable and properly contextualized for fact-checking pipelines. The bottleneck is not in claim identification (most prior methods handle this adequately) but in handling ambiguity and achieving appropriate decontextualization. Methods that explicitly identify and abstain from extracting when ambiguity is unresolvable produce higher-quality claim sets that lead to more reliable fact-checking verdicts.

## Key trends

- **From subjective to outcome-based evaluation**: Shift from hand-crafted heuristics and subjective human judgment of decontextualization quality toward metrics grounded in fact-checking performance
- **Fine-grained coverage metrics**: Moving beyond sentence-level binary classification (sentence contains claim or not) to element-level assessment that captures which specific information units are covered
- **Explicit ambiguity handling**: Recognizing that LLMs can identify when language is ambiguous and when context cannot resolve that ambiguity; abstaining from extraction when confidence is insufficient
- **QA format as standard**: Emphasis on question-answer pairs (particularly LLM-generated content) as the target text type, reflecting real-world usage patterns

## Open problems

- **Generalization beyond QA format**: How do these methods perform on other text types (news articles, technical documents, scientific papers)?
- **Scalability of element-level evaluation**: Can element-level coverage evaluation be automated more reliably without heavy human involvement?
- **Outcome-based evaluation portability**: How to apply outcome-based decontextualization metrics when fact-checking infrastructure varies?
- **Ambiguity resolution thresholds**: What constitutes "likely reader agreement" on ambiguity resolution? How sensitive are results to this threshold?
- **Interaction with downstream fact-checkers**: How do claim extraction method improvements translate to end-to-end fact-checking performance?

## Contradictions and debates

- **Atomicity vs. pragmatism**: Prior work pursued maximal claim atomicity (breaking claims into smallest units); this work argues atomicity doesn't correlate with fact-checking performance and deprioritizes it in favor of pragmatic decontextualization
- **Implicit vs. explicit coverage**: Is implicit coverage of verifiable elements acceptable? Claimify treats it as true positive (semantic equivalence), but downstream use may suffer if context is not explicit
- **Ambiguity abstention vs. best-effort resolution**: Is it better to abstain from extraction (Claimify) or always attempt extraction and let downstream systems handle uncertainty?

## Recommended reading order

For someone new to this topic:
1. [[papers/2025-claimify-claim-extraction|Claimify]] — start with abstract and Section 2 (evaluation framework) to understand the landscape; Section 3 describes the Claimify method; Section 5 details experimental setup and results
2. [[concepts/element-level-coverage|Element-Level Coverage]] — understand why sentence-level evaluation is insufficient and how element-level metrics improve assessment
3. [[concepts/decontextualization|Decontextualization]] — see how outcome-based evaluation differs from subjective approaches and why it matters for fact-checking
4. [[concepts/ambiguity-handling|Ambiguity Handling]] — recognize that explicit ambiguity identification is novel and valuable for conservative, high-confidence extraction

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
