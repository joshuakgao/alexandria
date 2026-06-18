---
title: "Towards Effective Extraction and Evaluation of Factual Claims"
authors: [Dasha Metropolitansky, Jonathan Larson]
year: 2025
tags: [claim-extraction, vision-language-models]
url: ""
pdf: "[[2025-claimify-claim-extraction.pdf]]"
date_ingested: 2026-06-18
---

# Towards Effective Extraction and Evaluation of Factual Claims

![[2025-claimify-claim-extraction-thumbnail.png]]

## Research gap

This paper addresses the critical gap in claim extraction evaluation for fact-checking systems. While decompose-then-verify approaches (extracting claims then verifying them independently) are standard in LLM fact-checking, the quality of extracted claims directly impacts fact-checking effectiveness. The authors propose a comprehensive evaluation framework based on three dimensions: entailment (faithfulness to source), coverage (capturing verifiable information), and decontextualization (claims usable for fact-checking without additional context). They introduce **Claimify**, an LLM-based method that uniquely handles ambiguity by identifying referential and structural ambiguities and determining whether they can be resolved from context. Claimify uses a four-stage pipeline (selection, disambiguation, decomposition) and demonstrates superior performance compared to five baselines on the BingCheck dataset.

## Contributions

- **Comprehensive evaluation framework**: Three-dimensional assessment (entailment, element-level coverage, outcome-based decontextualization) with automated and human-validated metrics
- **Novel element-level coverage metric**: Granular assessment of whether extracted claims cover distinct pieces of information (verifiable elements) from source sentences
- **Outcome-based decontextualization evaluation**: Pragmatic approach measuring whether missing context affects fact-checking verdicts, rather than subjective judgments of sufficiency
- **Claimify method**: First claim extraction system to explicitly identify unresolvable ambiguities and abstain from extraction when interpretations are uncertain

## Method

**Claimify pipeline** (4 stages):

1. **Selection**: LLM determines if sentence contains verifiable content; rewrites to retain only verifiable components
2. **Disambiguation**: Identifies referential ambiguity (unclear references) and structural ambiguity (multiple grammatical interpretations); determines if ambiguity can be resolved using question and context; abstains if unresolvable (≤5.4% of sentences)
3. **Decomposition**: Decomposes disambiguated sentences into decontextualized factual claims; uses bracketed notation `[inferred info]` to flag context-dependent inferences
4. **Context handling**: Separate configurable context windows (p preceding, f following sentences) for each stage

**Evaluation framework**:
- **Entailment**: Uses NLI-based classification to verify claims are entailed by source
- **Element-level coverage**: Segments sentences into verifiable/unverifiable elements; evaluates whether each element is covered (explicitly or implicitly) in extracted claims; computes precision/recall on elements
- **Decontextualization**: Three-step process: (1) generate maximally decontextualized version c_max; (2) retrieve evidence for both c and c_max; (3) check verdict alignment; classifies results as desirable/undesirable based on whether verdicts match

## Datasets & evaluation

**Baseline methods tested**: AFaCTA, Factcheck-GPT, VeriScore, DnD, SAFE

**Dataset**: BingCheck (396 answers from Microsoft Copilot), 6,490 annotated sentences

**Key findings** (on gpt-4o model):
- Claimify outperforms all baselines on element-level coverage metrics
- Disambiguation stage is novel to Claimify; 2.4-5.4% of sentences labeled "Cannot be disambiguated" across different LLMs (gpt-4o: 3.2%)
- Outcome-based decontextualization evaluation reveals methods differ significantly in producing verdict-aligned claims
- Only 0.8% of sentences return no claims in decomposition stage, indicating high extraction rate when disambiguation succeeds

**Comparison to related work**: Claimify is first to explicitly handle ambiguity identification and resolution; other methods either ignore ambiguity or assume it is always resolvable.

## Limitations

- Evaluation heavily relies on LLM-based assessment; human annotations used primarily for sentence-level claim presence (not element-level verification)
- Focus on QA-pair format (question-answer); generalization to other text types (news articles, documents) remains unexplored
- Context window sizes and metadata integration are configurable but not extensively tuned
- Bracketed notation for inferred content is useful for transparency but may impact downstream fact-checking retrieval
- Outcome-based decontextualization requires access to evidence collection and fact-checking system; portability to other systems unclear

## Key takeaways

This work complements **claim relation classification** (e.g., ClaimFlow) by focusing on the upstream step of extracting high-quality claims. Builds on prior work on:
- **Claim detection**: Sentence-level classification of whether content is verifiable (Ni et al., 2024; Daxenberger et al., 2017)
- **Decomposition**: Breaking sentences into atomic claims (Min et al., 2023; Chen et al., 2023a)
- **Decontextualization evaluation**: Prior work relied on subjective human judgments; this paper proposes outcome-based metrics grounded in fact-checking performance

Differs from "check-worthiness" ranking approaches that prioritize sentences by importance rather than verifiability.
