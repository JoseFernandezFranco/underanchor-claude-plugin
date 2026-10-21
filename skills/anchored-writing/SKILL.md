---
name: anchored-writing
description: Draft or rewrite text in an Underanchor project citing only the user's own library, with every factual sentence anchored to the PDF paragraph that supports it. Use when the user asks to write, draft, expand or rewrite a section, paragraph or sentence of their paper, thesis or report "with my sources", "citing my PDFs", "with references from my library", or says "redacta", "escribe" or "cita" about an Underanchor project.
---

# Anchored writing with Underanchor

Underanchor links each sentence of a LaTeX document to one paragraph of a PDF in the user's
library (an *anchor*). Your job is to write text where every factual sentence carries such an
anchor, and to mark honestly what the library does not support.

## Rules that never bend

1. **Cite only from the library.** Every factual claim must come from a paragraph returned by
   `search_corpus` (or read with `get_chunk`) for this project. Never cite from your own
   knowledge, from the web, or from a paper the user mentions but has not uploaded.
2. **No support, no claim.** When the library has nothing that backs a sentence, do not
   write it: leave the sentence out and tell the user in the chat which claim is missing and
   what kind of source would support it. Never invent a reference, a number or a page.
3. **The paragraph must say it.** Paraphrase faithfully. Numbers, units, places, years and
   the direction of an effect must match the paragraph. If the paragraph only partly supports
   the sentence, narrow the sentence until it matches.
4. **One sentence, one anchor.** Each write anchors the inserted text to one paragraph, so
   insert one supported sentence (or one clause) per call.

## Steps

1. **Find the project.** Call `list_projects`. If the tool is not available, the user
   connected Underanchor with read-only permission: you can still search and quote sources
   in the chat, but to write in the document they must reconnect choosing **Read and write**
   (call `get_help` with "connect read and write" for the exact steps). A project shared by
   someone else appears with the address `<owner email>/<project>`: pass that address as
   `project_name` or `project`.
2. **Read it.** Call `read_project(project_name)` to get the `.tex`, the existing `anchors`
   (byte offsets) and open `comments`. Write in the language and LaTeX style the document
   already uses. If the user selected a passage in the web editor, `get_editor_selection`
   returns it with its offsets.
3. **Search the project's library.** For each claim call
   `search_corpus(query, project=project_name)`. Passing `project` keeps the search inside
   the library that project may cite. Tips:
   - try two or three phrasings, and English terms even if the text is in another language
     (most PDFs are in English);
   - results come with `chunk_id`, `bibtex_key`, `page`, `text` and often a `link`;
   - a result found only by meaning (`channels` = `["vec"]`) with `similarity` below about
     0.85 is a weak match, not evidence: read it before using it;
   - if `text` has `truncated: true`, read the whole paragraph with
     `get_chunk(chunk_id, context=false)` before relying on figures or nuances;
   - formulas, tables and figures: read them with `get_chunk_image(chunk_id)` and cite their
     `chunk_id` like any paragraph;
   - `user_annotations` on a result are the user's own highlights and notes: they show what
     the user cares about in that source.
4. **Write.** Choose the tool by how much control the user wants:
   - **Default: `propose_change`** with `quote` (the exact fragment of the `.tex` to replace,
     same spaces and LaTeX commands; add `occurrence` if it appears more than once),
     `replacement`, a short `body` with the reason, and the `chunk_id` that supports it. The
     user accepts or rejects it in the web; on acceptance the whole `replacement` is anchored
     to that paragraph and gets its `\cite{}` if it has none. So quote exactly the sentence you
     are replacing (a placeholder, a weak sentence, an old `[SIN FUENTE]` marker) and keep the
     replacement to that one sentence. Nothing changes in the document until they accept.
   - **`replace_selection`** only when the user asked you to write directly. It overwrites
     bytes `[start, end)` of the `.tex` (UTF-8 byte offsets from `read_project` or
     `get_editor_selection`; `start` = `end` inserts without deleting) and anchors the
     inserted text to `chunk_id`. Put the citation yourself in `text`, as
     `\cite{<bibtex_key>}` with the key from the search result. Call `read_project` again
     before the next write, because offsets move.
   - A sentence without support is not written at all.
5. **Check.** Call `check_unsourced(project_name)`. It lists sentences with no valid anchor
   (older documents may carry a `[SIN FUENTE]` marker, which is still accepted) (`reason` `sin_anclaje`: never anchored; `anclaje_roto`: its paragraph
   no longer resolves). Fix them or tell the user which remain.

## How to answer in the chat

- For each source you use, give the author/key and page, and include its `link` as a
  "view paragraph" link so the user can open the highlighted paragraph in one click.
- Say plainly which claims you left out for lack of a source and what kind of source would
  support them, so the user can upload it.
- Keep tool output out of the answer: summarise, do not paste JSON.

## When the user asks how to do something in the app

Uploading PDFs, creating a project, compiling, sharing or plans are done in the web app, not
through these tools. Call `get_help` with the user's question (in English) and guide them with
the on-screen labels it returns. Labels come as **English** (*Spanish*): use the one that
matches the user's interface language.
