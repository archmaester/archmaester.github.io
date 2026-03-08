# Blog Post Reference Guide

Quick reference for creating new blog posts in this Jekyll (al-folio theme) site.

## File Location & Naming

```
_posts/YYYY-MM-DD-slug-title.md
```

- **Directory:** `_posts/`
- **Date format:** `YYYY-MM-DD` (e.g., `2026-03-08`)
- **Slug:** lowercase, words separated by hyphens (e.g., `floating-point-precision-bf16-deep-learning`)
- **Extension:** `.md`

Example: `_posts/2026-03-08-floating-point-precision-bf16-deep-learning.md`

## Front Matter (YAML)

Every post must start with this front matter block:

```yaml
---
layout: post
title: "Your Post Title Here"
date: YYYY-MM-DD
description: A one-sentence summary of the post (shown below title and used for SEO/previews).
tags: [tag1, tag2, tag3]
---
```

### Required Fields

| Field         | Description                                       | Example                                              |
|---------------|---------------------------------------------------|------------------------------------------------------|
| `layout`      | Always `post`                                     | `post`                                               |
| `title`       | Post title in double quotes                       | `"Understanding Gradients in a Linear Layer"`        |
| `date`        | Publication date (matches filename date)          | `2026-03-08`                                         |
| `description` | One-sentence summary (no quotes needed)           | `A step-by-step derivation of the weight gradient.`  |
| `tags`        | Array of lowercase, hyphenated tags               | `[deep-learning, backpropagation, neural-networks]`  |

### Optional Fields (supported by layout)

| Field    | Description                  | Example         |
|----------|------------------------------|-----------------|
| `author` | Author name (shown in meta)  | `Monish Keswani`|

## Content Formatting

### Structure Pattern

Posts follow a numbered section pattern:

```markdown
Intro paragraph(s) explaining the topic.

---

## 1. First Section Title

Content...

---

## 2. Second Section Title

Content...

---

## Key Takeaway

Final summary paragraph.
```

- Use `## ` (h2) for main sections — these auto-generate the Table of Contents (requires 3+ h2 headings)
- Separate sections with `---` (horizontal rules)
- Number sections sequentially: `## 1. Topic`, `## 2. Topic`, etc.
- End with a `## Key Takeaway` section

### Math (LaTeX)

MathJax is enabled globally (`enable_math: true` in `_config.yml`).

- **Inline math:** `$x = 2$`
- **Display math:** `$$x = (-1)^{\text{sign}} \times 2^{\text{exponent}}$$`

### Code Blocks

Use fenced code blocks with language identifiers:

````markdown
```python
y = W @ x
dL_dW = g[:, None] @ x[None, :]
```
````

Also used for pseudocode/diagrams without a language tag:

````markdown
```
Conv → BatchNorm → ReLU
```
````

### Tables

Standard markdown tables:

```markdown
| Format | Exponent | Mantissa |
|--------|----------|----------|
| FP32   | 8        | 23       |
| BF16   | 8        | 7        |
```

### Images

Use medium_zoom-enabled images (`enable_medium_zoom: true`):

```markdown
![Alt text](/assets/img/your-image.png)
```

Place images in `assets/img/`.

## Site Configuration Notes

- **Permalink pattern:** `/blog/:year/:title/` (from `_config.yml`)
- **Future posts:** Enabled (`future: true`) — posts with future dates will be visible
- **Math:** Globally enabled via MathJax 3.2.0
- **Dark mode:** Supported
- **Comments:** Giscus configured but currently disabled (`enable_giscus: false`)
- **Analytics:** GoatCounter enabled (site: `archmaester`)

## Checklist for New Post

1. Create file in `_posts/` with correct `YYYY-MM-DD-slug.md` naming
2. Add front matter with all required fields (`layout`, `title`, `date`, `description`, `tags`)
3. Write content using numbered `##` sections separated by `---`
4. End with a `## Key Takeaway` section
5. Use `$$...$$` for display math, `$...$` for inline math
6. Use fenced code blocks with language tags where applicable
