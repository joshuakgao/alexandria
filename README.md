
I have created a custom CLAUDE.md that may be useful to you as it supports thumbnail photos for each paper, and identifies trends over time for a research topic.

## Usage

```
cd ~/Documents/Obsidian/alex
claude --dangerously-skip-permissions
```
### Add a paper to inbox.

```
1. https://arxiv.org/pdf/2412.07612 viewdelta
2. https://arxiv.org/pdf/2605.30341
3. https://arxiv.org/abs/1409.0575 imagenet
4. ...
```


**Then in Claude Code:**

```claude
ingest 1 [Image 1] change-detection
# or
ingest 2           # Claude should prompt you for thumbnail image and topics
# or
ingest imagenet [Image 2] cv image-classification

# Ask Claude to traverse your Wiki's and answer questions
What are some research gaps in the world-models literature?
```
