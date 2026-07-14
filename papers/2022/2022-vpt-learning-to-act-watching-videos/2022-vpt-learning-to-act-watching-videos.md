---
title: "Video PreTraining (VPT): Learning to Act by Watching Unlabeled Online Videos"
authors: [Bowen Baker, Ilge Akkaya, Peter Zhokhov, Joost Huizinga, Jie Tang, Adrien Ecoffet, Brandon Houghton, Raul Sampedro, Jeff Clune]
year: 2022
venue: "NeurIPS 2022"
tags: [reinforcement-learning, world-models, embodied-ai]
url: "https://arxiv.org/abs/2206.11795"
date_ingested: 2026-07-04
---

# Video PreTraining (VPT): Learning to Act by Watching Unlabeled Online Videos

![[2022-vpt-learning-to-act-watching-videos-thumbnail.png]]

## Research gap
Large-scale pretraining on internet data has transformed text and image understanding, but sequential decision domains (games, robotics, computer use) lack action labels in publicly available video data. Reinforcement learning alone is too sample-inefficient for hard-exploration problems in complex action spaces. No prior work had demonstrated that the vast quantity of unlabeled online video could be leveraged to learn behavioral priors for sequential decision-making at scale.

## Contributions
- Introduces **Video PreTraining (VPT)**, a semi-supervised imitation learning method that trains an inverse dynamics model (IDM) on a small labeled dataset (~2K hours), uses it to pseudo-label ~70K hours of unlabeled internet video, and then trains a behavioral cloning foundation model (0.5B parameters) on the pseudo-labeled data.
- Demonstrates that the IDM is **two orders of magnitude more data-efficient** than direct behavioral cloning — inverting environment dynamics is far easier than modeling the full distribution of human behavior.
- Shows nontrivial **zero-shot capabilities** from the foundation model (tree chopping, crafting tables — tasks requiring ~970 consecutive actions) that are impossible to learn from scratch with RL in the native human interface.
- Achieves **diamond pickaxe crafting** in Minecraft via three-phase training (pretraining → BC fine-tuning → RL fine-tuning) — the first reported success on this task, requiring ~24,000 consecutive actions with the native mouse-and-keyboard interface.
- Demonstrates that a **KL divergence auxiliary loss** to the frozen pretrained policy prevents catastrophic forgetting of latent skills during RL fine-tuning, enabling progressive skill acquisition through the technology tree.
- Open-sources contractor data, model weights, and the Minecraft environment.

## Method
**Inverse Dynamics Model (IDM):** A non-causal model (temporal convolutions + ResNet + unmasked attention) that predicts the action at each timestep given both past and future frames. Trained on ~2K hours of labeled contractor gameplay. Achieves 90.6% keypress accuracy and 0.97 R² for mouse movements.

**Data pipeline:** ~270K hours of Minecraft video collected via keyword search, filtered to ~70K hours of "clean" survival-mode segments using a trained binary classifier (8,800 labeled images). The IDM pseudo-labels all clean video with action predictions.

**Foundation model:** A 0.5B parameter causal model trained with standard behavioral cloning on IDM-labeled data — minimizing negative log-likelihood of pseudo-labeled actions conditioned on past observations only. Trained for 30 epochs on 720 V100 GPUs over ~9 days.

**BC fine-tuning:** The foundation model is fine-tuned on narrower datasets targeting specific behavior distributions (e.g., early-game house building from contractor data, or keyword-filtered "episode 1" videos).

**RL fine-tuning:** Phasic Policy Gradient (PPG) with a shaped reward for items in the diamond pickaxe crafting sequence. A KL divergence loss to the frozen pretrained policy prevents catastrophic forgetting of downstream skills (e.g., furnace smelting) while earlier skills (e.g., log chopping) are being reinforced.

## Datasets & evaluation
- **Training data:** ~70K hours of filtered internet Minecraft video (unlabeled, pseudo-labeled by IDM); ~2K hours of labeled contractor gameplay for IDM training.
- **Evaluation:** Zero-shot rollouts in standard Minecraft survival mode (60-minute episodes, 72,000 actions). Metrics include collection and crafting rates for items in the technology tree.
- **Key results:**
  - Zero-shot: crafts planks, crafting tables, sticks; navigates terrain, swims, pillar-jumps, raids villages.
  - BC fine-tuned (contractor_house): 213× more crafting tables, crafts wooden and stone tools (median 2.3 min / 2,790 actions for humans).
  - RL fine-tuned: 80%+ iron pickaxe reliability (human-level), ~20% diamond collection, 2.5% diamond pickaxe success (first reported non-zero rate with native interface). Prior work achieved ~0.1% diamond success with simplified action spaces.
- **Scaling:** Foundation model performance increases with data scale up to the full 70K hours; crafting tables only emerge above 5K hours; stone tools only at the largest scale. IDM quality plateaus at ~100 hours of labeled data.

## Limitations
- **Single domain:** Only tested in Minecraft; generalization to other sequential decision domains (robotics, computer use) is hypothesized but undemonstrated.
- **No task conditioning:** The foundation model cannot be directed to perform specific tasks — it models the average behavioral distribution. Preliminary caption-conditioning experiments show only weak steerability.
- **Loss-evaluation disconnect:** Validation loss does not consistently correlate with downstream rollout performance, making training progress hard to measure.
- **Native interface difficulty:** While using the full mouse-and-keyboard interface is more general, it dramatically increases exploration difficulty (60 consecutive attack actions to chop one log), limiting absolute success rates.
- **IDM error propagation:** Pseudo-label noise from the IDM propagates to the foundation model; the effect of systematic IDM biases on downstream capabilities is not fully characterized.
- **Compute cost:** Training requires 720 V100 GPUs for ~9 days for the foundation model alone, plus substantial RL fine-tuning compute (~1.3M episodes).

## Key takeaways
- **Inverse dynamics as data multiplier:** A small amount of labeled data (~2K hours, ~$2K USD) can unlock massive unlabeled video datasets for behavioral cloning. The IDM is the key enabler — it is orders of magnitude more data-efficient than direct BC because inverting environment mechanics is easier than modeling intent.
- **Behavioral priors as exploration priors:** The foundation model's zero-shot capabilities (impossible to learn with RL from scratch) serve as an effective exploration prior that makes RL fine-tuning feasible for extremely long-horizon tasks.
- **Three-phase training paradigm:** Pretraining → BC fine-tuning → RL fine-tuning is a general recipe for hard-exploration sequential decision domains, directly paralleling the pretrain → fine-tune paradigm in NLP.
- **KL regularization preserves latent skills:** Without a KL loss to the pretrained policy, RL fine-tuning catastrophically forgets downstream skills before they can be discovered, stalling progress. This is a general principle for fine-tuning behavioral priors.
- **Bridges Genie and NitroGen:** VPT occupies a middle ground between fully unsupervised latent action discovery (Genie, which needs no labels at all) and fully supervised behavior cloning (NitroGen, which uses automatically extracted action labels). VPT's IDM approach requires minimal human labeling to unlock internet-scale video for action learning.
