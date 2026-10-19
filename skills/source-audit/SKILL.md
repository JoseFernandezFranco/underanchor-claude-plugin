---
name: source-audit
description: Audit a draft sentence by sentence against the user's Underanchor library and report which claims are supported, weakly supported, unsupported or broken. Use when the user asks "which sentences have no source?", "check my citations", "verify my draft", "does my library support this?", "audit this chapter", "revisa las fuentes" or wants to know what is left before submitting.
---

# Source audit with Underanchor

The goal is a clear, honest map of the draft: for each factual sentence, is there a paragraph
in the user's library that says it? Report first; change the document only if the user asks.

## What to audit

- **An Underanchor project**: call `list_projects`, then `read_project(project_name)` to get
  the text, its `anchors` (each with its `id`, `chunk_id` and byte offsets) and its comments.
  A project shared by someone else uses the address `<owner email>/<project>`.
- **Text pasted in the chat** (or a read-only connection, where `list_projects` and
  `check_unsourced` are not available): audit it with `search_corpus` alone and say that the
  result is not stored in any project.

## Steps

1. **Run the checker.** `check_unsourced(project_name)` returns every sentence of the body
   with no valid anchor and no `[SIN FUENTE]` marker, with its byte offsets and a `reason`:
   `sin_anclaje` (never anchored) or `anclaje_roto` (the anchored paragraph no longer exists,
   for example because the PDF was deleted). An empty list means every sentence is anchored or
   declared `[SIN FUENTE]`; it does not mean every anchor is right, so continue.
2. **Look for support for each flagged sentence.** Call
   `search_corpus(query, project=project_name)` with two or three phrasings (English terms
   work best for English PDFs). Read the paragraph, with `get_chunk(chunk_id, context=false)`
   when the result is `truncated`, and `get_chunk_image` for formulas and tables.
3. **Spot-check existing anchors.** For anchors the user cares about (or all of them if the
   document is short), read the anchored paragraph with `get_chunk(chunk_id, context=false)`
   and compare it with the sentence. An anchor proves where a sentence comes from, not that the
   sentence is right: flag numbers, scope or direction that do not match.
4. **Classify every sentence** as one of:
   - **Supported**: a paragraph states it. Give `bibtex_key`, page and the `link`.
   - **Partly supported**: the paragraph says something narrower or different (another number,
     place, year or population). Say what differs and propose a wording that matches.
   - **No support in the library**: nothing found after several phrasings. Suggest the kind of
     source that would support it; the sentence should carry `[SIN FUENTE]` until one is
     uploaded.
   - **Not a factual claim**: signposting or the authors' own aims ("Section 3 describes…").
     These need no source; Underanchor accepts a line `% anclaje:exempt <reason>` just before
     such a sentence. Propose it, do not add it without the user's agreement.
   - **Broken anchor** (`anclaje_roto`): the source paragraph is gone; search again for a new one.

## Report

Give a compact table: sentence (first words), verdict, source (key, page, "view paragraph"
link), proposed action. Then the totals: sentences checked, supported, partly supported, without
support, exempt, broken. Never present a sentence as supported without a paragraph you have read.

## If the user wants the fixes applied

Prefer actions the user confirms in the web:

- `propose_change(project_name, quote, replacement, body, chunk_id=…)` to anchor or reword a
  sentence: on acceptance it is anchored to `chunk_id` and gets its `\cite{}`.
- `add_comment(project_name, quote, body)` to leave a note on a sentence without changing it.
- `remove_anchor(project_name, anchor_id)` removes a wrong anchor (the text stays). It deletes
  provenance, so ask before using it.

Afterwards run `check_unsourced` again and report what is left.
