---
topic: Sports Analytics
slug: sports-analytics
---

# Sports Analytics

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "sports-analytics")
SORT year DESC
```

## Overview

# Sports Analytics — Overview

*Last updated: 2026-06-25 | Sources: 6 papers*

## Current thesis

Sports analytics with ML is bifurcating into two tracks: heavyweight deep learning on video/trajectory data for post-hoc analysis, and lightweight classical ML on wearable sensor data for real-time, on-device feedback. A third track in broadcast video understanding has evolved through three generations of jersey number recognition: detection-based (Liu & Bhanu, CVPRW 2019, pose-guided R-CNN), STR-based (Koshkina & Elder, CVPRW 2024, PARSeq), and single-stage uncertainty-aware (Grad, CVPRW 2025, digit-compositional ViT with Dirichlet uncertainty). The field is converging on end-to-end architectures that eliminate multi-stage error propagation, with uncertainty quantification emerging as a key capability for handling ambiguous frames. Within the wearable track, a further distinction is emerging between battery-powered IMU sensors (2019, 2025) and energy-harvesting piezoelectric sensors (2022). IMU-based approaches prioritize multi-axis kinematics and multi-class action recognition; piezoelectric approaches prioritize maintenance-free long-duration operation and direct motion-to-electricity conversion. Volleyball is emerging as a useful benchmark domain with converging results across both modalities (2019–2025), enabling comparative analysis of sensor technology, algorithm approach, and practical deployment considerations.

## Key trends

**2019:**
- **Multi-sensor IMU fusion for volleyball** established that dual-wrist sensor placement and Acc+Mag fusion provides meaningfully better action detection than single sensors or single wrists (86.9% UAR). Naive Bayes outperforms LDA and SVM for imbalanced training settings.
- **System integration was an early concern**: Salim et al. built a full pipeline from sensor to web frontend, demonstrating that event auto-tagging for coached video review was technically feasible even with 2019-era hardware.
- **Non-dominant hand is underrated**: the finding that the non-dominant wrist provides better UAR under real-distribution training (imbalanced) challenges the standard practice of dominant-hand placement; this result has not been followed up in subsequent papers.
- **Detection-based jersey number recognition**: Liu & Bhanu introduced a pose-guided R-CNN that modifies the RPN to jointly propose person and digit regions, using human body keypoints to refine digit localization. This detection-based paradigm (81.3% digit AP, 78.7% jersey accuracy) established the baseline approach that later scene text recognition methods would surpass.

**2022:**
- **Energy-harvesting enters sports monitoring**: Liu et al. introduced piezoelectric PVDF film sensors as an alternative to battery-powered IMU. The self-powered modality solves the maintenance bottleneck (battery replacement/charging) but trades multi-axis kinematics for local strain sensing at a single joint location.
- **Sensor modality diversity**: three papers over four years now span piezoelectric, IMU, and (implicitly) potential video/computer vision modalities. The field is exploring complementary sensor technologies rather than converging on a single approach.

**2024:**
- **Scene text recognition for jersey numbers**: Koshkina & Elder (CVPRW 2024) reframe jersey number recognition as a scene text recognition problem, leveraging PARSeq pre-trained on general text-in-the-wild datasets. This achieves 91.4% image-level accuracy on hockey and 87.45% tracklet-level on SoccerNet — a 19-point improvement over prior best — with only minimal fine-tuning on a small public hockey dataset.
- **Cross-sport transfer**: The pipeline trained on hockey transfers to soccer with 83.9% accuracy without soccer-specific fine-tuning, suggesting jersey number recognition benefits from sport-agnostic visual features learned by STR pre-training.
- **Re-ID for occlusion handling**: Person re-identification features (Centroid-ReID) are used for unsupervised main subject filtering in tracklets — iterative Gaussian outlier rejection removes frames where the player is occluded, boosting tracklet-level accuracy without requiring frame-level occlusion annotations.
- **Broadcast video as a new data modality**: Unlike the wearable sensor track (2019–2025), this work operates on standard broadcast camera footage, expanding the sensor modality landscape for sports analytics.

**2025:**
- **Real-time, edge-deployable movement classification** is an active problem in sports wearables. Papers are demonstrating that classical ML (LDA, SVM) on hand-crafted sensor features can reach high accuracy on constrained sports action sets, while remaining deployable on Arduino/Raspberry Pi hardware without cloud dependency.
- **Volleyball action type classification** is now solved at high accuracy (99.09% with LDA on 6 classes using IMU), whereas 2019 work only addressed binary action/non-action. The progression from detection to fine-grained classification mirrors trends in other action recognition domains.
- **Dataset scale is the bottleneck**: controlled training datasets of a few hundred labelled samples are standard; in-game generalisation is underreported. No cross-sensor comparison (IMU vs. piezoelectric) has been performed on the same task.
- **Single-stage uncertainty-aware jersey number recognition**: Grad (CVPRW 2025) eliminates multi-stage pipelines entirely with an end-to-end ViT architecture. Digit-compositional heads (TDA) decompose numbers into constituent digits with shared weights, reducing head parameters by 86% while improving generalization. Dirichlet-based uncertainty modeling provides calibrated confidence, with the largest gains (+5–6%) on invisible/ambiguous cases where softmax models are overconfident. Achieves 85.62% on SoccerNet Challenge, surpassing prior published best (79.31%).
- **Uncertainty-aware pseudolabeling**: Using model uncertainty to filter pseudolabels for unlabeled data (SoccerNet ReID) enables competitive public-data-only models (83.52% Challenge accuracy), addressing the data scarcity problem without proprietary datasets.

## Open problems

- **Player-independent generalisation**: most papers train and test on the same players; cross-player performance is rarely evaluated
- **Jersey number recognition → long-term tracking integration**: Koshkina & Elder's pipeline assumes pre-existing tracklets; integrating jersey number recognition into end-to-end tracking (using numbers to resolve identity switches and merge fragmented tracklets) is untested. Liu & Bhanu's pose-guided approach (2019) similarly evaluates on isolated frames rather than tracklets
- **Detection vs. recognition vs. single-stage**: Three paradigms now exist — detection-based (Liu & Bhanu 2019), STR-based (Koshkina & Elder 2024), and single-stage uncertainty-aware (Grad 2025). Grad's single-stage approach surpasses Koshkina on the SoccerNet Challenge set (85.62% vs 79.31%) but neither paper compares against the other's approach head-to-head. Whether combining pose priors, STR features, and uncertainty modeling in a unified system could further improve remains unexplored
- **Cross-sport generalization**: Koshkina shows hockey-to-soccer transfer; Grad evaluates only on soccer. Whether single-stage uncertainty-aware approaches transfer across sports as well as STR-based methods is unknown
- **In-game vs. training conditions**: lab/training collected data may not reflect match-intensity biomechanics
- **Sensor placement standardisation**: no consensus on optimal placement for volleyball-specific classification across or within modalities (wrist vs. hand for IMU; hand vs. arm for piezo)
- **Multi-sensor fusion**: single-sensor approaches (IMU or piezo) capture only local information; whole-body movement analysis requires multi-sensor arrays
- **Cross-modality comparison**: no head-to-head comparison of IMU vs. piezoelectric sensors on the same task/dataset; relative strengths unclear

## Contradictions and debates

- **LDA vs. deep learning**: the volleyball LDA paper (Koteda 2025) claims superiority over complex models but does not benchmark against them on the same split — the claim is unverified and likely dataset-size-dependent.
- **Dominant vs. non-dominant hand**: Salim et al. (2019) found non-dominant hand is better under imbalanced training; subsequent work (Koteda 2025, Liu et al. 2022) do not address placement at all. Whether dominant or non-dominant placement is optimal for action classification remains an open comparison.
- **Binary detection vs. multi-class classification**: Salim et al. treat action type classification as future work; Koteda et al. and Liu et al. demonstrate it is achievable. However, Koteda reports accuracy (not UAR), making cross-paper comparison difficult.
- **Sensor modality trade-off**: IMU provides multi-axis kinematics but requires batteries; piezoelectric provides self-powered operation but limited to single-joint strain sensing. No explicit comparative study of this trade-off exists.

## Recommended reading order

1. [[papers/2019-volleyball-action-modelling-imu-feedback|Volleyball Action Modelling (2019)]] — start here for the broader sensor/placement comparison and system perspective; establishes what sensors and classifiers work for volleyball IMU
2. [[papers/2022-self-powered-wearable-motion-sensor-volleyball|Self-Powered PVDF Sensor (2022)]] — read next to understand the alternative modality (energy harvesting) and its deployment advantages; exposes the sensor trade-off space
3. [[papers/2025-realtime-volleyball-movement-classification-lda|Volleyball LDA (2025)]] — conclude with the state-of-the-art IMU + classical ML approach; tighter focus, higher accuracy, real-time deployment; enables appreciation of how the field has specialized since 2019
4. [[papers/2019/2019-pose-guided-rcnn-jersey-number/2019-pose-guided-rcnn-jersey-number|Pose-Guided R-CNN (CVPRW 2019)]] — the detection-based approach to jersey number recognition; establishes person-digit geometric association as a key inductive bias
5. [[papers/2024/2024-jersey-number-recognition/2024-jersey-number-recognition|Jersey Number Recognition (CVPRW 2024)]] — the recognition-based successor; shows how pre-trained STR models surpass detection-based methods with minimal fine-tuning and cross-sport transfer
6. [[papers/2025/2025-uncertainty-aware-jersey-number/2025-uncertainty-aware-jersey-number|Uncertainty-Aware Jersey Number Recognition (CVPRW 2025)]] — the current state of the art; single-stage end-to-end with digit-compositional heads and Dirichlet uncertainty; demonstrates when to abstain matters as much as when to predict

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
