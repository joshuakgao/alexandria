---
title: "RF-DETR: Neural Architecture Search for Real-Time Detection Transformers"
authors: [Isaac Robinson, Peter Robicheaux, Matvei Popov, Deva Ramanan, Neehar Peri]
year: 2026
venue: "ICLR 2026"
tags: [object-detection, segmentation]
url: "https://arxiv.org/abs/2511.09554"
date_ingested: 2026-08-09
---

# RF-DETR: Neural Architecture Search for Real-Time Detection Transformers

![[2026-rf-detr-thumbnail.png]]

## Research gap

Open-vocabulary detectors generalize poorly to out-of-distribution classes, while specialist real-time detectors (YOLO variants, RT-DETR, D-FINE) implicitly overfit to COCO through bespoke architectures, schedulers, and augmentation pipelines that assume specific dataset characteristics. No existing method combines internet-scale pretraining with systematic architecture search to produce real-time detectors that transfer well to diverse target domains. Additionally, latency benchmarking across the detection literature is inconsistent and irreproducible due to GPU power throttling.

## Contributions

- **RF-DETR**: a family of scheduler-free, NAS-based detection transformers that achieve state-of-the-art among real-time methods on both COCO and Roboflow100-VL. First real-time detector to exceed 60 AP on COCO.
- **End-to-end weight-sharing NAS for detection**: explores five "tunable knobs" (patch size, decoder layers, query tokens, image resolution, window count) to discover accuracy-latency Pareto curves from a single training run without retraining.
- **RF-DETR-Seg**: extends the approach to instance segmentation with a lightweight head, also supporting NAS-based Pareto optimization.
- **Standardized latency benchmarking**: identifies GPU power throttling as a major source of irreproducibility and proposes a simple buffering protocol to fix it.

## Method

RF-DETR modernizes LW-DETR by replacing the CAEv2 backbone with a DINOv2 ViT pretrained on internet-scale data. The architecture uses interleaved windowed and non-windowed attention blocks, a multi-scale projector with layer norm (enabling gradient accumulation on consumer GPUs), and deformable cross-attention in the decoder. Detection and segmentation losses are applied at all decoder layers to support decoder dropout at inference.

The weight-sharing NAS works as follows: at each training iteration, a random model configuration is sampled from the search space and a gradient update is performed, effectively training thousands of sub-nets in parallel. The five search dimensions are:

1. **Patch size** — interpolated via FlexiViT-style transformation; smaller patches increase accuracy at higher cost.
2. **Decoder layers** — any subset of decoder blocks can be dropped at inference; removing all blocks yields a single-stage detector.
3. **Query tokens** — dropped by maximum class logit confidence; the optimal count encodes dataset statistics about objects per image.
4. **Image resolution** — positional embeddings are pre-allocated for the largest resolution and interpolated for smaller ones.
5. **Window count** — controls the granularity of windowed self-attention blocks.

The approach is scheduler-free: no cosine LR schedule or aggressive augmentations (only horizontal flips and random crops). This avoids implicit biases toward specific dataset sizes or distributions. For segmentation, a lightweight head bilinearly interpolates encoder output to produce pixel embeddings, with query-pixel dot products generating masks. RF-DETR-Seg is pretrained on Objects-365 pseudo-labeled with SAM2 masks.

## Datasets & evaluation

**COCO**: RF-DETR (nano) achieves 48.0 AP, beating D-FINE (nano) by 5.3 AP at similar latency. RF-DETR (2x-large) reaches 60.1 AP — the first real-time detector above 60 AP. RF-DETR-Seg (nano) outperforms YOLOv11-Seg (x-large) while running 4x faster.

**Roboflow100-VL (RF100-VL)**: RF-DETR (2x-large) outperforms GroundingDINO (tiny) by 1.2 AP while running 20x faster, demonstrating strong transfer to diverse real-world domains.

**Latency standardization**: by buffering 200ms between forward passes to prevent GPU power throttling, the authors show that reported latencies in prior work are often inconsistent. All measurements use NVIDIA T4 with TensorRT 10.4.

## Limitations

- The NAS search space is hand-designed; the five knobs were chosen based on domain knowledge rather than learned.
- Architecture augmentation regularization requires more than 100 epochs to converge on small datasets, necessitating optional fine-tuning of NAS-mined models on RF100-VL.
- Latency standardization via buffering measures single-query latency rather than sustained throughput, which may not reflect all deployment scenarios.
- The segmentation head intentionally omits multi-scale backbone features to minimize latency, potentially sacrificing mask quality for speed.

## Key takeaways

- Weight-sharing NAS applied end-to-end to detection transformers is highly effective: a single training run produces an entire Pareto frontier of models, and sub-nets not seen during training still achieve strong performance.
- Internet-scale pretraining (DINOv2) is a key enabler — it dramatically improves detection on small datasets where previous specialist detectors struggled.
- Training schedulers and aggressive augmentations are a hidden source of benchmark overfitting. Scheduler-free training with minimal augmentation generalizes better across diverse domains.
- The "architecture augmentation" effect — randomly sampling different configurations during training — acts as a regularizer, similar to dropout at the architecture level.
- GPU power throttling is a major confound in latency benchmarking that the field has largely ignored.
