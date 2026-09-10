---
title: "Learning Agile Robotic Locomotion Skills by Imitating Animals"
authors: [Xue Bin Peng, Erwin Coumans, Tingnan Zhang, Tsang-Wei Edward Lee, Jie Tan, Sergey Levine]
year: 2020
venue: "RSS 2020"
tags: [robotics]
url: "https://xbpeng.github.io/projects/Robotic_Imitation/Robotic_Imitation_2020.pdf"
date_ingested: 2026-08-27
---

# Learning Agile Robotic Locomotion Skills by Imitating Animals

![[2020-learning-agile-robotic-locomotion-imitating-animals-thumbnail.png]]

## Research gap

Manually designing controllers for agile legged locomotion requires deep expertise for each skill and remains far from matching the fluid motions of animals. RL-based alternatives can automate controller design but suffer from reward engineering challenges and tend to produce unnatural behaviors that are infeasible on real hardware. Prior motion imitation work in simulation had not been successfully transferred to real robots for dynamic, agile skills.

## Contributions

- An end-to-end framework for learning diverse agile locomotion skills on a real quadruped robot by imitating motion capture data from real animals.
- A motion retargeting pipeline using inverse kinematics to map animal mocap data to a robot's morphology.
- A sample-efficient latent-space domain adaptation method that fine-tunes simulation-trained policies for real-world deployment using a learned dynamics representation.
- Demonstration of 10 distinct locomotion skills (gaits, hops, turns, spins) on a Laikago quadruped robot, transferred from simulation to the real world.

## Method

The framework has three stages:

1. **Motion retargeting**: Mocap clips from a real dog are retargeted to the Laikago robot via inverse kinematics. Corresponding keypoints (feet, hips) are specified on the animal and robot, and IK solves for robot poses that track the keypoints at each frame.

2. **Motion imitation via RL**: A policy network is trained in simulation with PPO to imitate the retargeted reference motion. The state includes three previous poses (IMU root orientation + joint rotations) and three previous actions. The goal specifies target poses at four future timesteps (~1 second ahead). Actions are PD controller targets passed through a low-pass filter. The reward combines pose tracking, velocity tracking, end-effector tracking, root position, and root velocity terms. Domain randomization over dynamics parameters (mass, friction, motor strength) is applied during training to produce robust policies.

3. **Latent-space domain adaptation**: An encoder maps dynamics parameters to a latent vector that modulates policy behavior. During real-world deployment, advantage-weighted regression (AWR) searches the latent space to find dynamics configurations that maximize performance on the real robot, requiring only ~50 episodes of real-world interaction.

## Datasets & evaluation

Motion data was recorded from a real dog using a commercial motion capture system (Xsens), including gaits (pace, trot, backward pace/trot), spin, side-steps, in-place steps, turn, hop-turn, and a "running man" motion. Policies were evaluated on normalized return (0–1) in both simulation and the real world. The adaptive policy achieved the best real-world performance on most skills (e.g., 0.827 on dog pace vs. 0.350 for robust baseline). The adaptive policy also maintained balance significantly longer than baselines — often reaching the maximum episode length without falling.

## Limitations

- Reference motions are generally not fully physically feasible for the robot due to morphological differences, limiting tracking fidelity.
- The latent-space adaptation still requires ~50 real-world episodes per skill.
- Skills are learned independently with separate policies rather than a single multi-skill controller.
- The approach was demonstrated on a quadruped with relatively simple foot contacts; more complex morphologies (bipeds, hands) may pose additional challenges.

## Key takeaways

- Animal motion capture data provides a powerful and general source of skill priors for robot locomotion, eliminating the need for per-skill reward engineering.
- Combining motion imitation with latent-space sim-to-real adaptation enables dynamic, agile skills that were previously only achievable in simulation.
- This work was foundational for later research on humanoid motion tracking and sim-to-real transfer at scale (e.g., SONIC, DeepMimic-to-real pipelines).
