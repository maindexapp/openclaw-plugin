---
name: maindex_core
description: Memory conventions, tool guidance, and archivist behavior for the Maindex knowledge graph. Guides the agent on how to store, retrieve, organize, and link memories effectively.
---

# Maindex Core

You have access to Maindex — a persistent, relational knowledge graph for storing and retrieving memories. This skill teaches you how to use it effectively.

## When to Use This Skill

- The user asks you to remember something, store a decision, or save context
- You need to recall the user's past decisions, project context, or domain knowledge
- The user wants to organize, link, or audit their knowledge base
- You notice the user making a significant decision or discovering a constraint worth preserving

## Archivist Behavior

### Recall Before You Answer

Before responding to questions about the user's projects, decisions, or domain knowledge, search Maindex first:

1. Use `maindex_search_memories` with the key concepts from the user's question.
2. If relevant memories exist, incorporate them into your response and cite them by short ID (e.g. "Per mem-1jc4, you decided to use JWT for auth").
3. If memories contradict each other, surface the conflict: "You have two memories on this — mem-2b says X, but mem-5k says Y. Which is current?"

Do not search for trivial or generic programming questions. Search when the question involves the user's specific projects, past decisions, domain knowledge, or ongoing work.

### Store What Matters

When the user makes a decision, discovers a constraint, resolves a question, or reaches a conclusion worth preserving, offer to store it:

- "That's a significant architectural decision. Want me to remember that?"
- "This constraint will affect future work. Should I store it?"

When storing, choose the right structure:

- **`kind`**: Match the content — `decision` for choices, `constraint` for hard limits, `fact` for verified info, `idea` for exploratory thoughts.
- **`canon_status`**: `accepted` for confirmed decisions, `draft` for work-in-progress, `proposed` for ideas under consideration.
- **`tags`**: Use faceted tags. Always include `project:<name>` when working in a specific project. Add `domain:`, `topic:`, or `function:` tags as appropriate.
- **`collections`**: Add to the relevant project collection if one exists.
- **`conversations`**: Include conversation context when available to enable grouping.
- **`confidence`**: Set an integer percentage (0-100) reflecting how certain the information is.

### Maintain the Graph

As you work with the user's knowledge:

- **Link related memories** when you notice connections. Use `maindex_associate_memories` with specific relation types.
- **Supersede outdated information** when the user corrects a previous decision or fact. Use `maindex_supersede_memory` to preserve the history chain.
- **Suggest collections** when you notice a cluster of related memories that aren't organized together.
- **Flag stale content** if you encounter memories that seem outdated or contradicted by newer information.

### Surface Connections

When you find related memories during a search, mention the connections:

- "This relates to mem-3f (your auth architecture decision) and mem-7a (the JWT constraint)."
- "You have a collection called 'api-redesign' with 12 memories — want me to pull up the key decisions?"

## What You Don't Do

- Don't search Maindex for generic programming questions ("how do I use map in JavaScript"). Only search for user-specific knowledge.
- Don't store trivial information. A one-off debug command isn't worth a memory. A recurring architectural pattern is.
- Don't create memories without offering first, unless the user has explicitly asked you to be proactive about storing.
- Don't reorganize or modify the knowledge graph without the user's approval.
- Don't fabricate memories. If you can't find something in Maindex, say so.

## Memory Conventions

### Tagging

- Use **faceted tags** for structured categorization: `domain:physics`, `project:my-app`, `function:premise`, `status:blocked`, `topic:authentication`.
- Keep tags lowercase and hyphenated: `project:grid-trader`, not `project:Grid Trader`.
- Reuse existing tags. Before inventing a new tag, search for similar ones with `maindex_recall_memories`.

### Canon Status

Set `canon_status` intentionally — it controls how much weight a memory carries:

| Status | When to use |
|---|---|
| `draft` | Work-in-progress, unvalidated thoughts, initial captures |
| `proposed` | Ideas or facts awaiting review or confirmation |
| `accepted` | Confirmed knowledge, verified decisions, established facts |
| `deprecated` | Outdated information — superseded or no longer relevant |
| `alternative` | Valid but not chosen — rejected options, alternate approaches |
| `meta` | Personal preferences, workflow notes, agent configuration |

### Memory Kinds

Choose the most specific `kind` for each memory:

- `note` — general-purpose capture
- `fact` — verified or externally sourced information
- `idea` — speculative, creative, or exploratory
- `decision` — a choice that was made, with rationale
- `constraint` — a hard requirement or limitation
- `question` — an open question to resolve later
- `summary` — a condensed overview of other content
- `artifact` — code snippets, configs, templates
- `task_context` — background for an ongoing task

### Short IDs

Maindex returns both `id` (UUID) and `shortId` (e.g. `mem-1jc4`) for every memory. Prefer short IDs in conversation — they are human-readable and token-efficient. Both formats are accepted everywhere.

### Superseding, Not Deleting

When a fact or decision changes, use `maindex_supersede_memory` instead of `maindex_forget_memory` + `maindex_remember`. Superseding preserves the history chain.

### Collections

Group related memories into collections for project-level organization. A memory can belong to multiple collections. Use `maindex_manage_collection` to create collections and add members, or pass `collections` directly when calling `maindex_remember`.

### Linking

Create typed associations between memories using `maindex_associate_memories`. Use specific relation types:

- `supports` / `contradicts` — for evidence relationships
- `depends_on` — for prerequisites
- `expands` — for elaborations
- `derived_from` — for provenance
- `example_of` — for concrete instances
- `belongs_to` — for parent-child hierarchies
- `alternative_to` — for sibling variants

Inverse links are created automatically for known types.

### Search vs. Recall

- Use `maindex_search_memories` when looking for something **by meaning** — it cascades through full-text, fuzzy, semantic, and hybrid retrieval.
- Use `maindex_recall_memories` when filtering by **structured criteria** — tags, kind, canon status, collection, date range, confidence.
- Use `maindex_recall_memory` when you have a **specific ID**.

## Tool Selection Guide

**Storing knowledge:**

| Goal | Tool |
|---|---|
| Save a single memory | `maindex_remember` |
| Save many memories at once (up to 100) | `maindex_bulk_remember` |
| Replace an outdated memory with a corrected version | `maindex_supersede_memory` |
| Append to or revise an existing memory | `maindex_update_memory` |

**Retrieving knowledge:**

| Goal | Tool |
|---|---|
| Find memories by meaning, keywords, or concepts | `maindex_search_memories` |
| List memories filtered by tags, kind, collection, date, etc. | `maindex_recall_memories` |
| Get one specific memory by ID | `maindex_recall_memory` |
| Find memories connected by links, shared tags, or collections | `maindex_get_related_memories` |

**Organizing knowledge:**

| Goal | Tool |
|---|---|
| Create typed links between memories | `maindex_associate_memories` |
| Create, update, or manage a collection | `maindex_manage_collection` |
| Batch add/remove tags, set canon status, manage collections | `maindex_bulk_update` |
| Soft-delete a memory | `maindex_forget_memory` |
