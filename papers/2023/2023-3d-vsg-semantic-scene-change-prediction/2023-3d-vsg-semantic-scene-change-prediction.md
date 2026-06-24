---
title: "3D VSG: Long-term Semantic Scene Change Prediction through 3D Variable Scene Graphs"
authors: [Samuel Looper, Javier Rodriguez-Puigvert, Roland Siegwart, Cesar Cadena, Lukas Schmid]
year: 2023
venue: "ICRA 2023"
tags: [construction-monitoring, scene-graphs, change-detection]
url: ""
pdf: "[[2023-3d-vsg-semantic-scene-change-prediction.pdf]]"
date_ingested: 2026-06-18
---

# 3D VSG: Long-term Semantic Scene Change Prediction through 3D Variable Scene Graphs

![[2023-3d-vsg-semantic-scene-change-prediction-thumbnail.png]]

## Research gap

This paper extends 3D Scene Graphs (SGs) to model and predict long-term semantic scene changes in environments shared with other agents. Traditional SGs assume static scene semantics; this work introduces Variable Scene Graphs (VSGs) that augment SGs with "variability" attributes representing likelihoods of discrete change events. The paper formalizes semantic scene variability estimation—the task of predicting which objects are likely to change (position, semantic state, or composition). The authors present DeltaVSG, a graph-based learning method for VSG estimation evaluated on the 3RScan long-term indoor dataset. VSGs enable predictive planning: robots can anticipate scene changes and plan more efficiently. Results show DeltaVSG achieves 77.1% accuracy and 72.3% recall in predicting scene changes, with 66% speedup in active robotic change detection compared to scene-change-unaware baselines. The approach demonstrates that incorporating domain knowledge about likely changes (e.g., mugs typically near tables) improves change prediction reliability.

## Contributions

- **Variable Scene Graphs**: Novel representation extending 3D SGs with variability attributes for modeling long-term semantic scene changes
- **Scene variability estimation**: Formalizes task of predicting change likelihood for individual objects given a scene
- **DeltaVSG method**: Graph-based learning approach for estimating VSGs from standard SGs with supervised training
- **Predictive change detection**: Enables robots to plan more efficiently by anticipating likely scene modifications
- **Real-world validation**: Demonstrates improvements on 3RScan dataset with human-interpretable change predictions

## Method

**Variable Scene Graph representation**:
- Extends traditional SGs (vertices = objects, edges = relationships) with variability attribute per object
- Variability encodes likelihood of discrete change events: position change, semantic state change, composition change
- Graph structure preserves semantic and geometric relationships critical for predicting consistent changes

**DeltaVSG training**:
1. Takes standard 3D SG as input
2. Predicts variability for each object using graph neural networks
3. Incorporates local scene context (object properties, relationships, spatial layout)
4. Leverages human intuition: objects like fridges unlikely to disappear; handheld objects likely to move

**Key insight**: Change likelihoods depend on semantic roles and spatial relationships, not global statistics alone.

## Datasets & evaluation

- **3RScan dataset**: 77.1% accuracy, 72.3% recall for variability prediction
- **Active change detection**: 66% speedup in task completion when planning with predicted changes
- **Interpretability**: Predictions align with human expectations (e.g., mugs near tables, stable objects remain fixed)
- **Cross-scene generalization**: VSG predictions transfer to unseen environments

## Limitations

- How do VSGs scale to very large scenes with hundreds of objects?
- Can variability predictions transfer across environments with different object distributions?
- How sensitive is performance to errors in initial SG construction?
- Can VSGs incorporate temporal trends (e.g., objects drift toward walls over time)?
- How to handle extremely rare or novel change types not seen in training?

## Key takeaways

Extends work on 3D Scene Graphs (Armeni et al., Wald et al., Rosinol et al.) which assume static semantics. Related to dynamic SGs (Rosinol et al.) for short-term dynamics but focuses on long-term discrete changes. Differs from prior change detection work by predicting rather than reacting to changes, enabling proactive planning.
