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
tags: [tag1, tag2, tag3]
---
```

## Frontmatter (optional fields)

```yaml
paper:
  title: "Paper Title"
  authors: "Authors"
  year: 2024
  url: "https://..."
```

`paper` renders as a linked reference (Authors, Year) in the expanded detail view.

## Content structure

Write concise bullet points. No headings needed — the content expands inline on the concepts page (no separate page is generated).

```markdown
- **Motivation:** Why this concept exists.
- **Equation:** Key math. Use `$...$` for inline math.
- **Training:** How it works during training.
- **Inference:** How it works during inference.
- **Pros:** Benefits.
- **Cons:** Drawbacks.
- **Impl:** Code reference (e.g., `nn.Dropout(p)` in PyTorch).
```

Adapt the bullet points to fit the concept. Keep it brief — a few lines, not paragraphs.

## How the listing page works

The concepts page (`/concepts/`) has three views:
1. **Timeline** — concepts grouped by date (DD-MM-YYYY), newest first
2. **Cards** — grid layout (zoom-in on hover, no descriptions shown)
3. **List** — compact table

Clicking a concept in any view expands its detail inline (no separate page). All views support tag filtering.

## Features available in content

- **Math:** `$inline$` and `$$display$$` (MathJax enabled site-wide)
- **Code:** Inline with backticks
- **Bold/Italic:** `**bold**`, `*italic*`
- **Lists:** Bulleted (`-`)

## Tags convention

Use lowercase, hyphenated tags. Reuse existing tags when possible. Common tags:
- `deep-learning`, `regularization`, `neural-networks`, `optimization`
- `cnn`, `rnn`, `transformer`, `attention`
- `math`, `linear-algebra`, `probability`

## Quick copy-paste template

```yaml
---
layout: concept
title: ""
date:
tags: []
---

- **Motivation:**
- **Key idea:**
- **Impl:**
```
