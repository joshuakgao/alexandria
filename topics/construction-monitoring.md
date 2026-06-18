---
topic: Construction Monitoring
slug: construction-monitoring
---

# Construction Monitoring

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "construction-monitoring")
SORT year DESC
```

## Overview

# Construction Monitoring — Overview

*Last updated: 2026-04-07 | Sources: 5 papers*

## Current thesis

Construction monitoring is evolving from purely geometric change detection toward semantically-aware frameworks that integrate uncertainty quantification and predictive modeling. Early work (2022) established voxel-based change detection with explicit measurement uncertainty and BIM verification; mid-period work (2022-2023) extended this with semantic segmentation for object-level interpretation; recent developments (2023-2024) add predictive capabilities (anticipating likely scene changes) and specialized semantic reasoning (six semantic change types). The field increasingly recognizes that detection alone is insufficient—interpretation of change meaning (semantic type, significance relative to measurement uncertainty, impact on project schedule) is essential for practical construction management.

## Key trends

**2022 — Foundation: Geometric + Uncertainty**
- [[papers/2022-change-detection-indoor-construction-bim|Meyer et al.]] introduce voxel-based change detection with Dempster-Shafer uncertainty quantification for indoor construction. Key insight: measurement uncertainty must be modeled explicitly; significance of detected changes evaluated against BIM Level of Accuracy spec, not arbitrary thresholds.
- [[papers/2022-semantics-aided-3d-change-detection-construction|Huang et al.]] propose two-stage framework combining occupancy-based geometry detection with semantic segmentation. First major work to systematically integrate geometric and semantic change analysis for construction.

**2023 — Expansion: Reviews & Prediction**
- [[papers/2023-change-detection-urban-objects-review|Stilla & Xu]] publish comprehensive review covering registration, variance estimation, change analysis techniques across urban applications (construction automation, building investigation, vegetation monitoring). Identifies persistent gaps: dataset reliability, uncertainty propagation, semantic integration, theory-practice disconnect.
- [[papers/2023-3d-vsg-semantic-scene-change-prediction|Looper et al.]] introduce Variable Scene Graphs (VSGs) for predicting long-term scene changes. Major paradigm shift: from detecting past changes to predicting future changes, enabling proactive planning. Shows 77.1% accuracy in predicting scene variability.

**2024 — Specialization: Semantic Categorization**
- [[papers/2024-3d-change-detection-construction-scenarios|Ge & Liu]] propose six semantic change types (appeared, disappeared, partially disappeared, growth, moved, rotated) with type-specific quantization methods. Advances beyond binary "changed/unchanged" to nuanced change characterization. Improves construction ontology: construction progress now interpreted as sequence of semantically-typed changes.

**Evolution summary**: Geometric change detection (2022) → Semantic integration (2022-2023) → Predictive capabilities (2023) → Specialized semantic typing (2024). Each phase adds interpretability layer enabling more actionable construction insights.

## Open problems

- **Uncertainty propagation**: How to rigorously propagate measurement uncertainty, registration error, and segmentation uncertainty through multi-stage pipelines?
- **Semantic robustness**: How robust are semantic change categorizations (6 types) to variation in construction styles, object orientations, and complex spatial configurations?
- **Prediction generalization**: Do VSG-based predictions of scene changes transfer across construction types, geographies, and temporal scales?
- **Real-time scalability**: Can uncertainty-aware, semantic-integrated change detection operate in real-time on large construction sites with thousands of objects?
- **Bridging BIM-reality gap**: How to handle BIM incompleteness, inaccuracy, and updates that lag construction progress?
- **Temporary vs. permanent**: Distinguishing construction-driven changes (progress) from temporary objects (scaffolding, equipment) remains unsolved; complicates automated progress calculation
- **Multi-object relationships**: How to reason about changes in spatial relationships and dependencies between objects (e.g., one element supporting another)?

## Contradictions and debates

- **Geometry vs. semantics**: Early work favored pure geometric detection (voxel-based, surface distance) for objectivity; recent work argues semantics essential for interpretation. Evidence supports both: geometry robust to classification errors, but semantics necessary for actionable insights.
- **Uncertainty burden**: Some practitioners view rigorous uncertainty quantification (Dempster-Shafer, belief functions) as computationally expensive for marginal gain; others argue explicit uncertainty prevents costly false positives. Trade-off depends on application criticality.
- **Prediction vs. detection**: Is construction monitoring primarily a detection problem (what changed?) or prediction problem (what will change?)? Review work emphasizes detection; recent trend toward prediction for proactive management.
- **UAV vs. TLS**: UAV photogrammetry offers accessibility and scalability; TLS offers accuracy and indoor capability. No consensus on optimal platform; application-dependent.

## Recommended reading order

1. Start with [[papers/2022-change-detection-indoor-construction-bim|Meyer et al.]] to understand foundational voxel-based change detection with uncertainty quantification
2. Read [[papers/2022-semantics-aided-3d-change-detection-construction|Huang et al.]] for semantic + geometric integration approach
3. Consult [[papers/2023-change-detection-urban-objects-review|Stilla & Xu review]] to contextualize techniques and applications across domains
4. Study [[papers/2023-3d-vsg-semantic-scene-change-prediction|Looper et al.]] for paradigm shift to predictive modeling and scene graphs
5. Examine [[papers/2024-3d-change-detection-construction-scenarios|Ge & Liu]] for latest specialized semantic categorization approach
6. Explore concept pages: [[concepts/construction-progress-monitoring|Construction Progress Monitoring]], [[concepts/change-detection|Change Detection]], [[concepts/uncertainty-quantification|Uncertainty Quantification]]

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
