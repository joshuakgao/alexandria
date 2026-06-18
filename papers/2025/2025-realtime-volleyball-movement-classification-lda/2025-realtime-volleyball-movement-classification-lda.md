---
title: "Real-Time Volleyball Movement Classification using Linear Discriminant Analysis"
authors: [Tarun Kumar Koteda, Uvarasi U., Revathi M., Boopathy K., Muthulekshmi M., Don Chris Allan B.]
year: 2025
venue: "IC2NC 2025 (IEEE International Conference on NexGen Networks and Cybernetics)"
tags: [sports-analytics]
url: ""
pdf: "[[2025-realtime-volleyball-movement-classification-lda.pdf]]"
date_ingested: 2026-06-18
---

# Real-Time Volleyball Movement Classification using Linear Discriminant Analysis

![[2025-realtime-volleyball-movement-classification-lda-thumbnail.png]]

## Research gap

This paper presents a real-time volleyball movement classification system using Linear Discriminant Analysis (LDA) on wearable IMU sensor data. The system classifies six fundamental volleyball actions — Dig, Spike, Serve, Block, Setting, and Spiking — from 6-axis inertial measurements (3-axis accelerometer + 3-axis gyroscope) worn by athletes. Using the Volleyball Training Feedback Dataset (462 labelled samples from Kaggle), the pipeline extracts statistical features per fixed-length time window, applies LDA for simultaneous dimensionality reduction and classification, and achieves 99.09% overall accuracy with an F1 score of 98.17%.

## Contributions

- End-to-end pipeline from raw IMU signal to real-time movement label, deployable on embedded hardware
- Feature set of 6 statistical descriptors per sensor axis: mean, standard deviation, RMS, skewness, energy, signal magnitude area (SMA), and inter-axis correlation
- Empirical validation that LDA outperforms more complex models on this dataset while remaining interpretable and computationally trivial
- Application of the Within-Class and Between-Class scatter matrix formulation to maximize class separability across all 6 volleyball movement types

## Method

**Pipeline:** Sensor Input → Preprocessing (noise filtering, normalization) → Feature Extraction → LDA → Movement Label

**Sensors:** 3-axis accelerometer + 3-axis gyroscope (6 DoF IMU), worn by athletes during practice sessions. Data collected at a fixed sampling rate; time-series partitioned into fixed-size sliding windows (2–2.5 seconds, 50% overlap).

**Feature extraction:** From each window, 6 statistical features are computed per sensor axis (42 features total across 7 axes). These features encapsulate fundamental biomechanical properties of each movement.

**LDA objective:** Find the projection that maximises the ratio $\frac{S_B}{S_W}$ (Between-Class scatter / Within-Class scatter), reducing the 42-dimensional feature space to a compact subspace that best separates the 6 movement classes.

**Dataset:** Volleyball Training Feedback Dataset — 462 labelled samples, 6 movement classes. Train/test split: 70:30 (balanced class distribution).

## Datasets & evaluation

![[raw/sports-analytics/2025/assets/volleyball-lda-fig2-confusion-matrix.png]]
*Figure 2: Confusion matrix — near-perfect classification across all 6 movement classes*

![[raw/sports-analytics/2025/assets/volleyball-lda-fig4-performance-metrics.png]]
*Figure 4: LDA performance metrics by movement class — consistent across configurations*

| Metric | Score |
|--------|-------|
| Accuracy | 99.09% |
| Precision | 99.17% |
| Recall | 98.19% |
| F1 Score | 98.17% |

ROC curves show AUC ≈ 1.0 across all movement classes, indicating exceptional discriminative power. Performance is consistent regardless of LDA configuration tested.

Notable biomechanical findings from feature importance analysis: jump height, angles, feedback score, and recommended performance modifications are the most discriminative features. Vital signs (heart rate, temperature) and gyroscopic data contribute little to classification.

## Limitations

- Dataset is small (462 samples) and collected under controlled training conditions — generalisability to in-game situations is untested
- LDA assumes Gaussian class distributions and linear separability; performance may degrade with noisier in-the-field sensor data or unfamiliar player styles
- No comparison against deep learning baselines on the same dataset and split, making the claimed superiority over complex models unverified
- Sensor placement details (ankle, wrist, torso?) are not fully specified, which limits reproducibility
- The paper does not address player-independent evaluation — models may overfit to specific players' movement signatures in the training set

## Key takeaways

The stated motivation is lightweight, real-time deployment without cloud infrastructure — LDA's low computational cost makes it suitable for edge devices such as Arduino boards or Raspberry Pi with wireless IMU modules.

- Prior volleyball analytics work (cited: U. Gang et al., 2024; T. Wang et al., 2025) uses deep learning (CNNs, LSTMs) with video or trajectory data; this paper argues LDA is competitive and more deployable on resource-constrained hardware
- Concurrent sports-HAR work applies SVM and deep learning to similar wearable sensor setups; LDA is positioned as simpler and faster at inference
- The Volleyball Training Feedback Dataset is shared with prior feedback system work (referenced as Kaggle dataset nj4y07); comparability across papers using this dataset is limited by unreported preprocessing differences
