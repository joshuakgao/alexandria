---
topic: Diffusion Models
slug: diffusion-models
---

# Diffusion Models

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "diffusion-models")
SORT year DESC
```

## Overview

Diffusion Models encompasses 11 papers in this literature review.

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
