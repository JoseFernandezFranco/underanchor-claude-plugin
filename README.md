# Underanchor plugin

**Every claim, one click from its source.**

[Underanchor](https://underanchor.com) is a LaTeX editor where every sentence is anchored to the
exact paragraph of the source PDF it comes from. This plugin connects your AI assistant to your
own Underanchor library and projects, and adds three skills that make it write and review the
way Underanchor expects: cite only from your PDFs, link every citation to its highlighted
paragraph, and mark what has no source instead of inventing it.

Underanchor does not include an AI model. You bring your own assistant; the plugin gives it the
tools and the working method.

## What you need

- An Underanchor account at [underanchor.com](https://underanchor.com), with the PDFs you want
  to cite uploaded to a library. The connector works on every plan.
- For writing in a document: an Underanchor project (a LaTeX document) that uses that library.

## What's inside

| Part | What it does |
|---|---|
| Connector `underanchor` (`.mcp.json`) | Remote MCP server at `https://underanchor.com/mcp`, signed in with OAuth. |
| Skill `anchored-writing` | Drafts or rewrites text citing only your library; each factual sentence is anchored to a paragraph, and unsupported ones are written with `[SIN FUENTE]` ("no source"). |
| Skill `source-audit` | Checks a draft sentence by sentence: supported, partly supported, without support, broken anchor; proposes fixes you accept in the app. |
| Skill `supervisor-ready` | Gets a document ready for your supervisor or reviewer: no silent gaps, broken anchors fixed, open comments answered, and the steps to share it. |

The plugin contains no executable code: only Markdown skills, a connector declaration and an
icon. `plugin.json` and `mcp.json` at the root describe the same plugin in the
open [Agent Plugins](https://agent-plugins.org) format, for clients that read it.

## Connect

1. Install the plugin (from the Claude directory, or in Claude Code:
   `/plugin marketplace add JoseFernandezFranco/underanchor-claude-plugin` and then
   `/plugin install underanchor@underanchor`).
2. Connect the **Underanchor** connector (on claude.ai and in Cowork: the plugin's
   **Connectors** tab). Your browser opens Underanchor: sign in and choose a permission:
   - **Read only (research)**: search your library and read paragraphs, formulas, outlines and
     your own highlights and notes. Your documents are never touched.
   - **Read and write (draft)**: also read your projects and write in them, with every sentence
     anchored. Most writes are proposals you accept or reject in the app.
3. Ask, for example:
   - "What do my sources say about traffic and NO2? Cite the paragraphs."
   - "Draft the related-work section of my project citing only my library."
   - "Which sentences in my draft have no source?"
   - "Prepare this chapter for my supervisor."

You can revoke access at any time in Underanchor: account menu → **Connect an AI** →
**Authorized applications** → **Revoke**.

## Tools the connector exposes

Read (library): `search_corpus`, `get_chunk`, `get_chunk_image`, `get_paper_outline`,
`list_papers`, `list_libraries`, `list_pdf_annotations`, `get_help`.

With **Read and write** only: `list_projects`, `read_project`, `get_editor_selection` and
`check_unsourced` (read); `add_comment`, `propose_change`, `reply_comment` and
`resolve_comment` (write comments and proposals, never the document itself);
`replace_selection` (rewrites part of the `.tex`) and `remove_anchor` (removes the provenance of
a sentence, not its text), both marked as destructive so your client asks before running them.

Every tool works only on the data of the account that authorised it. None reaches the open web.

## What the plugin sends and where

The skills only tell your assistant how to use the connector. When you use it, your assistant
sends the requests you trigger (search queries, the text it proposes, comment replies) to
`https://underanchor.com/mcp`, and Underanchor returns paragraphs of your PDFs, your LaTeX
document, your highlights and notes, and links back to `https://underanchor.com`. What your
assistant receives is then processed by its provider (for example Anthropic) under your own
agreement with that provider. Underanchor does not send your data to any AI model, does not
train models on it and does not sell it.

## Privacy Policy

Underanchor's privacy policy covers what data is collected, how it is used and stored, who it is
shared with, how long it is kept and how to contact us:

- Privacy policy: https://underanchor.com/legal/privacidad (English:
  https://underanchor.com/legal/en/privacidad)
- Terms: https://underanchor.com/legal/en/terminos

Data is hosted in the European Union. Connector tokens are stored only as hashes and can be
revoked from your profile.

## Support

Write to contacto@underanchor.com, or use **Feedback** in the app. Usage guides for every
feature are available to your assistant through `get_help`.

## License

The plugin files (skills, manifests, README) are released under the [MIT License](LICENSE). The
Underanchor service itself is not open source and is governed by its terms.

Underanchor is not affiliated with, sponsored by or endorsed by Anthropic, OpenAI or any
publisher.
