---
name: supervisor-ready
description: Get an Underanchor document ready for a supervisor, advisor, reviewer or co-author - every sentence anchored or honestly marked, open comments answered, citations consistent - and explain how to share it. Use when the user says "prepare this for my supervisor", "ready for review", "send it to my advisor", "before I share it", "déjalo listo para mi tutor" or asks what is left before showing the draft to someone.
---

# Supervisor-ready with Underanchor

A reviewer in Underanchor sees the draft and, for each citation, the exact paragraph it rests
on. The draft is ready when the reviewer can check every claim in one click and nothing is
hidden. This skill needs the connector with **Read and write** permission; with read-only
permission, explain that and call `get_help` with "connect read and write".

## Checklist

Work through it in order and keep a running list for the final summary.

1. **Read the project.** `list_projects`, then `read_project(project_name)`: text, `anchors`
   and `comments`. A project shared with the user uses the address `<owner email>/<project>`.
2. **No silent gaps.** Run `check_unsourced(project_name)`. Every sentence it returns needs one
   of: an anchor (see the `anchored-writing` skill), the `[SIN FUENTE]` marker, or, if it is not
   a factual claim, an exempt line proposed to the user. The goal is an empty list.
3. **Declared gaps.** Count the `[SIN FUENTE]` markers in the text and list them. They are
   honest and the reviewer will see them; ask the user whether to look for sources now
   (`search_corpus` with `project=project_name`) or to leave them for the reviewer.
4. **Broken anchors.** Sentences that `check_unsourced` returns with `reason` `anclaje_roto`
   are anchored to a paragraph that no longer resolves (for example, its PDF was deleted).
   Search for a replacement source; to drop the old anchor, `remove_anchor` with its `id` from
   `read_project` (ask first: it deletes the provenance, not the text).
5. **Citations match the library.** Each `\cite{}` key in the text should be the `bibtex_key`
   of a paper in the project's library (`list_papers(project=project_name)`). Report keys that
   match no paper.
6. **Open comments.** For each comment with `state` `open`:
   - a question or a request you can address: make the change (with `propose_change`, or
     `replace_selection` if the user asked you to write directly), then
     `resolve_comment(project_name, comment_id, reply)` saying what you did;
   - something that needs the user's decision: `reply_comment(project_name, comment_id, body)`
     and leave it open;
   - a suggestion from a reviewer (`kind` `suggestion`) or a tracked change (`track`): leave
     it, the user accepts or rejects those in the web.
7. **Final check.** Run `check_unsourced` again.

## Summary for the user

Report: sentences anchored, `[SIN FUENTE]` left (with where), broken anchors fixed, comments
resolved or answered, keys without a paper. Offer a two-line note the user can send to the
reviewer with what is still open.

## Sharing is done by the user in the app

These tools do not share, compile or create links. Call `get_help` with "share with a
reviewer" (and "provenance report" if the reviewer has no account) and give the user the steps
with the labels it returns. In short: **Share** (*Compartir*) → email and **Role** (*Papel*)
**Review** (*Revisar*) → **Invite** (*Invitar*). For someone without
an account, the **Provenance report** (*Informe de procedencia*) in the same window lists every
sentence next to its paragraph. Use the labels of the user's interface language.
