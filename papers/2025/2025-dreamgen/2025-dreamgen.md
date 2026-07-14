---
title: "DreamGen: Unlocking Generalization in Robot Learning through Video World Models"
authors: [Joel Jang, Seonghyeon Ye, Zongyu Lin, Jiannan Xiang, Johan Bjorck, Yu Fang, Fengyuan Hu, Spencer Huang, Kaushil Kundalia, Lin Yen-Chen, Loic Magne, Ajay Mandlekar, Avnish Narayan, You Liang Tan, Guanzhi Wang, Jing Wang, Qi Wang, Yinzhen Xu, Xiaohui Zeng, Kaiyuan Zheng, Ruijie Zheng, Ming-Yu Liu, Luke Zettlemoyer, Dieter Fox, Jan Kautz, Scott Reed, Yuke Zhu, Linxi Fan]
year: 2025
tags: [world-models, embodied-ai, image-video-generation]
url: "https://arxiv.org/abs/2505.12705"
date_ingested: 2026-07-10
---

# DreamGen: Unlocking Generalization in Robot Learning through Video World Models

![[2025-dreamgen-thumbnail.png]]

## Research gap
Scaling robot learning is bottlenecked by the cost of collecting teleoperation data for every new task and environment. Simulation offers an alternative but suffers from significant sim-to-real gaps for visuomotor policies. Prior work has explored video world models as real-time planners, but not as large-scale synthetic data generators for downstream policy training — leaving the strong physical reasoning and language grounding priors in video generative models underexploited for robotics.

## Contributions
- A simple 4-stage pipeline (fine-tune video world model → generate rollouts → label pseudo-actions → train visuomotor policy) that uses video world models as synthetic data generators, producing "neural trajectories" for robot policy training.
- Demonstration of **behavior generalization**: a GR1 humanoid performs 22 novel behaviors (pouring, tool use, articulated object manipulation) using teleoperation data from only a single pick-and-place task in one environment.
- Demonstration of **environment generalization**: policies transfer to 10 unseen environments using a video model fine-tuned on a single environment.
- Log-linear scaling of policy performance with synthetic data volume (up to 333× augmentation on RoboCasa).
- **DreamGen Bench**: a video generation benchmark for evaluating how well video world models adapt to novel robot embodiments, with strong correlation to downstream policy success.
- Validation across three robot embodiments (Fourier GR1 humanoid, Franka Emika, SO-100) and both simulation and real-world settings.

## Method
1. **Video world model fine-tuning**: Fine-tune a state-of-the-art image-to-video model (WAN2.1 by default) on teleoperated robot trajectories using LoRA to preserve internet video priors. Multi-viewpoint data is concatenated into a 2×2 grid. Evaluated on instruction-following and physics-following metrics.
2. **Video rollout generation**: Given initial frames (with randomized object positions or novel environments) and language instructions, generate large volumes of synthetic robot videos depicting both familiar and novel behaviors.
3. **Pseudo-action labeling**: Since generated videos lack action annotations, recover pseudo-action sequences using either:
   - **Inverse dynamics model (IDM)**: ViT encoder predicts actions from consecutive frame pairs, trained on real teleoperation data.
   - **Latent action model (LAPA)**: VQ-VAE-based model that discovers latent actions from video, similar to Genie's approach but applied to specific robot embodiments.
4. **Visuomotor policy training**: Train downstream policies (GR00T N1, Diffusion Policy, π0) on the resulting neural trajectories (video + pseudo-action pairs), either alone or co-trained with real demonstrations.

## Datasets & evaluation
- **Simulation**: RoboCasa benchmark — scaling synthetic data up to 333× relative to original demonstrations yields log-linear policy improvement.
- **Real-world data augmentation**: 9 tasks across 3 robots with 10–13 real trajectories per task:
  - GR1 humanoid: 37% → 46.4% average success (4 tasks)
  - Franka Emika: 23% → 37% average success (3 tasks)
  - SO-100: 21% → 45.5% average success (2 tasks)
- **Behavior generalization**: 22 novel behaviors on GR1 — 43.2% success in seen environments, 28.5% in unseen environments (vs. 0% for GR00T N1 trained on pick-and-place alone).
- **Environment generalization**: Transfer to 10 new environments from a single training environment.
- **DreamGen Bench**: Evaluates 8 video models (4 zero-shot, 4 fine-tuned) on instruction following, physics following, and generalization to unseen objects/behaviors/environments. Higher benchmark scores correlate with stronger downstream policy performance.

## Limitations
- Requires manual collection of initial frames for each new environment (though far cheaper than full teleoperation).
- Pseudo-action labeling introduces noise — IDM and LAPA produce approximate actions that may not perfectly match the generated motions.
- Video world model quality is the bottleneck: generated videos must be physically plausible and instruction-following for neural trajectories to be useful.
- Evaluated primarily on tabletop and single-arm manipulation; scaling to bimanual, contact-rich, or deformable-object tasks is untested.
- DreamGen Bench correlation with downstream success is demonstrated but the causal mechanism (which video quality factors matter most) is not fully characterized.

## Key takeaways
- Video world models are more valuable as **offline synthetic data generators** than as real-time planners — the generated neural trajectories provide a scalable axis for robot learning that is orthogonal to collecting more human demonstrations.
- The combination of LoRA-fine-tuned video models + pseudo-action labeling enables **zero-to-one generalization**: from a single task in a single environment to 22 novel behaviors across 10 unseen environments.
- Log-linear scaling with synthetic data volume suggests that DreamGen-style data augmentation has not yet saturated — more generated data continues to improve policies.
- DreamGen Bench provides a diagnostic proxy for video-to-robotics transfer quality, enabling video model researchers to evaluate embodied applicability without a physical robot.
- The pipeline is embodiment-agnostic: validated on humanoid, industrial arm, and low-cost arm platforms with consistent gains.
