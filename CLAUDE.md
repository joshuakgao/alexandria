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
authors: [Firstname Lastname, Firstname Lastname, ...]   # Always use "Firstname Lastname" format for every author.
year: 2024
venue: "NeurIPS 2024"           # Only include if specified in ingest command (e.g. "venue: CVPR 2026"). Omit otherwise.
tags: [topic-slug-1, topic-slug-2]
url: "https://arxiv.org/abs/..."
pdf: "[[YYYY-paper-slug.pdf]]"
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

Paper tables use Dataview queries and auto-populate from paper frontmatter — do **not** write static table rows.

````markdown
---
topic: Topic Display Name
slug: topic-slug
---

# Topic Display Name

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "topic-slug")
SORT year DESC
```

## Overview
One or two paragraphs describing what this topic covers and why it matters.

## Trends
Bulleted observations about how the field is evolving, based on the ingested papers.

## Open questions
Unsolved problems, active debates, or promising directions that appear across the literature.
````

Update the overview, trends, and open questions sections each time a new paper is added. The paper table updates automatically via Dataview.

### Master index — `topics/_index.md`

The master index also uses Dataview and auto-populates — do **not** write static rows.

````markdown
# Literature Review Index

```dataviewjs
const topics = dv.pages('"topics"')
  .where(p => p.slug)
  .sort(p => p.topic, "asc");
const papers = dv.pages('"papers"');

dv.table(
  ["Topic", "Papers"],
  topics.map(t => {
    const count = papers.where(p => p.tags && p.tags.includes(t.slug)).length;
    return [t.file.link, count];
  })
);
```
````

Topics appear automatically when a topic file with a `slug` property exists.

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
5. **Topic tags** — always auto-generate topic tags from the paper content. If the user also provides tags in the command (e.g. `ingest imagenet image-classification computer-vision`), merge them with the auto-generated ones. Prefer reusing existing topic slugs (check `topics/`) before creating new ones. Topic tags must only be research areas and not just concepts. **Present the proposed topic tags to the user and wait for confirmation before continuing.**
6. **File the PDF** — move to `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}.pdf`.
7. **Save thumbnail** — save as `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}-thumbnail.png` (or `.jpg`).
8. **Create paper summary** — write `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}.md` following the paper summary format above.
9. **Update topic indexes** — for each topic tag, if `topics/{topic-slug}.md` does not exist, create it with the Dataview query template (see format above). The paper table populates automatically via Dataview; do **not** add static table rows. Revise the overview, trends, and open questions sections.
10. **Master index** — `topics/_index.md` auto-populates via Dataview. No manual update needed. If a new topic file was created in step 9, it will appear automatically.
11. **Remove** the ingested document from the inbox list.

A single ingest typically touches 5–15 files. Do all updates in one pass.

---
