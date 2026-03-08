# AI Summary Generation Prompt

This prompt is used to generate/update the AI summary of the site author based on their blog posts, concepts, and knowledge base.

## Instructions

When asked to update the summary:

1. Read all blog posts in `_posts/` (frontmatter + content overview)
2. Read all concepts in `_concepts/` (frontmatter + content)
3. Read `KNOWLEDGE.md` for the knowledge index
4. Read the existing summary at `AI_SUMMARY.md` (if it exists)
5. Generate an updated `AI_SUMMARY.md` that covers:
   - **Focus Areas**: What technical domains the author is actively working in (derived from recent posts and concepts)
   - **Depth Indicators**: Which topics have deep coverage vs. surface-level exploration
   - **Learning Trajectory**: What direction the author's learning is heading based on chronological content
   - **Technical Style**: How the author approaches technical writing (math-heavy, practical, etc.)
   - **Recent Activity**: What was added or changed since the last summary update

## Rules
- Only include information derivable from the site content (posts, concepts, knowledge index)
- No personal details — focus purely on technical areas and interests
- Keep it concise — this is meant to be consumed by AI agents or collaborators
- Include a "Last updated" date at the top
- When updating, preserve any manually added notes in AI_SUMMARY.md (marked with `<!-- manual -->`)
