# Concepts Reference Guide

This is the single source of truth for adding new concepts. Do NOT read existing concept files for reference — use this file only.

## Directory

All concept files live in `_concepts/`.

## Filename

```
_concepts/<slug>.md
```

Use a short, lowercase, hyphenated slug. Examples: `dropout.md`, `batch-normalization.md`, `attention-mechanism.md`.

## Frontmatter (required fields)

```yaml
---
layout: concept
title: "Concept Name"
date: YYYY-MM-DD
description: "One-line summary of the concept."
tags: [tag1, tag2, tag3]
---
```

## Frontmatter (optional fields)

```yaml
paper: "Paper Title (Authors, Year)"
```

`paper` renders as an italicized reference below the title on the concept page.

## Content structure

Use markdown with `##` headings for sections. Typical sections (adapt as needed):

```markdown
## Core Idea
Brief explanation of the concept.

## Motivation
Why this concept exists / what problem it solves.

## Formalism
Math formulas, equations, definitions. Use `$...$` for inline and `$$...$$` for display math.

## Pros & Cons
| Pros | Cons |
| :--- | :--- |
| ... | ... |

## Implementation Snippet (PyTorch)
\```python
# code here
\```
```

Not every concept needs all sections. Use what fits.

## Features available in content

- **Math:** `$inline$` and `$$display$$` (MathJax enabled site-wide)
- **Code blocks:** Fenced with language identifier (```python, ```bash, etc.)
- **Tables:** Standard markdown tables
- **Bold/Italic:** `**bold**`, `*italic*`
- **Lists:** Bulleted (`*`) and numbered (`1.`)

## Tags convention

Use lowercase, hyphenated tags. Reuse existing tags when possible. Common tags:
- `deep-learning`, `regularization`, `neural-networks`, `optimization`
- `cnn`, `rnn`, `transformer`, `attention`
- `math`, `linear-algebra`, `probability`

## How the listing page works

The concepts page (`/concepts/`) has three views:
1. **Timeline** — concepts grouped by date (DD-MM-YYYY), newest first
2. **Cards** — grid layout
3. **List** — compact table

All views support tag filtering. Multiple concepts on the same `date` are grouped together in the timeline view.

## Quick copy-paste template

```yaml
---
layout: concept
title: ""
date:
description: ""
tags: []
---

## Core Idea


## Key Details


```
