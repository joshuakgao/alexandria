---
title: "WorldGym: World Model as an Environment for Policy Evaluation"
authors: [Julian Quevedo, Ansh Kumar Sharma, Yixiang Sun, Varad Suryavanshi, Percy Liang, Sherry Yang]
year: 2025
tags: [world-models, embodied-ai]
url: "https://arxiv.org/abs/2506.00613"
date_ingested: 2026-07-22
---

# WorldGym: World Model as an Environment for Policy Evaluation

![[2025-worldgym-thumbnail.png]]

## Research gap
Evaluating robot control policies is costly in the real world, and handcrafted simulators require significant manual effort to improve in realism and generality. While video world models can visually emulate physical interactions, they have not been systematically used as environments for evaluating robot policies across diverse morphologies and tasks.

## Contributions
- Proposes WorldGym, an autoregressive action-conditioned video generation model that serves as a proxy environment for evaluating robot policies.
- Introduces flexible alignment of diffusion horizon length with policies' action chunk sizes, enabling efficient rollouts for diverse policies from a single world model checkpoint.
- Demonstrates that policy success rates in WorldGym highly correlate (r = 0.78) with real-world success rates across multiple VLA-based policies.
- Shows the world model enables easy evaluation of policy generalization on out-of-distribution tasks and environments using only a single start frame.

## Method
WorldGym trains a latent Diffusion Transformer on sequences of frames paired with actions using Diffusion Forcing for autoregressive frame generation. Robot action vectors are linearly projected and added to diffusion timestep embeddings, conditioning the model through AdaLN-Zero modulation. Classifier-free guidance is used to improve action adherence by randomly dropping out actions for entire video clips during training. Causal temporal attention blocks interleaved with spatial attention blocks handle frame conditioning. At inference, the diffusion horizon length is set equal to the policy's action chunk size for efficient generation. A VLM (GPT-4o) serves as the reward model, scoring generated rollouts against language instructions for task success.

## Datasets & evaluation
Trained on diverse data from the Open-X Embodiment dataset spanning multiple robot morphologies (Bridge, Google Robot, VIOLA, Berkeley UR5). Evaluated three VLA policies (RT-1-X, Octo, OpenVLA) on 17 challenging Bridge tasks. Policy success rates in WorldGym correlated strongly with real-world success rates (r = 0.78, p < 0.001), with mean success rates differing by only 3.3% from real-world values. Relative policy rankings were preserved across different policy versions, sizes, and training checkpoints. Also tested on out-of-distribution tasks and environments, revealing that modern VLA policies struggle with object shape discrimination and can be distracted by adversarial object facades.

## Limitations
- Generating highly realistic object interactions (e.g., deformable objects, complex contact dynamics) remains challenging for the video world model.
- The VLM reward model can introduce errors in success determination.
- Evaluation is limited to end-effector control policies; full-body or dexterous manipulation is not explored.
- The approach inherits biases from training data distributions in the Open-X Embodiment dataset.

## Key takeaways
- A single video world model trained on diverse robotic data can serve as a practical evaluation proxy across different robot morphologies, leveraging the observation that all robots share the same physical world. WorldGym offers a safe, reproducible, and low-cost alternative for sanity-checking policies before real-world deployment, and its ability to test OOD generalization from a single frame is particularly valuable for discovering failure modes early.
