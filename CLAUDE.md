A personal literature review wiki for AI research and related fields. The LLM maintains the wiki; the human curates sources.

---

## Directory structure

```
papers/
  {year}/
    {YYYY-paper-slug}/
      {YYYY-paper-slug}.md             # Paper summary
      {YYYY-paper-slug}-thumbnail.png  # Thumbnail image (or .jpg)
topics/
  _index.md                # Master index of all topics
  {topic-slug}.md          # One file per topic (e.g. change-detection.md)
inbox.md                   # Numbered list of sources to ingest (URLs and/or local PDFs)
log.md                     # Chronological log of ingested papers
CLAUDE.md                  # This file
```

Locally downloaded PDFs listed in `inbox.md` are **not** stored in the wiki. Unless the entry gives an explicit path, resolve a bare filename in this order:

1. `~/Downloads/{filename}`
2. `~/Desktop/{filename}`
3. the wiki root

If the file cannot be found in any of these, stop and ask the user for the path rather than guessing.

---

## Naming conventions

- **Paper slug** (`{YYYY-paper-slug}`): Derive from the actual paper title, not the temporary inbox name and not the PDF filename. Lowercase kebab-case. Drop articles (a, an, the) and filler words. Keep it short but recognizable.
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
url: "https://arxiv.org/abs/..."   # Download URL, or the DOI / publisher page for a local PDF. Omit if unknown.
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

A numbered list of sources to ingest. Each entry is either a **URL** to download or a **local PDF** already on disk, and may optionally include a temporary nickname after the source.

```markdown
1. https://arxiv.org/pdf/2412.07612 viewdelta
2. https://arxiv.org/pdf/2605.30341
3. https://arxiv.org/abs/1409.0575 imagenet
4. 1-s2.0-S1093968726030938-main.pdf
5. ~/Downloads/attention-is-all-you-need.pdf transformer
```

An entry is treated as a URL if it starts with `http://` or `https://`; otherwise it is treated as a local PDF path (see [Directory structure](https://claude.ai/chat/eb4d3faa-342b-43eb-9763-f8dfa93abc40#directory-structure) for how bare filenames are resolved). Both kinds may be mixed freely in the same list, and either kind can be referenced by number or by nickname when ingesting.

### Ingest log — `log.md`

An append-only, reverse-chronological record of every ingested paper. Newest entries go at the top of the table, directly under the header row. Never rewrite or delete existing rows; if a paper is later removed from the wiki, add a new row noting the removal rather than editing history.

```markdown
# Ingest Log

| Date | Paper | Year | Venue | Topics | Source |
|------|-------|------|-------|--------|--------|
| 2026-06-18 | [[2024-viewdelta]] | 2024 | WACV 2025 | change-detection, vision-language-models | https://arxiv.org/pdf/2412.07612 |
| 2026-06-17 | [[2026-example-paper]] | 2026 | Expert Systems with Applications | multi-agent-systems | 1-s2.0-S1093968726030938-main.pdf (local) |
```

- **Date** — the ingest date (same value as `date_ingested` in the paper frontmatter).
- **Paper** — wikilink to the paper slug.
- **Year** — publication year.
- **Venue** — the resolved venue, or blank if none was found.
- **Topics** — comma-separated topic slugs assigned to the paper.
- **Source** — the URL the paper was downloaded from. For a local PDF, record the filename as it appeared in `inbox.md` followed by `(local)`; if a DOI or publisher URL was resolved during ingest, record that instead, followed by `(local)`.

If `log.md` does not exist, create it with the header shown above before appending the first row.

---

## Ingest workflow

Triggered when the user says "ingest {number}" or similar (e.g. `ingest 3`, `ingest imagenet`, or `ingest 3 CVPR 2009`).

1. **Read `inbox.md`**. Find the entry by number or by temporary name.
2. **Obtain the PDF text**, depending on the entry type:
    - **URL entry** — download with `wget`. For `.pdf` files, extract text with `pdftotext file.pdf -`.
    - **Local PDF entry** — do not download anything. Resolve the path as described above, verify the file exists, and extract text with `pdftotext "{path}" -`. Never move, rename, modify, or delete the user's local PDF; read it in place.
3. **Duplicate check** — after reading the paper, check if it already exists in `papers/` (match by title or slug). If it does, warn the user that the paper has already been ingested, delete any files that were downloaded during this ingest (never the user's local PDF), and stop.
4. **Thumbnail** — if an image is provided in the command (e.g. `ingest 1 [Image #1]`), use it as the thumbnail. Assume the thumbnail image exists at "~/Desktop/Screenshot"
5. **Topic tags** — always auto-generate topic tags from the paper content. If the user also provides tags in the command (e.g. `ingest imagenet image-classification computer-vision`), merge them with the auto-generated ones. Prefer reusing existing topic slugs (check `topics/`) before creating new ones. Topic tags must only be research areas and not just concepts. **Present the proposed topic tags to the user and wait for confirmation before continuing.**
6. **Publishing venue** — determine the conference or journal where the paper was published. Check these sources in priority order: (1) the ingest command or inbox entry (e.g. `CVPR 2026`), (2) the paper text itself (headers, footnotes, or copyright notices), (3) a web search. Use the format `"Conference YYYY"` or `"Journal Name"` (e.g. `"NeurIPS 2024"`, `"ICLR 2026"`). arXiv is a preprint server, not a venue — do not use it. If no venue is found from any source, leave the `venue` field blank in the frontmatter.
    - Local PDFs are often publisher-typeset copies, so the venue, DOI, and volume/issue are usually printed on the first page or in the running header — check there first. The filename may also hint at the source (e.g. a `1-s2.0-...` name indicates ScienceDirect), but confirm it against the paper text before relying on it.
7. **Source URL** — for a URL entry, use the download link. For a local PDF, use the DOI or publisher page if one is printed in the paper or found by web search; otherwise omit the `url` field from the frontmatter.
8. **Save thumbnail** — save as `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}-thumbnail.png` (or `.jpg`).
9. **Create paper summary** — write `papers/{year}/{YYYY-paper-slug}/{YYYY-paper-slug}.md` following the paper summary format above.
10. **Update topic indexes** — for each topic tag, if `topics/{topic-slug}.md` does not exist, create it with the Dataview query template (see format above). The paper table populates automatically via Dataview; do **not** add static table rows. Revise the overview, trends, and open questions sections.
11. **Master index** — `topics/_index.md` auto-populates via Dataview. No manual update needed. If a new topic file was created in step 10, it will appear automatically.
12. **Append to log** — add a row to the top of the table in `log.md` following the ingest log format above. Create the file with its header if it does not yet exist.
13. **Remove** the ingested document from the inbox list, and fix the numbered list order.
14. **Clean up** — delete the thumbnail at "~/Desktop/Screenshot" and any PDF or text files downloaded during this ingest. Remove the local pdf. 

A single ingest typically touches 5–15 files. Do all updates in one pass.

---