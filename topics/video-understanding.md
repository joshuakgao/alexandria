---
topic: Video Understanding
slug: video-understanding
---

# Video Understanding

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "video-understanding")
SORT year DESC
```

## Overview

# Video Understanding — Overview

*Last updated: 2026-04-09 | Sources: 13 papers*

## Current thesis

Video understanding is consolidating around **large language models (LLMs) as task-agnostic reasoners**, replacing specialized single-task models. The shift reflects two innovations: (1) **architectural variety** — LLMs can receive inputs as text (analyst outputs), embeddings (dense vectors), or both, leading to three main paradigms (Analyzer×LLM, Embedder×LLM, hybrid); (2) **functional flexibility** — LLMs fill different roles (Summarizer, Manager, Text Decoder, Regressor, Hidden Layer) depending on task requirements. Temporal grounding is the bottleneck: RL-based approaches (TimeSearch-R) excel at adaptive search; supervised approaches (MOMA-QA) excel at precise localization when ground truth is available; no single method dominates both. The LLM framework unifies previously disparate tasks (QA, captioning, grounding, summarization) into generation/regression variants.

## Key trends

**2016 (Benchmark era begins):**
- **Multimodal video QA is established as a task.** [[papers/2016-movieqa|MovieQA]] introduces the first large-scale video question-answering dataset with 14,944 questions across 408 movies, integrating multiple modalities—plots, subtitles, video clips, scripts, and DVS transcriptions—to evaluate story comprehension. Unlike image-QA that focuses on object recognition, MovieQA requires reasoning about character motivation, causality, and temporal progression. The dataset establishes that no single modality suffices; understanding stories requires multimodal fusion. This work sets the template for future video-language benchmarks and frames video understanding as a reasoning task rather than a recognition task. The emphasis on "Why" and "How" questions (requiring temporal and semantic reasoning) contrasts sharply with simpler visual QA and signals the direction the field will take.

**2019 (Foundational architectures):**
- **Dual-pathway temporal-spatial decomposition emerges.** [[papers/2019-slowfast-networks|SlowFast Networks]] introduces the principle that categorical semantics evolve slowly (spatial pathway, low frame rate) while motion changes rapidly (temporal pathway, high frame rate). This biologically-inspired decomposition proves highly efficient: the Fast pathway adds only ~20% computation while capturing essential motion. SlowFast establishes the template for subsequent multi-rate video architectures and influences how later video-LLMs think about temporal processing efficiency. Modern temporal search methods (T*, TimeSearch-R, AKEYS) inherit the implicit assumption that not all frames need equal processing.

**2024:**
- **Chat-centric video understanding emerges as a paradigm.** [[papers/2024-videochat|VideoChat]] pioneers the formulation of video understanding as multi-round dialogue, integrating video foundation models (InternVideo) with LLMs (ChatGPT, LLaMA) through learnable token interfaces. Two pathways—VideoChat-Text (textual descriptions) and VideoChat-Embed (direct embeddings)—enable flexibility. The paper creates the first large-scale video instruction dataset with ChatGPT-generated conversations, establishing a new task format that replaces task-specific fine-tuning with unified dialogue-based reasoning.
- **Retrieval-augmented generation enters video domain.** [[papers/2024-vrag-retrieval-augmented-video-qa|VRAG]] adapts the RAG paradigm from NLP to long-form VideoQA, using multimodal retrieval (semantic + audio + OCR + objects) followed by temporal re-ranking. The retrieve-first approach contrasts with end-to-end Video-LLMs and foreshadows later agent-guided and RL-based search methods; RAG provides a natural baseline architecture for frame-efficient video understanding.

**2026 (Survey retrospective):**
- **LLM architecture diversity is the norm.** Three paradigms have emerged: Video Analyzer×LLM (text input, training-free), Video Embedder×LLM (embedding input, fine-tuned), and hybrids. The choice trades off computational cost vs. information preservation; no single paradigm dominates all tasks.
- **Adapter-based fine-tuning dominates.** Connective adapters (Q-formers, projections) align visual space to LLM token space. Insertive adapters (LoRA) modify LLM behavior for regression/grounding. Hybrid two-stage training of both is increasingly common; full LLM fine-tuning is rare due to cost.
- **Foundation model scale matters.** Larger LLMs (34B vs. 7B) provide consistent zero-shot gains; visual encoder quality (EVA-CLIP vs. standard ViT) similarly important. Gains compound: best models have strong both foundations.
- **Evaluation is undergoing flux.** GPT-3.5/4 evaluation of open-ended answers is popular but unstable (varies with GPT version, prompt engineering). Closed-ended benchmarks (multiple-choice) more stable but may not reflect open-form reasoning capability.
- **Temporal reasoning is architecturally dependent.** Dense temporal modeling (100+ frames sampled densely) critical for grounding tasks. Static models processing 8–10 frames fail on temporal reasoning; specialized temporal models (VTimeLLM, ST-LLM) needed.
- **Data composition is an orthogonal efficiency axis.** [[2026-videoweave|VideoWeave]] demonstrates that splicing short videos into synthetic long-context samples improves training efficiency without architectural changes. This is a complementary direction to model-centric improvements: rather than designing better architectures, organizing training data strategically yields compute-efficient gains.

**2025:**
- **Temporal search becomes the bottleneck.** [[papers/2025-re-thinking-temporal-search-long-form-video|T*]] introduces LV-HAYSTACK (480h, 15,092 annotated instances), revealing that current SOTA achieves only 2.1% temporal F1 despite high downstream task accuracy. This decoupling shows that task performance and search capability are related but distinct; future work must target temporal search directly, not just downstream metrics.
- **Reinforcement learning is entering the video domain.** The success of GRPO-style RL for text reasoning (DeepSeek-R1) is being transferred to video, with TimeSearch-R as the leading example. The key challenge specific to video is that the model must learn to *search* as well as *reason* — the frame context is not static.
- **Language agents enable efficient search without RL training.** [[papers/2025-agentic-keyframe-search|AKEYS]] demonstrates that language agents can guide classical search algorithms (A*, Dijkstra's) through hierarchical video trees just as effectively as RL-trained models, with less training overhead. Agent-guided temporal navigation using heuristic and movement costs emerges as a competing paradigm to RL-based dynamic search.
- **Token-level efficiency is orthogonal to frame selection.** [[papers/2025-keyframe-oriented-vision-token-pruning|KVTP]] demonstrates that even after selecting keyframes, substantial token reduction is possible through query-aware adaptive pruning (80% token reduction). This orthogonal efficiency axis complements frame selection: KVTP could enhance TimeSearch-R or AKEYS by reducing per-frame token overhead further.
- **Spatial search transfers to temporal domain.** T* reformulates temporal search as spatial search by transforming frame sequences into image grids and applying iterative coarse-to-fine spatial zooming. This cross-domain reformulation achieves 3x efficiency gains and shows that image-language models outperform video-language models for temporal search—suggesting the temporal dimension is the true bottleneck, not video processing per se.
- **Interleaved text-video thinking is an emerging paradigm.** Rather than encoding all video up front, models are learning to dynamically request relevant frames mid-reasoning. This mirrors how humans watch videos: we don't process every frame uniformly but seek specific moments when they become relevant.
- **Fine-grained multi-actor understanding is new.** MOMA-QA (Dai et al., 2025) introduces 300K QA pairs with temporal + bounding box annotations, forcing models to reason about specific entities in crowded scenes. SGVLM's scene graph integration shows explicit spatial structure helps.
- **Scene graphs are resurging as a complement to LLM-based video reasoning.** SGVLM shows that for relationship-type questions in crowded scenes, explicit graph structure over entities and relations (via Neural Motifs) provides gains that pure visual embeddings cannot match.
- **Sparse-event benchmarks emerge.** KVTP introduces SparseKV-QA, a unified benchmark highlighting long-form videos with sparse but crucial events. This fills a gap in existing benchmarks and enables fair evaluation of token compression methods under realistic conditions where not all frames are equally important.
- **Embodied cognition in 3D space is a new frontier.** [[papers/2025-urbanvideo-bench|UrbanVideo-Bench]] tests Video-LLMs on aerial/egocentric understanding in complex urban environments, revealing that current models struggle with spatial reasoning, counterfactual reasoning, and first-person navigation. Strong correlation between causal and other reasoning types suggests reasoning about causality is foundational; counterfactual reasoning remains a separate, harder cognitive task. Sim-to-Real transfer feasibility opens pathways for embodied AI training.
- **Long context + fine-grained annotations = practical scaling path.** [[papers/2025-internvideo2.5|InternVideo2.5]] demonstrates that pairing hierarchical token compression (context length) with task preference optimization (context fineness) yields 6x memory expansion without task-specific decoders. This orthogonal scaling approach (not model size, but context richness) shows promise for emergent vision capabilities like tracking and segmentation, suggesting that annotation transfer via DPO is an underexplored but practical efficiency axis.

## Open problems

- **Hierarchical search over very long content** (>1 hour): current models cap at ~768 frames; multi-level coarse-to-fine temporal search remains unsolved. Data composition (e.g., nested or hierarchical splicing) could help train models to reason at multiple scales, but this has not been explored.
- **Temporal grounding precision**: models that answer correctly may still localise the relevant moment imprecisely; MOMA-QA is one of the first benchmarks to provide ground-truth temporal intervals for direct evaluation; SGVLM's frame localizer shows this is tractable with supervision
- **Entity-specific reasoning in crowds**: MOMA-QA's bounding box augmentations sidestep the problem via preprocessing — but at inference time, locating the right entity without oracle boxes remains unsolved
- **Multi-event reasoning**: questions requiring integration of events spread across the full video length (not just a single temporal window) remain very challenging
- **Evaluation metric stability**: GPT-based evaluation is prone to drift across LLM versions; no consensus on robust metrics for open-ended video understanding
- **Causal reasoning**: distinguishing correlational from causal relationships in video remains weak in all current models
- **Relevance prediction accuracy**: frame relevance prediction (as in KVTP) depends on accurate query understanding; no systematic analysis of prediction error propagation or calibration across query types
- **Generalization of token pruning**: whether learned pruning rates transfer across datasets, video domains, or task types remains unclear; most work evaluates on held-in data
- **Cascading efficiency improvements**: KVTP, T*, TimeSearch-R, and AKEYS each provide efficiency gains; no work has combined multiple approaches to understand composability and diminishing returns

## Contradictions and debates

- **RL vs. SFT for temporal reasoning**: TimeSearch-R ablations show RL training is *not* strictly necessary for temporal search (SFT alone achieves reasonable F₁), but RL is critical for long-form video QA accuracy. The two tasks appear to require different inductive biases.
- **Egocentric data importance**: Ego4D data improves both temporal and action reasoning substantially (+7.4%, +5.7%); this suggests domain-specific data composition matters significantly for video RL training, not just scale.
- **Supervised vs. RL-trained localisation**: SGVLM uses a supervised frame localizer (trained on ground-truth temporal intervals from MOMA-QA) and achieves strong moment retrieval performance; TimeSearch-R uses RL to learn implicit temporal search. These approaches have not been compared head-to-head, and it is unclear how SGVLM's localizer would perform without annotation supervision.
- **Analyzer vs. Embedder efficiency trade-off**: Text-based approaches are training-free but lose visual information in language conversion; embedding-based approaches preserve signal but require fine-tuning (cost/computational). Survey shows both are viable; choice is task/resource-dependent, not hierarchical.
- **Hard vs. soft frame selection**: AKEYS and KVTP take different approaches—AKEYS does binary keyframe selection, KVTP applies soft per-frame pruning rates. No direct comparison; soft pruning may be more expressive but harder to interpret, while hard selection is cleaner but may discard valuable context.

## Recommended reading order

1. [[papers/2016-movieqa|MovieQA (2016)]] — **foundational benchmark** establishing video QA as a multimodal reasoning task; demonstrates that story comprehension requires integrating multiple information sources and reasoning about causality and intent, not just visual recognition
2. [[papers/2019-slowfast-networks|SlowFast Networks (2019)]] — **foundational architecture** dual-pathway separation of spatial (slow) and temporal (fast) processing; establishes efficiency principle (80/20 computation split) that informs all modern temporal search approaches
3. [[papers/2026-video-understanding-large-language-models-survey|Video Understanding With LLMs: Survey (2026)]] — **overview** of the taxonomy and landscape; provides context for both downstream approaches and modern LLM-based methods
4. [[papers/2024-videochat|VideoChat (2024)]] — pioneering chat-centric system; establishes dialogue-based video understanding as an alternative to task-specific approaches; introduces video instruction tuning paradigm
5. [[papers/2024-vrag-retrieval-augmented-video-qa|VRAG (2024)]] — retrieval-augmented generation approach to long-form VideoQA; multimodal search + temporal re-ranking; establishes the retrieve-first paradigm that informs later temporal search methods
6. [[papers/2026-videoweave|VideoWeave (2026)]] — addresses the orthogonal problem of training data efficiency; shows how data composition can improve model performance without architectural changes; read alongside the survey to understand cost-performance trade-offs
7. [[papers/2025-moma-qa-fine-grained-video-question-answering|MOMA-QA / SGVLM (2025)]] — deep dive into supervised temporal grounding + scene graphs (Video Embedder×LLM as Regressor); establishes supervised baseline for fine-grained localization
8. [[papers/2025-re-thinking-temporal-search-long-form-video|T* (2025)]] — benchmark (LV-HAYSTACK) establishing the temporal search gap (2.1% F1 SOTA); reformulates temporal search as spatial search using image grids and iterative zooming; demonstrates that temporal dimension—not video processing—is the bottleneck
9. [[papers/2025-agentic-keyframe-search|AKEYS (2025)]] — agent-guided classical search (A*/Dijkstra's) on hierarchical video trees; demonstrates that agent-directed heuristics can match RL-based approaches for temporal search without training overhead
10. [[papers/2025-timesearch-r-temporal-search-video-understanding|TimeSearch-R (2025)]] — contrasts with AKEYS: RL-trained adaptive search for longer contexts (Video Embedder×RL-tuned LLM); tackles long-form setting with dynamic frame retrieval
11. [[papers/2025-urbanvideo-bench|UrbanVideo-Bench (2025)]] — benchmark for embodied cognition in urban 3D spaces using aerial drone footage; reveals current Video-LLM limitations in spatial reasoning and counterfactual understanding; situates video understanding within embodied AI
12. [[papers/2025-internvideo2.5|InternVideo2.5 (2025)]] — orthogonal scaling via hierarchical token compression + task preference optimization; enables 6x longer memory while preserving fine-grained understanding; demonstrates annotation transfer as practical efficiency axis alongside frame selection and RL-based search

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
