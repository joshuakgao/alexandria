---
title: "ClaimFlow: Tracing the Evolution of Scientific Claims in NLP"
authors: [Aniket Pramanick, Yufang Hou, Saif Mohammad, Iryna Gurevych]
year: 2026
tags: [claim-relation-classification, benchmarking]
url: ""
pdf: "[[2026-claimflow-claim-relation-classification.pdf]]"
date_ingested: 2026-06-18
---

# ClaimFlow: Tracing the Evolution of Scientific Claims in NLP

![[2026-claimflow-claim-relation-classification-thumbnail.png]]

## Research gap

ClaimFlow introduces the first large-scale, manually-annotated dataset linking scientific claims across NLP papers with labeled epistemic relations. The work curates 304 papers from the ACL Anthology (1979–2025) containing 1,084 claims and 832 cross-paper claim relations. Beyond the dataset, the paper defines **Claim Relation Classification**, a new task that predicts how a citing claim engages with a cited claim (support, extend, qualify, refute, or background reference) given the citation context. Evaluation of strong neural models and LLMs yields a baseline performance of 0.78 macro-F1. Applied at scale across ~13k NLP papers, the models reveal how scientific claims propagate, evolve, and are challenged over decades, uncovering fundamental patterns in how the field progresses.

## Contributions

- **ClaimFlow dataset**: 304 expert-annotated papers with 1,084 claims and 832 labeled claim relations, enabling fine-grained analysis of scientific engagement
- **Claim Relation Classification task**: Operationalizes claim-level scientific stance prediction from citation context, claims, and contextual evidence
- **Large-scale longitudinal analysis**: Automatically-inferred claim graph across ~13k NLP papers reveals claim propagation, challenge patterns, and structural evolution of scientific ideas across 50+ years

## Method

The task takes as input a cited claim from an earlier paper, a citing claim from a later paper, and the citation context (citation sentence with surrounding context). The goal is to predict the epistemic relation between the claims.

**Relation taxonomy** (grounded in argumentation theory and scientific discourse analysis):
- **support**: citing claim provides evidence reinforcing the cited claim
- **extend**: citing claim applies the cited claim to new settings/data/tasks
- **qualify**: citing claim restricts the scope or specifies conditions for the cited claim
- **refute**: citing claim presents contradicting evidence or arguments
- **background**: cited claim mentioned for context without substantive evaluation

**Models evaluated**:
- Encoder-based: BERT, RoBERTa, SciBERT (fine-tuned on ClaimFlow)
- LLMs: GPT-4o, GPT-3.5-turbo, LLaMA-3-70B, Mixtral-8x7B (zero-shot and few-shot)

Annotations were performed by two expert NLP researchers with inter-annotator agreement (Cohen's κ = 0.75 for claim relations, Krippendorff's α = 0.72 for claim identification).

## Datasets & evaluation

**Model performance** (macro-F1 on test set):
- RoBERTa fine-tuned: 0.70
- SciBERT fine-tuned: 0.75
- GPT-4o few-shot: 0.78 (best overall)

**Error analysis**: Models struggle with distinguish extend vs. qualify (~30% of errors) and implicit stance expressions (~20%). All three input components (cited claim, context, citing claim) are needed for best performance.

**Large-scale findings** (ClaimFlow-AutoGraph, ~13k papers):
- 63.5% of claims remain isolated (never reused)
- 11.1% of claims are ever challenged (qualify or refute)
- Among challenged claims, qualification (8.3%) is more common than direct refutation (2.8%)
- Widely-propagated claims tend to be reshaped through qualification and extension rather than confirmed or refuted
- Early claims exert disproportionate normalized influence (ρ = −0.495 correlation between claim age and engagement)
- Claim-claim interaction graph remains sparse but increasingly stratified over time
- Engagement patterns are consistent across major NLP venues (ACL, EMNLP, NAACL, EACL, AACL)

## Limitations

- Analysis focuses on abstracts, introductions, and conclusions; full-text claim analysis remains a future direction
- Limited to ACL Anthology venues; important contributions in other conferences and preprints are underrepresented
- Automatically-inferred large-scale claim graph relies on models with ~78% accuracy; trend-level conclusions are reliable but individual predictions may err
- Future work: expanding ClaimFlow to other scientific domains, bootstrapping larger claim graphs, integrating with model performance trends to predict research trajectories

## Key takeaways

This work extends **citation analysis** (which typically operate at sentence or paper level) by introducing claim-level analysis. It builds on prior work in **scientific claim extraction** and **argumentative structure modeling**, but uniquely tracks how claims interact across papers rather than treating them in isolation. It complements longitudinal studies of NLP research (e.g., paradigm shifts, topic trends) by offering a claim-centric lens.
