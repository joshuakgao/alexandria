---
title: "RT-2: Vision-Language-Action Models Transfer Web Knowledge to Robotic Control"
authors: [Anthony Brohan, Noah Brown, Justice Carbajal, Yevgen Chebotar, Xi Chen, Krzysztof Choromanski, Tianli Ding, Danny Driess, Avinava Dubey, Chelsea Finn, Pete Florence, Chuyuan Fu, Montse Gonzalez Arenas, Keerthana Gopalakrishnan, Kehang Han, Karol Hausman, Alexander Herzog, Jasmine Hsu, Brian Ichter, Alex Irpan, Nikhil Joshi, Ryan Julian, Dmitry Kalashnikov, Yuheng Kuang, Isabel Leal, Lisa Lee, Tsang-Wei Edward Lee, Sergey Levine, Yao Lu, Henryk Michalewski, Igor Mordatch, Karl Pertsch, Kanishka Rao, Krista Reymann, Michael Ryoo, Grecia Salazar, Pannag Sanketi, Pierre Sermanet, Jaspiar Singh, Anikait Singh, Radu Soricut, Huong Tran, Vincent Vanhoucke, Quan Vuong, Ayzaan Wahid, Stefan Welker, Paul Wohlhart, Jialin Wu, Fei Xia, Ted Xiao, Peng Xu, Sichun Xu, Tianhe Yu, Brianna Zitkovich]
year: 2023
venue: "CoRL 2023"
tags: [embodied-ai, vision-language-action]
url: "https://arxiv.org/abs/2307.15818"
date_ingested: 2026-07-14
---

# RT-2: Vision-Language-Action Models Transfer Web Knowledge to Robotic Control

![[2023-rt-2-vision-language-action-thumbnail.png]]

## Research gap
Prior approaches to incorporating vision-language models (VLMs) into robotics only addressed high-level planning — parsing commands into discrete primitives executed by separate low-level controllers that do not benefit from web-scale semantic knowledge. The low-level action policies remained disconnected from the rich visual and linguistic understanding of internet-pretrained models.

## Contributions
- Introduces the vision-language-action (VLA) model category: co-fine-tuning large VLMs on both web-scale vision-language data and robotic trajectory data to directly output low-level robot actions.
- A simple, general recipe for converting robot actions into text tokens, enabling action prediction without any new model parameters or architectural modifications.
- Demonstrates emergent semantic reasoning capabilities inherited from web pretraining, including symbol understanding, rudimentary reasoning, and chain-of-thought multi-stage inference.
- Comprehensive evaluation with ~6,000 real-world robotic trials showing ~2x improvement over RT-1 on generalization to novel objects, backgrounds, and environments.

## Method
RT-2 adapts pretrained VLMs (PaLI-X up to 55B parameters, PaLM-E 12B) for robotic control by representing actions as text tokens. The 6-DoF end-effector displacement, gripper extension, and termination command are discretized into 256 bins and expressed as integer tokens concatenated into a string. The model is co-fine-tuned on a mixture of web-scale vision-language tasks (VQA, captioning) and robot demonstration data, with the robot input formatted as VQA: "Q: what action should the robot take to [task]? A: [action tokens]". Output is constrained to valid action tokens during robot inference. Co-fine-tuning (rather than robot-only fine-tuning) is critical for generalization, as it exposes the model to both abstract visual concepts and low-level actions simultaneously. For real-time inference, the 55B model runs at 1-3 Hz on a multi-TPU cloud service queried over the network; the 5B variant achieves ~5 Hz.

## Datasets & evaluation
Training uses web-scale data from PaLI-X and PaLM-E combined with robot demonstrations from RT-1 (collected over 17 months with 13 robots in an office kitchen, covering 200+ tasks). Evaluation spans ~6,000 real-world trials across seen tasks (200+ instructions) and unseen generalization categories (novel objects, backgrounds, environments), each split into easy and hard cases. RT-2 matches RT-1 on seen tasks and achieves ~2x improvement on generalization over the next best baselines (RT-1, MOO), and ~6x over VC-1 and R3M. Emergent capabilities include placing objects near specific numbers/icons, interpreting inter-object relations, and chain-of-thought reasoning (e.g., selecting a rock as an improvised hammer, choosing an energy drink for a tired person).

## Limitations
Physical skills are still limited to the distribution seen in robot training data — the model cannot learn new motor primitives from web data alone. The largest model (55B) requires multi-TPU cloud inference at only 1-3 Hz, making on-robot deployment impractical. Evaluation is limited to a single robot morphology (7-DoF mobile manipulator with gripper) in primarily kitchen/office settings.

## Key takeaways
- Robot actions can be treated as just another language by tokenizing them into text, enabling VLMs to become VLAs without architectural changes.
- Co-fine-tuning on web and robot data simultaneously is essential — naive fine-tuning on robot data alone degrades generalization.
- Web-scale pretraining transfers semantic reasoning to robotic control as emergent capabilities, even though the model never saw these reasoning tasks paired with robot actions during training.
- This paper coined the term "vision-language-action model" and established the paradigm that subsequent work (OpenVLA, pi-0, DexVLA) builds upon.
