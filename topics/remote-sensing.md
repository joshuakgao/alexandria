---
topic: Remote Sensing
slug: remote-sensing
---

# Remote Sensing

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "remote-sensing")
SORT year DESC
```

## Overview

Remote Sensing encompasses 6 papers in this literature review.

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
