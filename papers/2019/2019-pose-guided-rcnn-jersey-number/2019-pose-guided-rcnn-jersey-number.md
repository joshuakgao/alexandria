---
title: "Pose-Guided R-CNN for Jersey Number Recognition in Sports"
authors: [Hengyue Liu, Bir Bhanu]
year: 2019
venue: "CVPRW 2019"
tags: [sports-analytics, re-id]
url: "https://openaccess.thecvf.com/content_CVPRW_2019/papers/CVSports/Liu_Pose-Guided_R-CNN_for_Jersey_Number_Recognition_in_Sports_CVPRW_2019_paper.pdf"
pdf: "[[2019-pose-guided-rcnn-jersey-number.pdf]]"
date_ingested: 2026-06-25
---

# Pose-Guided R-CNN for Jersey Number Recognition in Sports

![[2019-pose-guided-rcnn-jersey-number-thumbnail.png]]

## Research gap
Prior jersey number recognition methods either relied on brittle OCR pipelines with heavy pre/post-processing, or treated the problem as single-number classification on tightly cropped patches. None exploited the geometric relationship between human body pose and digit location to suppress false positives (yard markers, clocks, banners) or to handle multi-player scenes with multiple jersey numbers per image.

## Contributions
- A three-class Region Proposal Network (RPN) that jointly proposes background, person, and digit regions, enabling geometric association between person and digit proposals to filter false positives.
- A pose-guided bounding-box regressor that uses predicted human body keypoints to refine digit localization, leveraging the spatial relationship between body parts and jersey number placement.
- State-of-the-art jersey number recognition results with support for multiple players and digits per image, unlike prior single-number approaches.
- A new dataset of 3,567 soccer-match images annotated with person and digit bounding boxes, human body keypoints, and digit segmentation masks.

## Method
The framework extends Faster R-CNN with RoIAlign (from Mask R-CNN) in two key ways. First, the RPN is modified to output three classes (background, person, digit) instead of the standard two (foreground, background). Person and digit proposals are collected separately and associated via a graph matching formulation — each person node is matched to its top-2 nearest digit nodes by Euclidean distance between bounding-box centers. Second, a human body keypoint prediction branch (predicting K keypoint heatmaps per RoI) provides a pose-guided supervision signal. Predicted keypoint masks are concatenated with pooled digit features before the final classification and bounding-box regression heads, encouraging the model to localize digits relative to body structure. The backbone uses ResNet-50 with FPN for multi-scale features. A lightweight variant achieves 12 fps on a GTX 1080 Ti.

## Datasets & evaluation
Evaluated on a custom dataset of 3,567 soccer-match video frames with multi-digit annotations. The pose-guided R-CNN achieves the best digit detection AP (81.3% at IoU 0.5) and jersey number recognition accuracy (78.7%) compared to baselines including Faster R-CNN (72.9% / 68.2%), Mask R-CNN (76.8% / 72.4%), and the 3-class RPN variant without pose guidance (79.2% / 75.1%). Qualitative results on web-scraped images from basketball, American football, and other sports demonstrate cross-sport applicability.

## Limitations
- Only back-facing jersey numbers are considered; front/side numbers are excluded.
- The dataset is relatively small (3,567 images) and limited to soccer, with cross-sport evaluation only qualitative.
- The graph matching for person-digit association assumes at most two digits per player, which fails for three-digit numbers.
- No comparison with contemporary scene text detection/recognition methods that could serve as strong baselines.
- The dataset was not publicly released at publication time despite stated intentions.

## Key takeaways
- Exploiting the geometric relationship between human pose and digit location is an effective inductive bias for jersey number recognition — it reduces false positives from environmental text (yard markers, banners) that lack associated person detections.
- A minimal modification to the RPN (2-class to 3-class) with negligible parameter increase provides substantial gains by enabling structured proposal association.
- This work established the detection-based paradigm for jersey number recognition, later superseded by scene text recognition approaches (e.g., Koshkina & Elder 2024) that leverage pre-trained STR models for higher accuracy with less sport-specific engineering.
