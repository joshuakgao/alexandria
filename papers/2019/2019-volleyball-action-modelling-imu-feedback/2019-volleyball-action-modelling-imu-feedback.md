---
title: "Volleyball Action Modelling for Behavior Analysis and Interactive Multi-modal Feedback"
authors: [Fahim Salim, Fasih Haider, Sena Busra Yengec Tasdemir, Vahid Naghashi, Izem Tengiz, Kubra Cengiz, Tomas Ward]
year: 2019
venue: "eNTERFACE'19"
tags: [sports-analytics, video-understanding]
url: ""
pdf: "[[2019-volleyball-action-modelling-imu-feedback.pdf]]"
date_ingested: 2026-06-18
---

# Volleyball Action Modelling for Behavior Analysis and Interactive Multi-modal Feedback

![[2019-volleyball-action-modelling-imu-feedback-thumbnail.png]]

## Research gap

This paper presents an end-to-end system for automatic volleyball action detection using wrist-worn IMU sensors and classical ML classifiers, developed as part of the Smart Sports Exercises (SSE) project. Nine players (eight usable) wore dual-wrist IMU devices during regular training sessions, capturing accelerometer, gyroscope, magnetometer, and barometer data. The primary task is binary action/non-action classification; action type classification (forearm pass, overhead pass, serve, smash, etc.) is identified as future work.

## Contributions

- **Dual-wrist IMU evaluation**: explicit comparison of dominant vs. non-dominant vs. both-hands sensor placement for volleyball action classification
- **Multi-sensor modality comparison**: all four IMU sensor types (accelerometer, gyroscope, magnetometer, barometer) evaluated individually and in fusion combinations
- **Sensor fusion finding**: Acc+Mag fusion on both hands outperforms any single-sensor or single-hand configuration (86.9% UAR)
- **Non-dominant hand result**: under imbalanced training, non-dominant hand provides better UAR than dominant hand — contradicts the common assumption in sports IMU research that dominant-hand placement is optimal
- **Auto-tagging system prototype**: modular architecture (sensors → ML → Solr indexing → web frontend) for real-time event detection and coached video review

## Method

**Sensors:** Xsens MTw IMUs providing 3-axis accelerometer, 3-axis gyroscope, 3-axis magnetometer, and barometer (4 sensor types, up to 10 channels per hand).

**Feature extraction:** Time-domain statistical features — mean, standard deviation, median, mode, skewness, kurtosis — computed over 0.5-second windows with 50% overlap. 6 features × each sensor dimension per frame.

**Classifiers:** Decision Tree (DT, leaf=5), K-Nearest Neighbour (KNN, k=5), Naive Bayes (NB, kernel), Linear Discriminant Analysis (LDA), Support Vector Machine (SVM, linear kernel, C=0.5).

**Evaluation:** Leave-One-Subject-Out (LOSO) cross-validation. Two experimental setups:
- Experiment 1: balanced training (equal action/non-action samples), imbalanced test
- Experiment 2: imbalanced training and test (matches real deployment distribution)

**Metric:** Unweighted Average Recall (UAR) — arithmetic mean of per-class recall, appropriate for imbalanced datasets.

**System:** Two-stage classifier (action/non-action → action type, second stage is future work). Events indexed in Apache Solr; web frontend filters by player and action type, jumping to the relevant video timestamp on click.

## Datasets & evaluation

**Experiment 1 (balanced training):**
- Best single-sensor: accelerometer, dominant hand — 82.50% UAR (KNN)
- Sensor average: accelerometer best overall (81.91% DH, 80.41% NDH)
- Fusion (both hands): Acc+Mag — 82.52% UAR

**Experiment 2 (imbalanced training):**
- Best single-sensor: accelerometer, non-dominant hand — 83.99% UAR (NB)
- Fusion (both hands): Acc+Mag — **86.87% UAR** (best overall result)
- Overall fusion average (all sensors, both hands): 82.43%

Key finding: non-dominant hand outperforms dominant hand under imbalanced training (83.99% vs. 79.83%); dominant hand better under balanced training (82.50% vs. 81.71%). Suggests training data distribution interacts with hand dominance in ways not yet explained.

## Limitations

- Action type classification (forearm pass, overhead pass, serve, smash, etc.) remains future work — binary action/non-action is a prerequisite, not the final goal
- Small dataset (8 players, 990 action instances) raises concerns about generalisation; LOSO cross-validation addresses this partially but subject pool is limited
- Barometer is consistently the weakest sensor (UAR ~50–60%) — its inclusion seems to add noise rather than signal for volleyball actions; not explained why it was included
- The dominant vs. non-dominant hand interaction with training data balance is an unexplained finding — opens an interesting question about biomechanical asymmetry in volleyball
- System prototype is proof-of-concept only; latency, robustness, and integration with live match video are not evaluated

## Key takeaways

The study contributes both a machine learning evaluation (5 classifiers × 4 sensor modalities × 2 experimental setups) and a system-level contribution: a prototype auto-tagging architecture that indexes detected events in Apache Solr and exposes them via an HTML5/JavaScript web frontend for coached video review.

A notable finding is that sensor placement matters in non-obvious ways: under balanced training, the dominant hand provides marginally better UAR; under imbalanced (full) training, the non-dominant hand outperforms. Decision fusion of accelerometer and magnetometer sensors on both hands achieves the best overall UAR of 86.9% (Experiment 2, imbalanced training).

- **Koteda et al. (2025)** ([[papers/2025-realtime-volleyball-movement-classification-lda|Real-Time Volleyball Movement Classification using LDA]]): later volleyball IMU paper with a tighter focus — single wrist, 6-class action type recognition, 99.09% accuracy with LDA. Where Salim et al. treat action type classification as future work, Koteda et al. demonstrate it is feasible. Key difference: Koteda uses a single sensor position; Salim et al. show dual-wrist fusion is clearly beneficial.
- **Kautz et al. (2017)** [cited as [27]]: beach volleyball activity recognition with deep CNN on wrist IMU — one of the stronger baselines in this area; SGVLM notes that Salim et al. focus on a broader sensor comparison rather than competing on accuracy with deep methods.
