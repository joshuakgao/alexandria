---
title: "Stable Video Diffusion: Scaling Latent Video Diffusion Models to Large Datasets"
authors: [Andreas Blattmann, Tim Dockhorn, Sumith Kulal, Daniel Mendelevitch, Maciej Kilian, Dominik Lorenz, Yam Levi, Zion English, Vikram Voleti, Adam Letts, Varun Jampani, Robin Rombach]
year: 2023
tags: [image-video-generation]
url: "https://arxiv.org/abs/2311.15127"
date_ingested: 2026-06-30
---

# Stable Video Diffusion: Scaling Latent Video Diffusion Models to Large Datasets

![[2023-stable-video-diffusion-thumbnail.png]]

## Research gap
Prior video diffusion models varied widely in training methods, and the field lacked a systematic understanding of how data curation and multi-stage training strategies affect video generation quality. The impact of data selection — despite being well-recognized among practitioners — was heavily underrepresented in the literature, with most work focusing on architectural innovations instead.

## Contributions
- Identifies and validates a three-stage training pipeline for video LDMs: (I) text-to-image pretraining, (II) video pretraining on large curated data at low resolution, (III) high-quality video finetuning at higher resolution on a smaller dataset.
- Introduces a systematic data curation workflow for video: cascaded cut detection, synthetic captioning (CoCa, V-BLIP, LLM-based summarization), optical flow filtering, aesthetic scoring, and OCR-based text density filtering — applied to ~600M clips to produce curated subsets.
- Demonstrates through human preference studies that data curation during pretraining produces performance improvements that persist after high-quality finetuning, and that curated 10M samples outperform uncurated 10M and even established datasets (WebVid-10M, InternVid-10M).
- Trains state-of-the-art text-to-video and image-to-video models, with the image-to-video model preferred by human voters over closed-source systems GEN-2 and PikaLabs.
- Shows that video diffusion models learn a strong 3D/multi-view prior: finetuning SVD for multi-view synthesis outperforms specialized methods (Zero123XL, SyncDreamer) at a fraction of compute (12K steps, 16 hours on 8 A100s).
- Releases code and model weights publicly.

## Method
Architecture builds on Stable Diffusion 2.1, inserting temporal convolution and attention layers after every spatial layer (following Align Your Latents). Unlike prior work that freezes spatial layers, SVD finetunes the full model. Uses the EDM framework with noise schedule shifted toward higher noise values for high-resolution finetuning. Frame rate micro-conditioning allows control over temporal dynamics. For image-to-video, text embeddings are replaced with CLIP image embeddings, and the conditioning frame is concatenated channel-wise (noise-augmented) to the U-Net input. Classifier-free guidance scale is linearly increased across the frame axis to balance conditioning fidelity and saturation. Camera motion LoRAs are trained on temporal attention blocks using motion-categorized subsets. For multi-view synthesis, the image-to-video model is finetuned on Objaverse orbital renders with elevation conditioning. Data curation pipeline: cascaded cut detection at three FPS levels (revealing ~4× more cuts than metadata), three captioning methods (CoCa mid-frame, V-BLIP mid-frame, LLM summary of both), optical flow scoring to remove static scenes, CLIP-based aesthetic filtering, and OCR-based text density filtering.

## Datasets & evaluation
Large Video Dataset (LVD): ~577M clips from publicly available sources. LVD-F (filtered): ~152M clips after curation. High-quality finetuning set: ~1M (text-to-video) and ~250K (image-to-video) curated clips. UCF-101 zero-shot text-to-video: FVD 242.02, substantially outperforming all baselines (Make-A-Video 367.23, PYOCO 355.20, Video LDM 550.61). Human preference studies consistently show curated pretraining produces better models even after finetuning. Image-to-video preferred over GEN-2 and PikaLabs. Multi-view synthesis on GSO test set: LPIPS 0.14, PSNR 16.83, CLIP-S 0.89 — outperforming Zero123XL (0.20/14.51/0.87), SyncDreamer (0.18/15.29/0.88), and ablations without video prior.

## Limitations
- Text-to-video generation quality still lags behind image generation — temporal consistency and motion realism remain challenging at high resolution.
- The three-stage training pipeline is computationally expensive, requiring large-scale pretraining before task-specific finetuning.
- Data curation relies on heuristic thresholds (optical flow, aesthetic scores, text density) that may not transfer optimally across different video domains.
- Image-to-video model can produce artifacts with standard classifier-free guidance, requiring the linearly increasing guidance schedule workaround.
- Multi-view generation is demonstrated only on synthetic objects (Objaverse, GSO) and casual captures (MVImgNet) — generalization to complex real-world scenes is untested.

## Key takeaways
- Data curation is the most underappreciated factor in video generation quality. Systematic filtering (cut detection, motion filtering, aesthetic scoring) on pretraining data produces improvements that persist through finetuning — even small curated datasets outperform larger uncurated ones.
- The three-stage pipeline (image pretrain → video pretrain → video finetune) is strictly better than skipping video pretraining, validating the approach of progressively specializing a foundation model.
- Video diffusion models learn implicit 3D priors: finetuning for multi-view synthesis works remarkably well with minimal compute (12K steps), outperforming specialized 3D methods. This suggests video generation and 3D understanding are deeply connected, and video models may help overcome data scarcity in the 3D domain.
- Shifting the noise schedule toward higher noise values is critical for high-resolution finetuning — a practical insight from the image domain (EDM) that transfers directly to video.
- SVD established the open-source foundation for video generation, analogous to Stable Diffusion's role for image generation, enabling a broad ecosystem of downstream applications and adaptations.
