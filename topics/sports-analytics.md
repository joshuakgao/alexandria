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

*Last updated: 2026-04-06 | Sources: 3 papers*

## Current thesis

Sports analytics with ML is bifurcating into two tracks: heavyweight deep learning on video/trajectory data for post-hoc analysis, and lightweight classical ML on wearable sensor data for real-time, on-device feedback. Within the wearable track, a further distinction is emerging between battery-powered IMU sensors (2019, 2025) and energy-harvesting piezoelectric sensors (2022). IMU-based approaches prioritize multi-axis kinematics and multi-class action recognition; piezoelectric approaches prioritize maintenance-free long-duration operation and direct motion-to-electricity conversion. Volleyball is emerging as a useful benchmark domain with converging results across both modalities (2019–2025), enabling comparative analysis of sensor technology, algorithm approach, and practical deployment considerations.

## Key trends

**2019:**
- **Multi-sensor IMU fusion for volleyball** established that dual-wrist sensor placement and Acc+Mag fusion provides meaningfully better action detection than single sensors or single wrists (86.9% UAR). Naive Bayes outperforms LDA and SVM for imbalanced training settings.
- **System integration was an early concern**: Salim et al. built a full pipeline from sensor to web frontend, demonstrating that event auto-tagging for coached video review was technically feasible even with 2019-era hardware.
- **Non-dominant hand is underrated**: the finding that the non-dominant wrist provides better UAR under real-distribution training (imbalanced) challenges the standard practice of dominant-hand placement; this result has not been followed up in subsequent papers.

**2022:**
- **Energy-harvesting enters sports monitoring**: Liu et al. introduced piezoelectric PVDF film sensors as an alternative to battery-powered IMU. The self-powered modality solves the maintenance bottleneck (battery replacement/charging) but trades multi-axis kinematics for local strain sensing at a single joint location.
- **Sensor modality diversity**: three papers over four years now span piezoelectric, IMU, and (implicitly) potential video/computer vision modalities. The field is exploring complementary sensor technologies rather than converging on a single approach.

**2025:**
- **Real-time, edge-deployable movement classification** is an active problem in sports wearables. Papers are demonstrating that classical ML (LDA, SVM) on hand-crafted sensor features can reach high accuracy on constrained sports action sets, while remaining deployable on Arduino/Raspberry Pi hardware without cloud dependency.
- **Volleyball action type classification** is now solved at high accuracy (99.09% with LDA on 6 classes using IMU), whereas 2019 work only addressed binary action/non-action. The progression from detection to fine-grained classification mirrors trends in other action recognition domains.
- **Dataset scale is the bottleneck**: controlled training datasets of a few hundred labelled samples are standard; in-game generalisation is underreported. No cross-sensor comparison (IMU vs. piezoelectric) has been performed on the same task.

## Open problems

- **Player-independent generalisation**: most papers train and test on the same players; cross-player performance is rarely evaluated
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

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
