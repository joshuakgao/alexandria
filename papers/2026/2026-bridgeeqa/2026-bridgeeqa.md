---
title: "BridgeEQA: Virtual Embodied Agents for Real Bridge Inspections"
authors: [Subin Varghese, Joshua Gao, Asad Ur Rahman, Vedhus Hoskere]
year: 2026
venue: ""
tags: [embodied-ai, construction-monitoring, scene-graphs]
url: ""
pdf: "[[2026-bridgeeqa.pdf]]"
date_ingested: 2026-06-18
---

# BridgeEQA: Virtual Embodied Agents for Real Bridge Inspections

![[2026-bridgeeqa-thumbnail.png]]

## Research gap

BridgeEQA introduces infrastructure inspection as a compelling problem class for episodic memory Embodied Question Answering (EM-EQA), arguing that bridge inspection demands capabilities that household EQA benchmarks under-represent: multi-scale reasoning across dozens of egocentric images, long-range spatial understanding of hierarchical structures, alignment with standardized condition rubrics (NBI 0-9 scale), and citation of supporting photographic evidence. The benchmark comprises 2,200 open-vocabulary QA pairs grounded in professional inspection reports across 200 real-world bridges in Vermont, with 9,586 images (avg. 47.93 per scene).

## Contributions

- BridgeEQA benchmark: 2,200 QA pairs over 9,586 images from 200 bridges across 73 towns; expert-grounded in professional inspection reports with NBI condition ratings
- Inspection EQA problem class: formalized checklist for multi-view, rubric-grounded, evidence-cited QA applicable beyond bridges to dams, tunnels, pipelines
- EMVR: MDP-based scene graph traversal that converts EM-EQA to A-EQA, mitigating "lost in the middle" positional bias in long-context VLMs
- Image Citation Relevance metric: VLM-as-a-judge evaluation of cited visual evidence against reference images (0.817 human correlation)

## Method

**Scene graph construction:** VLM (Gemini 2.5 Flash/Pro) processes bridge inspection images and outputs structured JSON with nodes (image name, central focus, image description) and edges (spatial/semantic/hierarchical relationships). Edges encode hierarchical (overview↔detail), structural (supports), spatial adjacency, condition similarity, and part-whole relationships. Purely visual — no GPS or geolocation required.

**EMVR MDP formulation:**
- State: (current node, interaction history)
- Observation: complete scene graph structure (labels, descriptions, edges) + current node
- Actions via function calls: MOVE(node), COMPARE({nodes}), REASON(node), RESPOND(query)
- Policy: VLM selects actions based on state and inspection query
- Terminates on RESPOND action

**Dataset construction:** Extract from unstructured PDF inspection reports → page/image filtering → Gemini-based parsing of text, images, condition ratings → automated + human validation → QA generation. Quality: Faithfulness 0.997, Answer Relevancy 0.997, Answerability 0.996.

## Datasets & evaluation

**Condition rating accuracy (±1):** EMVR w/ Images+SG achieves best performance with Grok 4 Fast. Expert inspectors agree within ±1 at 98% rate. **Answer Correctness:** 0.648 (Grok 4 Fast + EMVR) vs. 0.576 (Multi-Frame VLM). **Image Citation Relevance:** 0.889 (Grok 4 Fast + EMVR) vs. 0.687 (Multi-Frame VLM). Scene graph context consistently improves all methods. Socratic LLM w/ SG provides competitive alternative to EMVR on some VLMs. Error analysis reveals low citation quality correlates with hallucinations and poor answer quality.

## Limitations

- Only tested with proprietary VLMs (Gemini, Grok); open-source sub-30B models couldn't reliably follow structured output and function-calling formats
- Scene graph quality depends on VLM construction; errors in graph structure propagate to navigation quality
- Currently limited to bridge inspection; generalization to other infrastructure types (dams, tunnels, pipelines) not empirically validated
- No temporal dimension — inspection reports from different time periods not compared; change detection over bridge lifecycle is a natural extension
- Real-world deployment on inspection robots not demonstrated; current evaluation uses pre-collected images only
- Performance gap to expert inspectors remains substantial, especially on exact condition rating match

## Key takeaways

The key methodological contribution is Embodied Memory Visual Reasoning (EMVR), which reformulates EM-EQA as an Active EQA problem via MDP-based scene graph traversal. Rather than feeding all images simultaneously to a VLM (where mid-sequence information is "lost in the middle"), EMVR uses an image-based scene graph as an allocentric map and navigates it via function calls (MOVE, COMPARE, REASON, RESPOND) to dynamically retrieve only relevant visual evidence. This repositions critical information at the end of the context window, mitigating positional bias.

EMVR with Grok 4 Fast achieves +9.34pp condition rating accuracy (±1), +20.2pp Image Citation Relevance, and +7.2pp Answer Correctness over the Multi-Frame VLM baseline. A new metric, Image Citation Relevance, evaluates semantic similarity between agent-cited and reference images (0.817 Spearman correlation with human annotations).

BridgeEQA extends OpenEQA's EM-EQA paradigm to a domain with far greater scale (48 images/scene vs. OpenEQA's smaller scenes), specialized vocabulary, and rubric-grounded evaluation. The EMVR approach shares the scene-graph navigation philosophy with Mind Palace (episodic world instances) and MoMa-LLM (interactive scene graph search), but operates over image-based nodes rather than object-centric nodes — motivated by the lack of foundation models for dense bridge component detection. The MDP formulation connecting EM-EQA to A-EQA is novel in the embodied memory literature. Image Citation Relevance adds an evaluation dimension absent from prior EQA benchmarks. The work also relates to Memory-Centric EQA's entropy-based retrieval but uses navigational traversal rather than vector similarity for context selection.
