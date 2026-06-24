---
title: "MovieQA: Understanding Stories in Movies through Question-Answering"
authors: [Makarand Tapaswi, Yukun Zhu, Rainer Stiefelhagen Antonio Torralba, Raquel Urtasun, Sanja Fidler]
year: 2016
venue: "CVPR 2016"
tags: [video-understanding, vision-language-models]
url: ""
pdf: "[[2016-movieqa.pdf]]"
date_ingested: 2026-06-18
---

# MovieQA: Understanding Stories in Movies through Question-Answering

![[2016-movieqa-thumbnail.png]]

## Research gap

MovieQA introduces a large-scale benchmark for evaluating automatic story comprehension from movies. The dataset contains 14,944 multiple-choice questions (each with one correct and four deceiving answers) sourced from 408 movies with high semantic diversity. What distinguishes this work is its multimodal nature: questions can be answered using multiple information sources—video clips, subtitles, scripts, plot synopses, and DVS (Described Video Service) transcriptions. The questions span a spectrum from simple "Who did What to Whom" type questions (answerable from vision alone) to complex "Why" and "How" questions requiring reasoning over both visual and linguistic information. For 140 movies (6,462 questions), temporal annotations localize questions in the timeline, enabling precise video-language alignment.

## Contributions

- Large-scale multimodal QA dataset bridging video and text understanding
- Multiple information sources enabling analysis of complementary modalities
- Temporal annotations grounding questions in video timeline
- Benchmark baselines showing that semantic story comprehension remains challenging
- Online leaderboard at movieqa.cs.toronto.edu encouraging community participation

## Method

The dataset was collected by having annotators create quizzes about movies using plot synopses as reference, which naturally biased annotation toward story-centric questions rather than visual pattern recognition. The movies were sourced from publicly available subtitled content; scripts were crawled from imsdb.com, and extended plot synopses came from Wikipedia. For video annotation, 140 movies were annotated with temporal information, with an average clip duration of 202.7 seconds and average of 46.3 shots per question-answer pair. The dataset includes institutional splits (train/val/test) with careful curation to ensure high-quality questions.

## Datasets & evaluation

The paper provides detailed statistics on question and answer length, question type distribution, and dataset composition across modalities. Baseline experiments extending existing QA techniques (e.g., LSTM-based approaches) to the MovieQA setting demonstrate that the task is challenging, with significant room for improvement. The authors show that no single modality is sufficient—questions often require integration of visual content with dialogue and narrative description.

## Limitations

The paper notes that while temporal annotations exist for 140 movies, the remaining 268 movies lack precise timestamp information, limiting analysis of temporal reasoning. The use of plot synopses during annotation may bias questions toward plot-centric rather than cinematically-nuanced interpretation. The multimodal nature means different questions may have optimal solutions using different modalities, raising the question of how models should learn to select appropriate information sources.

## Key takeaways

MovieQA extends prior work on visual QA (VQA, CLEVR, etc.) to the temporal domain and significantly longer temporal sequences. Unlike image-based QA that focuses on object recognition, counting, and scene understanding, MovieQA requires high-level semantic reasoning about character motivation, plot development, and causal relationships. It builds on video description work (e.g., MovieDescription dataset) but shifts focus from captioning to comprehension.
