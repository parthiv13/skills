# Slice index for {STORIES}

> Copy this structure for `to-spec/{STORIES}/index.md`. Replace every `{placeholder}`. Delete every line that starts with `>` in the index.

## 1. Dependency diagram

```mermaid
flowchart LR
    {S01}[{01: responsibility}] --> {S02}[{02: responsibility}]
```

> Nodes are slice IDs with the slice's responsibility. Arrows are required merge order. Use separate branches for independent slices.

## 2. Merge order

| Order | Slice | Story | Responsibility | Why here |
| --- | --- | --- | --- | --- |
| {1} | [{01}]({artifact link}) | {STORY-ID} | {one sentence} | {the dependency that forces this position} |

## 3. Parallel candidates

> List slices that could be developed together even though they merge in order, or write "none".

{parallel candidates}

## 4. Status

| Slice | Status | PR | Notes |
| --- | --- | --- | --- |
| {01} | planned | | |

> Status values: planned, in progress, in review, merged. The implementation skill updates this table. The planning skill sets every slice to planned.

## 5. Pending revisions

> List unmerged slices whose artifact needs updating after a plan change, with the reason. Write "none" when empty. Never list a merged slice.

{pending revisions, or none}
