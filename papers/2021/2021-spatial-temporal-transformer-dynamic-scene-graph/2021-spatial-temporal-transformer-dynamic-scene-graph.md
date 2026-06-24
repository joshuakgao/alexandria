---
title: "Spatial-Temporal Transformer for Dynamic Scene Graph Generation"
authors: [Yuren Cong, Wentong Liao, Hanno Ackermann, Bodo Rosenhahn, Michael Ying Yang]
year: 2021
venue: "ICCV 2021"
tags: [dynamic-scene-graph-generation, scene-graphs, video-understanding]
url: ""
pdf: "[[2021-spatial-temporal-transformer-dynamic-scene-graph.pdf]]"
date_ingested: 2026-06-18
---

# Spatial-Temporal Transformer for Dynamic Scene Graph Generation

![[2021-spatial-temporal-transformer-dynamic-scene-graph-thumbnail.png]]

## Research gap

This paper introduces STTran (Spatial-Temporal Transformer), a neural architecture for generating dynamic scene graphs from video sequences. Unlike static scene graph generation from images, dynamic scene graph generation must capture both spatial relationships within frames and temporal dependencies across frames. STTran comprises two core modules: a spatial encoder that extracts spatial context and reasons about visual relationships within individual frames, and a temporal decoder that captures temporal dependencies between frames to infer dynamic relationships. The method handles variable-length videos without clipping and achieves state-of-the-art results on the Action Genome benchmark dataset.

## Contributions

- Novel spatial-temporal transformer architecture combining spatial encoding and temporal decoding for video scene graphs
- Application of multi-label classification for relationship prediction, acknowledging that multiple relationships can co-occur between object pairs
- Strategy for generating confident dynamic scene graph predictions with structured temporal reasoning
- Demonstrates that temporal dependencies significantly improve relationship prediction accuracy

## Method

**Spatial Encoder**: Processes individual video frames to extract spatial context. Uses object detection to identify entities and encodes spatial relationships between detected objects within the frame using transformer-based spatial context modeling.

**Temporal Decoder**: Takes encoded spatial representations from multiple frames and models temporal dependencies to predict dynamic relationships. The decoder refines object and relationship representations by attending across temporal sequences, enabling prediction of how relationships evolve over time.

**Multi-label Relationship Prediction**: Rather than single-label classification (one relationship per object pair), the method supports multiple concurrent relationships, reflecting real-world scenarios where object pairs can interact in multiple ways simultaneously.

## Datasets & evaluation

- Achieves state-of-the-art performance on Action Genome dataset for dynamic scene graph generation
- Ablation studies confirm the positive contribution of temporal dependencies on relationship prediction
- Outperforms methods that simply apply static scene graph generation independently to each frame
- Demonstrates the importance of structured temporal reasoning for video understanding

## Limitations

- Scalability to longer, more complex videos with many objects and relationships
- Generalization to diverse video domains beyond human-object interactions
- Computational complexity of temporal modeling across frames
- How to handle occlusions and object tracking errors in dynamic scenarios

## Key takeaways

Builds on the scene graph formalism established in prior work for static images but extends it to the video domain. Distinguishes itself from action recognition approaches by providing structured decomposition of activities into object-relationship graphs rather than monolithic event labels. Adopts the Transformer architecture from vision tasks like DETR and applies it specifically to the spatial-temporal scene graph problem.
