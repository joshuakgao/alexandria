---
topic: Object Detection
slug: object-detection
---

# Object Detection

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "object-detection")
SORT year DESC
```

## Overview

Object detection is the task of localizing and classifying objects within images or video frames, typically by predicting bounding boxes and class labels. It is one of the most mature and practically important problems in computer vision, underpinning applications from autonomous driving to robotics to surveillance. The field has evolved from two-stage detectors (R-CNN family) through single-stage approaches (YOLO, SSD) to end-to-end detection transformers (DETR and its variants), which eliminate hand-crafted components like non-maximum suppression and anchor boxes.

## Trends

- **End-to-end detection transformers** (DETR variants) are increasingly competitive with YOLO-family models at real-time latencies, removing the need for NMS and anchor tuning.
- **Neural architecture search** applied to detection enables automatic discovery of accuracy-latency tradeoffs from a single training run, rather than manually designing model size variants.
- **Internet-scale pretraining** (e.g., DINOv2) dramatically improves specialist detectors on small and diverse datasets, narrowing the gap with open-vocabulary VLMs while maintaining real-time speed.
- **Scheduler-free training** challenges the assumption that carefully tuned LR and augmentation schedules are necessary, showing that simpler approaches generalize better across diverse domains.

## Open questions

- Can weight-sharing NAS search spaces be learned rather than hand-designed?
- How to close the remaining gap between specialist and open-vocabulary detectors without sacrificing real-time inference?
- What is the optimal balance between pretraining scale and fine-tuning for domain-specific deployment?
- How should latency benchmarking be standardized across the field to enable fair comparisons?
