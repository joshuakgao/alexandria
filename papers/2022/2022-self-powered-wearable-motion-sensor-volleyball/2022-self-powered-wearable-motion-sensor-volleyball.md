---
title: "A Self-Powered Wearable Motion Sensor for Monitoring Volleyball Skill and Building Big Sports Data"
authors: [Weijie Liu, Zhenzhen Long, Gaoang Yang, Liwei Xing]
year: 2022
venue: "Biosensors"
tags: [sports-analytics]
url: ""
pdf: "[[2022-self-powered-wearable-motion-sensor-volleyball.pdf]]"
date_ingested: 2026-06-18
---

# A Self-Powered Wearable Motion Sensor for Monitoring Volleyball Skill and Building Big Sports Data

![[2022-self-powered-wearable-motion-sensor-volleyball-thumbnail.png]]

## Research gap

This paper presents a self-powered wearable motion sensor fabricated from piezoelectric PVDF (polyvinylidene fluoride) film for real-time monitoring of volleyball spiking technique. The sensor converts mechanical energy from body motion directly into electrical signals via the piezoelectric effect, eliminating the need for external power supplies or frequent battery charging. The flexible device conforms to the hand or arm and detects fine motor movements of finger and arm position during spiking, providing feedback on skill quality. Beyond motion capture, the sensor can simultaneously monitor physiological signals (pulse). The collected data can be wirelessly transmitted to build comprehensive sports analytics datasets.

## Contributions

- **Energy-harvesting wearable sensor**: demonstrates piezoelectric PVDF film as a practical solution to the power-supply problem in wearable sports devices
- **Self-powered operation**: sensor actively generates its own electrical signals from body motion, requiring no external battery or charging
- **Fine-grained motion detection**: captures detailed finger and arm kinematics during volleyball spiking, beyond simple binary detection/non-detection
- **Integrated physiological monitoring**: simultaneously tracks pulse alongside motion, providing holistic athlete data
- **Wireless data integration**: compatible with wireless transmitters for real-time data collection and big sports data pipeline

## Method

The sensor is a flexible thin-film device made from piezoelectric PVDF polymer. PVDF exhibits strong piezoelectric properties: mechanical deformation (from body motion) induces strain that polarizes the material, generating electrical voltage without external power.

The device is conformably attached to the hand or arm using flexible mounting. As the athlete performs volleyball movements (particularly spiking), the motion deforms the PVDF film, generating piezoelectric signals that reflect the motion's magnitude, frequency, and acceleration profile. The electrical output is directly proportional to the mechanical deformation, making it a natural sensor of motion intensity and fine motor control.

Signal amplification and wireless transmission interface allow the raw piezoelectric signals to be transmitted in real-time to a data collection system.

## Datasets & evaluation

The sensor successfully detects and discriminates spiking movements from other hand/arm activities. It captured fine motion details of spiking gesture, allowing assessment of skill quality and consistency. The device also monitored real-time pulse changes during volleyball matches, demonstrating multi-modal sensing capability from a single flexible substrate.

The self-powered operation was validated across extended monitoring sessions without battery depletion—the mechanical energy from normal athletic motion continuously powers the sensor.

## Limitations

- **Generalization across movements**: tested primarily on volleyball spiking; unclear how well sensor generalizes to other sports or motion types
- **Cross-athlete variability**: no analysis of whether different athletes' body mechanics, hand size, or arm stiffness affect sensor response
- **Quantitative validation**: no comparison to ground truth (e.g., high-speed video, IMU reference sensors); claimed skill monitoring is qualitative
- **Signal-to-skill mapping**: the relationship between raw piezoelectric amplitude and objective volleyball skill metrics is not formally established
- **Fatigue effects**: unclear how sensor response changes as athlete fatigues during a match
- **Durability**: no data on PVDF film degradation or sensor lifetime under repeated athletic stress

## Key takeaways

The key innovation is replacing battery-powered accelerometers with energy-harvesting piezoelectric sensors, addressing the power-supply bottleneck in wearable sports monitoring systems. This approach enables continuous, long-duration athlete monitoring without maintenance, making it practical for training and coaching scenarios.

This paper differs fundamentally from prior IMU-based approaches (Salim et al. 2019, Koteda 2025) in the sports-analytics wiki:
- **Sensor modality**: replaces accelerometers + gyroscopes (IMU) with piezoelectric transducers
- **Power model**: eliminates battery dependency via energy harvesting, solving a key practical limitation
- **Signal interpretation**: raw piezoelectric signals measure local strain (related to motion intensity) rather than acceleration; interpretation strategy differs
- **Generality**: IMU approach is modality-agnostic (applicable to any sport); piezoelectric approach requires careful mechanical coupling to the joint of interest

The paper aligns with broader wearable electronics literature emphasizing self-powered devices for health/sports monitoring, but positions sports skill monitoring as a novel application domain for piezoelectric sensors.
