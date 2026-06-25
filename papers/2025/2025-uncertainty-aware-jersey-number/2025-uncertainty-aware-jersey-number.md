---
title: "Single-Stage Uncertainty-Aware Jersey Number Recognition in Soccer"
authors: [Lukasz Grad]
year: 2025
venue: "CVPRW 2025"
tags: [sports-analytics, re-id]
url: "https://openaccess.thecvf.com/content/CVPR2025W/CVSPORTS/papers/Grad_Single-Stage_Uncertainty-Aware_Jersey_Number_Recognition_in_Soccer_CVPRW_2025_paper.pdf"
pdf: "[[2025-uncertainty-aware-jersey-number.pdf]]"
date_ingested: 2026-06-25
---

# Single-Stage Uncertainty-Aware Jersey Number Recognition in Soccer

![[2025-uncertainty-aware-jersey-number-thumbnail.png]]

## Research gap
Prior jersey number recognition methods were either multi-stage pipelines (detect region, then recognize) that propagate errors and cannot quantify end-to-end confidence, or single-stage models that lack principled uncertainty estimation. No prior work addressed when a model should abstain from prediction — critical for frames with occlusion, motion blur, or partial visibility where deterministic models produce overconfident but incorrect outputs.

## Contributions
- A digit-compositional jersey number head architecture (Tied Digit-Aware / TDA) that decomposes two-digit numbers into constituent digits with shared weights and position embeddings, reducing parameters by 86% versus independent classification while enabling compositional generalization.
- Integration of Dirichlet-based evidential uncertainty modeling that produces calibrated confidence estimates in a single forward pass, enabling the model to express uncertainty over similar-looking numbers and abstain on ambiguous frames.
- A single-stage end-to-end architecture that directly processes player crops via a ViT backbone without requiring explicit jersey detection or text localization.
- Uncertainty-aware tracklet aggregation that filters low-confidence frame predictions before combining, improving tracklet-level accuracy.
- State-of-the-art results on SoccerNet Challenge (85.62% tracklet accuracy), surpassing prior best published result (Koshkina et al., 79.31%).

## Method
The architecture has three components: (1) a pre-trained ViT backbone (ViT-B/8, ImageNet-21k) extracts features from torso-cropped player images (top 1/6 and bottom 1/3 removed); (2) a jersey number head maps features to 100-class scores; (3) a classification head produces probabilities and uncertainty. The key TDA head uses learnable position embeddings (additive or multiplicative) to distinguish single-digit, tens-place, and ones-place positions, with a shared digit classifier across all positions. Two-digit scores are composed by summing tens and ones digit scores. For uncertainty, network outputs are interpreted as evidence parameters of a Dirichlet distribution — high total evidence produces peaked distributions (low uncertainty), low evidence produces flat distributions (high uncertainty). Training uses Type-II Maximum Likelihood loss. At tracklet level, frame predictions are aggregated with confidence-based filtering: only frames with uncertainty below a threshold contribute to the final prediction. A pseudolabeling pipeline using uncertainty filtering extends training data from the SoccerNet ReID dataset.

## Datasets & evaluation
Evaluated on SoccerNet (Train/Test/Challenge splits) and a proprietary Copa America dataset (33,730 tracklets from 12 matches). The best model (ViT-B/8 trained on 200M proprietary dataset) achieves 85.62% on SoccerNet Challenge, up from 79.31% (Koshkina et al. 2024). A public-data-only variant (ViT-B, SoccerNet + pseudolabeled ReID) achieves 83.52% on Challenge. Digit-compositional heads (DA, TDA) consistently outperform independent heads (+1–2%). Dirichlet uncertainty improves over softmax by +1–2% on visible numbers and +5–6% on invisible/ambiguous cases. Ablations confirm that uncertainty-aware tracklet aggregation and the compositional digit structure each contribute meaningfully.

## Limitations
- Best results require a large proprietary dataset (200M, 594K tracklets from 200+ matches) that is not publicly available, though public-data results are still competitive.
- Only evaluated on soccer; generalization to other sports with different jersey styles, fonts, or three-digit numbers is untested.
- The torso-cropping preprocessing assumes reliable person detection bounding boxes; errors in detection propagate.
- Pseudolabeling for the ReID dataset uses a proprietary jersey number detector, limiting full reproducibility.
- No comparison with scene text recognition approaches (e.g., PARSeq-based methods) that could serve as strong baselines.

## Key takeaways
- Digit-compositional classification is a consistently better inductive bias than treating each jersey number as an independent class — it enables compositional generalization to number combinations underrepresented in training.
- Dirichlet-based uncertainty provides the largest gains on the hardest cases (invisible/ambiguous numbers), where softmax models are overconfident. The ability to abstain is as important as the ability to predict.
- The progression from detection-based (Liu & Bhanu 2019), to STR-based (Koshkina & Elder 2024), to single-stage uncertainty-aware (Grad 2025) shows the field converging on end-to-end architectures that eliminate multi-stage error propagation.
- Uncertainty-aware pseudolabeling is a practical strategy for expanding training data when only tracklet-level annotations exist.
