---
topic: Image Captioning
slug: image-captioning
---

# Image Captioning

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "image-captioning")
SORT year DESC
```

## Overview

Image Captioning encompasses 4 papers in this literature review.

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
