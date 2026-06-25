---
title: "A General Framework for Jersey Number Recognition in Sports Video"
authors: [Maria Koshkina, James H. Elder]
year: 2024
venue: "CVPRW 2024"
tags: [sports-analytics, re-id]
url: "https://openaccess.thecvf.com/content/CVPR2024W/CVsports/papers/Koshkina_A_General_Framework_for_Jersey_Number_Recognition_in_Sports_Video_CVPRW_2024_paper.pdf"
pdf: "[[2024-jersey-number-recognition.pdf]]"
date_ingested: 2026-06-25
---

# A General Framework for Jersey Number Recognition in Sports Video

![[2024-jersey-number-recognition-thumbnail.png]]

## Research gap
Jersey number recognition is critical for long-term player tracking in sports video, but prior methods treat it as a specialized classification problem requiring large proprietary datasets and dedicated networks trained from scratch. These approaches cannot generalize to unseen jersey numbers and lack publicly available datasets for benchmarking. Scene text recognition (STR) models, despite being a natural fit, had not been systematically applied to this task.

## Contributions
- A novel public image-level hockey jersey number dataset combining university and NHL broadcast sources with legibility and number annotations.
- A simple yet high-performance pipeline — legibility classification (ResNet34), pose-based torso cropping (ViTPose), and scene text recognition (PARSeq) — achieving 91.4% image-level accuracy on hockey, outperforming all prior methods.
- Extension to tracklet-level recognition via re-identification-based main subject filtering and prediction consolidation (heuristic and probabilistic), achieving 87.45% on SoccerNet test — a 19-point improvement over the previous best (68.53%).
- Cross-sport generalization analysis showing the pipeline transfers from hockey to soccer with minimal fine-tuning.

## Method
**Image-level:** (1) A ResNet34 binary legibility classifier (fine-tuned on balanced hockey data with SAM optimization) filters frames where jersey numbers are visible. (2) ViTPose body pose estimation localizes the torso region via shoulder and hip keypoints. (3) PARSeq, a state-of-the-art scene text recognition model pre-trained on synthetic and real-world text datasets, is fine-tuned on a small set of hockey jersey crops to recognize numbers. **Tracklet-level:** (1) Centroid-ReID extracts visual feature vectors for each frame; an iterative Gaussian outlier rejection (K=3 rounds, N=3.5 std) removes frames where the main subject is occluded by other players. (2) The legibility classifier is retrained with weak pseudo-labels derived from tracklet-level annotations. (3) PARSeq predictions across legible frames are consolidated via confidence-weighted majority vote with a bias toward two-digit numbers (since single-digit predictions often result from partial occlusion of two-digit numbers).

## Datasets & evaluation
**Hockey dataset** (novel): 132,983 images from 17 games (university + NHL broadcast), 7,787 legible, 4,250 with jersey number labels. **SoccerNet Jersey Number dataset**: 4,064 tracklets, ~2M frames from professional soccer broadcast. Image-level accuracy: 91.4% on hockey (vs. 90.4% prior best). Tracklet-level accuracy: 87.45% on SoccerNet test, 79.31% on challenge (vs. 68.53%/73.77% prior best). PARSeq achieves 85.4% out-of-the-box without any fine-tuning on jersey data. Cross-sport transfer: hockey-trained model achieves 83.9% on soccer without soccer fine-tuning.

## Limitations
- Relies on body pose estimation for torso localization, which can fail under extreme occlusion or unusual poses.
- The legibility classifier requires sport-specific fine-tuning with at least weak labels for optimal tracklet-level performance.
- Probabilistic consolidation underperforms the simpler heuristic approach, suggesting the STR confidence calibration is imperfect.
- Evaluated only on hockey and soccer; generalization to sports with different jersey layouts (e.g., basketball, American football) is untested.
- Does not integrate jersey number recognition into end-to-end tracking — the pipeline assumes pre-existing tracklets.

## Key takeaways
- Scene text recognition models (PARSeq) are a strong foundation for jersey number recognition, achieving 85.4% accuracy out-of-the-box and 91.4% with minimal fine-tuning — eliminating the need for large proprietary datasets or custom classification networks.
- Re-identification-based outlier filtering is effective for handling occlusion in tracklets without requiring frame-level occlusion annotations.
- The two-digit confusion problem (partial occlusion causing two-digit numbers to be read as single digits) is a dominant error mode, addressable through simple heuristic biasing.
- Cross-sport transfer works surprisingly well, with the hockey-trained pipeline achieving competitive performance on soccer data, suggesting jersey number recognition benefits from sport-agnostic visual features learned by STR pre-training.
