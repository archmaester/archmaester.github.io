---
layout: post
title: "Optimizing Claude Token Usage: A Practical Guide to Context Management"
date: 2026-02-28
description: Learn how Claude uses context tokens and how to optimize them using compacting, task isolation, and tool management.
tags: [llm, claude, prompt-engineering, productivity]
---

## 1. Understanding Claude's Context Stack

When Claude responds, it is not just reading your latest message.

It processes an entire **context stack**.

Every response consumes tokens from:

<div class="formula-box">

Total Context Used =  
System Prompt  
+ System Tools  
+ Memory  
+ Skills  
+ Messages  
+ Free Space (Remaining)

</div>

Let’s break this down.

---

### System Prompt
Core instructions that define Claude’s behaviour.

---

### System Tools
Capabilities Claude can access automatically.

Examples:

- NotebookEdit  
- Plan Mode  
- Agent  
- WebSearch  
- Code Execution  

These are loaded into context even if unused.

---

### Memory
Persisted knowledge about your workflow or project.

---

### Skills
Reusable capabilities Claude dynamically loads.

---

### Messages
Your conversation history.

⚠️ This is the **only component that continuously grows**.

---

### Free Space
Remaining capacity before hitting Claude's context limit (typically ~200K tokens).

---

## 2. The Core Problem: Messages Grow Forever

Unlike system components, messages accumulate.

Every prompt-response pair adds:

<div class="formula-box">

Input Tokens + Output Tokens → Stored in History

</div>

Over time:

<div class="formula-box">

Messages → eat into Free Space

</div>

This leads to:

- Slower responses  
- Reduced reasoning quality  
- Higher cost  
- Truncation risks  

---

## 3. Understanding Turns, KV Cache, and Why Claude Re-Reads Context

To truly optimize Claude Code usage, you must understand one core concept:

> Claude is priced on what it reads — and it re-reads everything every turn.

---

### What is a *Turn*?

A **turn** is one full interaction cycle:

<div class="formula-box">

User prompt → Claude reads full context → Claude generates response

</div>

That entire loop = **1 turn**

---

### What Constitutes a Turn?

Each turn includes Claude reprocessing:

- System Prompt
- Tools
- Memory
- Conversation History
- Your New Input

Even if *you didn't repeat anything*, Claude still reprocesses all of it.

---

### Turn Examples

| Interaction | Turn Count |
|-------------|------------|
| You ask: "Fix this bug" | 1 |
| You follow up: "Optimize performance" | 2 |
| You say: "Add logging" | 3 |

Even though this feels like one conversation, Claude performs **3 full inference passes**.

---

### Mental Model

Think of Claude like this:

> Each turn = Claude re-reading the entire book before answering the next question.

It does not continue thinking from where it left off.

---

### Why KV Cache Doesn't Help Across Turns

Many assume:

> "KV cache should store past context — so why re-read?"

KV cache only works **within one response generation**.

During token-by-token generation:

<div class="formula-box">

Token → Token → Token

</div>

KV cache avoids recomputing attention. But once Claude finishes answering:

- That inference ends
- KV cache is discarded

Next turn = **fresh inference**

---

### Why Claude Re-Reads Every Turn

Claude is stateless between turns by design. This allows:

- Tool updates
- File edits
- Memory changes
- Plan revisions
- Safety guarantees
- Deterministic behavior

If KV cache were persisted, Claude could reason using stale state, tool outputs could become inconsistent, and conversation edits would break correctness.

Persistent KV would lock Claude into past assumptions, break agent reliability, and make tool-driven workflows unsafe.

**KV cache = scratchpad while writing an answer.** Each new question requires re-reading the notebook, not reusing scratchpad thoughts.

---

## 4. The Solution: `/compact`

Claude provides a built-in command:

<div class="formula-box">

/compact

</div>

---

### What `/compact` Does

It summarizes conversation history while preserving:

- Key decisions  
- Constraints  
- Instructions  
- Project goals  

It replaces:

<div class="formula-box">

Long message chain  
↓  
Compressed semantic memory

</div>

Example:

Before:

<div class="formula-box">

Messages = 120K tokens

</div>

After:

<div class="formula-box">

Messages = ~8K tokens

</div>

---

## 5. How `/compact` Affects Context

