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
