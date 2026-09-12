---
topic: Robotics
slug: robotics
---

# Robotics

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "robotics")
SORT year DESC
```

## Overview

Robotics research encompasses the design, control, and deployment of physical robotic systems, spanning locomotion, manipulation, and whole-body coordination. The field is increasingly converging with foundation model research, as large-scale pretraining on motion capture data, simulation, and human video enables generalist controllers that replace per-task reward engineering with scalable learning objectives.

## Trends

- **Demonstration-augmented RL for dexterous manipulation**: Rajeswaran et al. (RSS 2018) showed that combining behavior cloning pre-training with an augmented policy gradient (DAPG) enables a 24-DoF anthropomorphic hand to learn complex manipulation tasks (relocation, in-hand repositioning, tool use, door opening) in under 6 robot-hours — a 30x reduction over pure RL with shaped rewards. Critically, demonstration-derived policies are more robust to environment variations and produce more natural motions, establishing that human priors via demonstrations are superior to reward shaping for encoding manipulation strategies.
- **Animal motion imitation as a general skill prior**: Peng et al. (2020) demonstrated that mocap data from real animals, retargeted via inverse kinematics, can serve as a universal learning objective for diverse agile locomotion skills — eliminating per-skill reward engineering and establishing the motion imitation + sim-to-real adaptation paradigm that subsequent work has scaled.
- **Motion tracking as a scalable foundation for humanoid control**: SONIC demonstrates that physics-based motion tracking — with dense per-frame supervision from mocap data — scales favorably with data, model size, and compute, enabling a single universal policy for diverse whole-body behaviors without reward engineering.
- **Universal control interfaces**: Shared tokenized representations that unify heterogeneous input modalities (VR teleoperation, video, text, music, VLA outputs) within a single control policy, eliminating the need for separate controllers per application.
- **Sim-to-real transfer at scale**: Large-scale simulation training (100M+ frames, 128 GPUs) with domain randomization enables robust deployment on physical humanoids with minimal sim-to-real gap.
- **VLA-driven loco-manipulation**: Vision-language-action models controlling the full kinematic chain (including feet) through universal token spaces, enabling tasks requiring coordinated hand and foot placement.
- **RGB-based visual sim-to-real for loco-manipulation**: VIRAL demonstrates that teacher-student privileged learning with large-scale visual domain randomization, delta action spaces over pretrained WBC policies, and DAgger-BC mixture distillation enables zero-shot RGB-based humanoid loco-manipulation approaching expert teleoperation performance. Compute scale is critical — low-compute regimes frequently fail entirely.
- **Monocular video-to-robot data generation for bimanual dexterous manipulation**: DexImit (Mu et al., RSS 2026) introduces an automated four-stage pipeline that converts monocular human manipulation videos into physically plausible bimanual dexterous manipulation data without depth sensors or camera parameters. Uses human hand size as a metric scale prior, force-closure-based grasp synthesis with MANO-prompted ranking, and an Action-Centric Scheduling Algorithm for long-horizon bimanual coordination. Comprehensive data augmentation (object pose/scale, camera, point cloud noise) enables zero-shot sim-to-real transfer. Handles tool use, long-horizon tasks, and fine-grained manipulation (stacking 6 cups). Dramatically lowers the data collection barrier compared to VR teleoperation (cf. DAPG's 25 demos per task) by harvesting demonstrations from arbitrary video.
- **Action-free video as scalable VLA supervision**: WholeBodyVLA demonstrates that separate locomotion and manipulation Latent Action Models trained on action-free egocentric human videos can provide effective pseudo-action supervision for VLA pre-training, reducing reliance on expensive teleoperation data by up to 8× while improving loco-manipulation success rates. The discrete-command LMO RL policy — replacing continuous velocity tracking with ternary directional flags — achieves higher precision on start–stop and turning movements critical for manipulation.
- **Stage-wise RL curriculum for dynamic whole-body coordination**: Humanoid Whole-Body Badminton demonstrates that a three-stage curriculum (footwork → swing → task refinement) enables a unified policy to discover coordinated striking behaviors — including pre-strike leg push-off for power generation — without motion priors or expert demonstrations. The prediction-free variant shows that implicit temporal reasoning from short observation histories can substitute for explicit trajectory prediction, simplifying deployment while maintaining comparable performance.

## Open questions

- How to ensure safety and energy efficiency for extended real-world humanoid deployments?
- Can motion tracking scaling continue to yield improvements, or will performance saturate at some data/compute threshold?
- How to close the sim-to-real gap for precise foot contact dynamics?
- What is the data efficiency frontier for VLA-based loco-manipulation — how many teleoperation demonstrations are needed per task?
- Can visual sim-to-real loco-manipulation extend beyond single-skill demonstrations to diverse, long-horizon task chaining?
- What is the minimum compute required for reliable sim-to-real loco-manipulation training, and can more efficient methods reduce the current 64-GPU requirement?
- Can discrete-command locomotion interfaces (as in WholeBodyVLA's LMO) scale to more complex movement primitives beyond advancing, turning, and squatting?
- How to integrate spatial memory and active perception into VLA-based loco-manipulation for navigation in large, cluttered environments?
- Can unified whole-body RL policies for dynamic tasks (racket sports, catching, throwing) transfer from controlled MoCap arenas to unstructured environments with onboard perception only?
- How does the quality and modality of demonstrations (VR teleoperation vs. video vs. motion capture) affect downstream RL fine-tuning for dexterous manipulation?
- Can video-to-robot pipelines like DexImit handle deformable objects, articulated objects, or true in-hand manipulation (finger gaiting, dexterous reorientation)?
- As video generation models improve, will generated videos become a sufficient data source for complex long-horizon dexterous manipulation, eliminating the need for real human demonstrations entirely?