| Component      | Before | After     |
|---------------|--------|-----------|
| Messages      | Huge   | Minimal   |
| Memory        | Preserved | Preserved |
| System Prompt | Unchanged | Unchanged |
| Skills        | Unchanged | Unchanged |

Claude does **not forget your work**.

It retains:

- Intent  
- Architecture  
- Constraints  

But removes:

- Redundant iterations  
- Debugging loops  
- Dead discussions  

---

## 6. Automatic Compaction

Claude may automatically run compaction when it detects:

<div class="formula-box">

(Input + Output) > Remaining Context Window

</div>

Since the context window is typically:

<div class="formula-box">

~200K tokens

</div>

Automatic compaction prevents failure —  
but happens **late**.

Manual compaction = better performance.

---

## 7. Task Isolation: Use New Windows

Claude carries history forward across a session.

So if you switch from:

<div class="formula-box">

RAG Debugging  
→ Writing a Blog

</div>

Claude still loads:

- Code discussions  
- Architecture decisions  
- Tool usage  
- Previous iterations  

Even if irrelevant.

Result:

- Token waste  
- Slower reasoning  

---

### Best Practice

Use a **new Claude window for a new task**

This removes:

<div class="formula-box">

Unnecessary Message History

</div>

and maximizes:

<div class="formula-box">

Free Space

</div>

---

## 8. Tool Optimization via `/tools`

Claude loads many tools by default.

Check active tools using:

<div class="formula-box">

/tools

</div>

You may see:

- NotebookEdit  
- Plan Mode  
- Agent  
- WebSearch  

Each tool consumes tokens in the system layer.

---

### Example

If your project has no `.ipynb` files:

👉 `NotebookEdit` is wasted context.

If you’re not browsing live data:

👉 `WebSearch` adds unnecessary overhead.

If you're not running autonomous workflows:

👉 `Agent` may be irrelevant.

Each unused tool acts as:

<div class="formula-box">

Token Tax

</div>

Removing unused tools improves:

- Available context  
- Response efficiency  
- Cost efficiency  

---

## 9. Summarization vs `/compact`

Since Claude re-reads every turn, optimization means reducing what it has to read. The two most powerful tools are **summarization** and **`/compact`** — but they work differently.

**Summarization** is semantic compression. You manually convert a long conversation into a structured memory document:

<div class="formula-box">

100K history → 20K structured memory

</div>

This keeps decisions, constraints, and current state — and removes exploration, iterations, and dead paths.

**`/compact`** is structural pruning. Claude removes redundant reasoning, tool chatter, and repetition — but does not rewrite meaning.

| Feature | Summarization | `/compact` |
|---------|--------------|-----------|
| Type | Semantic | Structural |
| Rewrites knowledge? | Yes | No |
| Removes clutter? | Yes | Yes |
| Keeps conclusions? | Depends on prompt | Automatically |
| Requires prompt? | Yes | No |
| Control level | High | Low |

**Summarization** = Rewrite the story
**`/compact`** = Remove the noise

### Impact on Usage

| Strategy | Context Size | Total Usage Trend |
|----------|-------------|-------------------|
| No optimization | Large | Explodes |
| Only `/compact` | Medium | Moderate |
| Only summarization | Small | Efficient |
| Summarization + `/compact` | Smallest | Optimal |

### Practical Workflow

<div class="formula-box">

Explore → Decide → Summarize → /compact → Continue

</div>

This converts recurring cost into one-time cost.

---

## 10. Optimization Checklist

| Action | Benefit |
|--------|--------|
| Run `/compact` regularly | Shrinks message history |
| Summarize after exploration phases | Semantic compression of expensive history |
| Use new window per task | Eliminates irrelevant history |
| Check `/tools` | Remove unused tool overhead |
| Avoid multi-project threads | Prevent silent context growth |
| Compact before major prompts | Maximize reasoning capacity |
| Combine summary + compact | Smallest possible context footprint |

---

## Final Insight

Claude performance is rarely limited by intelligence.

It is limited by:

> Context hygiene.

Managing:

- Message growth
- Tool load
- Task isolation
- Summarization cadence

is the difference between:

⚡ Fast, high-quality responses
vs
🐌 Slow, degraded reasoning

Claude re-reads context every turn because it is stateless by design.

KV cache helps within responses — not across turns.

So optimization is not about faster thinking...

It's about giving Claude less to remember.

Optimize context → Optimize Claude.