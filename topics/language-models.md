---
topic: Language Models
slug: language-models
---

# Language Models

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "language-models")
SORT year DESC
```

## Overview

This topic covers the architectures, training methods, and scaling principles behind large language models. T5 (Raffel et al., JMLR 2020) established the text-to-text framework — casting all NLP tasks into a unified input-output format — and provided the most comprehensive empirical study of transfer learning factors to date. Its systematic comparison of architectures (encoder-decoder vs. decoder-only vs. prefix LM), pre-training objectives (language modeling vs. denoising vs. span corruption), data quality (C4), and scaling strategies demonstrated that the encoder-decoder Transformer with span-corruption denoising achieves the best performance at equivalent compute, and that both scale and methodological choices matter. T5's text-to-text abstraction directly influenced the "prompt everything" paradigm and subsequent models (mT5, FLAN, UL2, PaLM).

## Trends

- **Text-to-text as universal interface**: T5 demonstrated that a single model, loss function, and decoding procedure can handle classification, regression, translation, summarization, and question answering by converting all tasks to text generation. This unified framing eliminated task-specific heads and output layers, paving the way for instruction-tuned and few-shot prompting paradigms.
- **Denoising over language modeling for pre-training**: T5's systematic comparison showed that denoising objectives (predicting corrupted spans) consistently outperform autoregressive language modeling for downstream task performance, particularly when the model is used in an encoder-decoder setup. Span corruption (contiguous spans, mean length 3) is slightly better and more computationally efficient than i.i.d. token corruption.
- **Data quality as a first-class concern**: C4's simple heuristic cleaning of Common Crawl (punctuation filtering, minimum sentence count, blocklist filtering) established the template for subsequent large-scale pre-training corpora. T5 showed that dataset repetition during pre-training is harmful and that in-domain data can boost specific tasks but constrains diversity.
- **Scaling laws in practice**: T5 demonstrated that larger models trained for fewer steps often outperform smaller models trained longer, and that ensembling provides orthogonal gains. The 11B model achieved SOTA on 18/24 tasks, but the systematic study showed non-scaling factors (objective, data, multi-task pre-training) provide meaningful additional gains.

## Open questions

- Whether encoder-decoder architectures (T5's finding) or decoder-only architectures (the path taken by GPT-3, PaLM, LLaMA) are ultimately more efficient for general-purpose NLP at extreme scale
- How to make pre-training more knowledge-efficient — T5 required 1 trillion tokens to reach peak performance, suggesting denoising may be a wasteful way to encode world knowledge
- Whether principled methods for multi-task mixing proportions can be found, or if the heuristic approaches used by T5 and its successors are near-optimal
- How the text-to-text framework extends to multimodal settings where inputs and outputs span modalities beyond text
