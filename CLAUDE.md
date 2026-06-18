A personal literature review wiki for AI research and related fields. The LLM maintains the wiki; the human curates sources.

---

## Directory structure

```
papers/
  {year}/
    {YYYY-paper-slug}/
      {YYYY-paper-slug}.pdf            # Raw paper PDF (immutable after filing)
      {YYYY-paper-slug}.md             # Paper summary
      {YYYY-paper-slug}-thumbnail.png  # Thumbnail image (or .jpg)
topics/
  _index.md                # Master index of all topics
  {topic-slug}.md          # One file per topic (e.g. change-detection.md)
inbox.md                   # Numbered list of wget-able links to ingest
CLAUDE.md                  # This file
```

---

## Naming conventions

- **Paper slug** (`{YYYY-paper-slug}`): Derive from the actual paper title, not the temporary inbox name. Lowercase kebab-case. Drop articles (a, an, the) and filler words. Keep it short but recognizable.
  - `2017-attention-is-all-you-need`
  - `2015-imagenet-large-scale-visual-recognition`
  - `2024-viewdelta`
- **Topic slug**: Lowercase kebab-case. Examples: `diffusion-models`, `mechanistic-interpretability`, `in-context-learning`, `rlhf`, `world-models`.

When ingesting a paper, assign all topics that the paper belongs to.

---

## File formats

### Paper summary — `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}.md`

```markdown
---
title: "Full Paper Title"
authors: [First Author, Second Author, ...]
year: 2024
venue: "NeurIPS 2024"           # Only include if specified in ingest command (e.g. "venue: CVPR 2026"). Omit otherwise.
tags: [topic-slug-1, topic-slug-2]
url: "https://arxiv.org/abs/..."
date_ingested: 2026-06-18
---

# Full Paper Title

![[{YYYY-paper-slug}-thumbnail.png]]

## Research gap
Why this work exists — what problem or limitation in prior work does it address?

## Contributions
Bulleted list of the paper's key contributions.

## Method
How the approach works. Include architecture details, training procedures, or algorithms as relevant.

## Datasets & evaluation
What benchmarks or datasets were used? Summarize key quantitative results.

## Limitations
Acknowledged or apparent weaknesses, failure modes, or scope constraints.

## Key takeaways
New knowledge, surprising findings, or implications for future work.
```

### Topic index — `topics/{topic-slug}.md`

```markdown
---
topic: Topic Display Name
slug: topic-slug
---

# Topic Display Name

## Papers

| Year | Thumbnail | Paper | Venue |
|------|-----------|-------|-------|
| 2024 | ![[2024-paper-slug-thumbnail.png\|400]] | [[2024-paper-slug\|Short Title]] | NeurIPS 2024 |
| 2017 | ![[2017-paper-slug-thumbnail.png\|400]] | [[2017-paper-slug\|Short Title]] | |

## Overview
One or two paragraphs describing what this topic covers and why it matters.

## Trends
Bulleted observations about how the field is evolving, based on the ingested papers.

## Open questions
Unsolved problems, active debates, or promising directions that appear across the literature.
```

Papers are listed in reverse chronological order (newest first). Update the overview, trends, and open questions sections each time a new paper is added.

### Master index — `topics/_index.md`

```markdown
# Literature Review Index

| Topic | Papers | Last updated |
|-------|--------|--------------|
| [[change-detection\|Change Detection]] | 3 | 2026-06-18 |
| [[world-models\|World Models]] | 1 | 2026-06-15 |
```

Topics are sorted alphabetically. Update the paper count and last-updated date whenever a topic gains a new paper.

### Inbox — `inbox.md`

A numbered list of links to download. Each entry may optionally include a temporary nickname after the URL.

```markdown
1. https://arxiv.org/pdf/2412.07612 viewdelta
2. https://arxiv.org/pdf/2605.30341
3. https://arxiv.org/abs/1409.0575 imagenet
```

---

## Ingest workflow

Triggered when the user says "ingest {number}" or similar (e.g. `ingest 3`, `ingest imagenet`).

1. **Read `inbox.md`**. Find the entry by number or by temporary name.
2. **Download** the link with `wget`. For `.pdf` files, extract text with `pdftotext file.pdf -`.
3. **Duplicate check** — after reading the paper, check if it already exists in `papers/` (match by title or slug). If it does, warn the user that the paper has already been ingested, delete any downloaded files, and stop.
4. **Thumbnail** — if an image is provided in the command (e.g. `ingest 1 [Image #1]`), use it as the thumbnail.
5. **Topic tags** — always auto-generate topic tags from the paper content. If the user also provides tags in the command (e.g. `ingest imagenet image-classification computer-vision`), merge them with the auto-generated ones. Prefer reusing existing topic slugs (check `topics/`) before creating new ones.
6. **File the PDF** — move to `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}.pdf`.
7. **Save thumbnail** — save as `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}-thumbnail.png` (or `.jpg`).
8. **Create paper summary** — write `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}.md` following the paper summary format above.
9. **Update topic indexes** — for each topic tag, update (or create) `topics/{topic-slug}.md`. Add the paper to the table and revise the overview, trends, and open questions.
10. **Update master index** — update `topics/_index.md` with current paper counts and dates.

A single ingest typically touches 5–15 files. Do all updates in one pass.

---
