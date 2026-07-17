---
topic: Vision-Language-Action Models
slug: vision-language-action
---

# Vision-Language-Action Models

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "vision-language-action")
SORT year DESC
```

## Overview
Vision-language-action (VLA) models extend vision-language models by adding action prediction capabilities for robot control. These models leverage internet-scale pretraining on images and text, then fine-tune on robot demonstration data to output low-level motor commands. VLAs represent a convergence of foundation model scaling, imitation learning, and embodied AI, aiming to create generalist robot policies that can interpret natural language instructions and act in the physical world. RT-2 (Google DeepMind, CoRL 2023) coined the term and established the paradigm by showing that VLMs can be co-fine-tuned on web and robot data to directly output tokenized actions, inheriting emergent semantic reasoning from web pretraining. DexVLA (CoRL 2025) challenged the assumption that scaling should focus on the VLM backbone, demonstrating that a billion-parameter diffusion action expert paired with a small VLM achieves strong cross-embodiment performance from only 100 hours of data. GR00T N1 (NVIDIA, 2025) introduced the data pyramid strategy for humanoid robots — layering web/human videos, synthetic data, and real demonstrations — with a dual-system architecture (VLM reasoning at 10Hz + DiT action generation at 120Hz), achieving 76.8% success on real-world GR-1 humanoid tasks and strong data efficiency. ChatVLA-2 (2025) addresses the fundamental tension between robot fine-tuning and VLM knowledge preservation, introducing dynamic MoE to disentangle reasoning from control and a two-stage training strategy that enables open-world embodied reasoning — achieving 82.7% success on unseen math equations where all prior VLAs score near zero.

## Trends
- **Actions as language tokens**: RT-2 established the foundational approach of discretizing robot actions into text tokens and training VLMs to predict them alongside natural language, requiring no architectural modifications.
- **Co-fine-tuning over naive fine-tuning**: Jointly training on web-scale vision-language data and robot trajectories is critical for generalization — robot-only fine-tuning degrades the semantic reasoning inherited from pretraining.
- **Emergent capabilities from web pretraining**: VLA models exhibit reasoning capabilities never seen in robot training data — symbol understanding, inter-object relation reasoning, and chain-of-thought inference — transferred from internet-scale pretraining.
- **Scaling action experts independently from VLM backbones**: DexVLA demonstrates that scaling the diffusion-based action expert to 1B parameters while keeping the VLM small (2B) is a viable alternative to scaling the language model.
- **Curriculum learning strategies** that progressively train on harder tasks across multiple embodiments.
- **Integration of diffusion models** as action decoders for handling multimodal action distributions.
- **Sub-step reasoning annotations** to enable end-to-end long-horizon task completion without external high-level planners.
- **Data pyramid for humanoid robots**: GR00T N1 structures heterogeneous training data by scale — web videos and human demonstrations at the base, synthetic/neural trajectories in the middle, real robot data at the top — with latent-action codebooks and IDM pseudo-labels to learn from action-less video sources.
- **Dual-system architectures**: Separating slow reasoning (VLM at 10Hz) from fast action generation (DiT at 120Hz) via cross-attention, enabling real-time humanoid control while retaining language understanding.
- **Neural trajectory augmentation**: Fine-tuning video generation models on robot data to produce counterfactual training videos with pseudo-action labels, consistently improving performance across data regimes.
- **Mixture-of-Experts for knowledge preservation**: ChatVLA introduced static MoE on MLP layers (control expert + understanding expert with shared attention) to isolate task-specific representations while enabling cross-task knowledge transfer. ChatVLA-2 extended this to dynamic MoE (8 experts, top-2 routing) to further disentangle multi-modal understanding from robot control within the VLM backbone.
- **Spurious forgetting over catastrophic forgetting**: ChatVLA's systematic analysis reveals that VLA fine-tuning causes "spurious forgetting" — not true knowledge erasure but misalignment of visual-text representations. The finding that even structured reasoning templates can partially reactivate conversational ability suggests VLM knowledge persists but becomes inaccessible through standard prompting after robot training.
- **Phased alignment training**: ChatVLA established the curriculum-based approach of first training on robot data (to master control), then co-training with visual-text data (to reactivate understanding). ChatVLA-2 reversed and extended this: co-training first (to establish reasoning), then freezing the VLM and training only the action expert (to strengthen reasoning-following), enabling VLAs to generalize to entirely unseen reasoning scenarios.
- **Reasoning-following action experts**: Injecting VLM reasoning tokens into the action expert's conditioning (via scale/shift parameters in latter-half layers) creates a tighter coupling between open-world reasoning and motor execution than FiLM-based approaches.

## Open questions
- How to achieve true zero-shot transfer across entirely new robot morphologies without embodiment-specific heads.
- Optimal balance between VLM backbone scale and action expert scale for data-efficient learning — RT-2 scales VLMs to 55B while DexVLA scales action experts to 1B with a 2B VLM.
- Reducing reliance on human demonstrations through simulation, self-play, or other automated data generation.
- Handling contact-rich, deformable-object manipulation reliably in long-horizon settings.
- Whether the actions-as-tokens approach (RT-2) or dedicated diffusion action experts (DexVLA, GR00T N1) is more effective as both data and model scale increase.
- Catastrophic forgetting during post-training — GR00T N1 shows that task-specific fine-tuning can erase pre-trained capabilities (e.g., inter-hand transfer lost after right-hand-only data).
- Quality and physical fidelity of neural trajectory generation — current video models still struggle with counterfactual diversity and physics adherence.
- How to fully preserve VLM pre-trained knowledge during robot fine-tuning — ChatVLA's static MoE and ChatVLA-2's dynamic MoE mitigate but do not eliminate capability loss. The optimal MoE configuration (static vs. dynamic, number of experts, routing strategy) for different VLM architectures is unexplored.
- Whether spurious forgetting (ChatVLA) is a universal phenomenon across VLM architectures or specific to certain backbone families — and whether the degree of recoverability varies with model scale or pretraining data composition.
- Whether open-world reasoning generalizes beyond tabletop manipulation to mobile manipulation and long-horizon tasks requiring sustained reasoning chains.
