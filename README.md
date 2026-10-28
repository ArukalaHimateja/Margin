# Margin

A notebook app built as a single-file Claude Artifact — a two-pane writing surface with a page tree sidebar and a block-based editor (paragraphs, headings, to-dos, bullets, code).

## Features

- Nested page tree with daily journal pages ("Today")
- Block-based editing: paragraphs, headings, to-dos, bullet lists, code blocks
- To-dos carry over automatically from the previous daily page
- Persists via the Claude Artifact `db` runtime capability, with local fallback storage
- AI assistance via the `sample` capability
- File/asset handling via `downloads` and `assets` capabilities

## Running it

This is a single self-contained `index.html` file designed to run as a Claude Artifact (it calls `window.claude.use(...)` for storage, auth, and AI sampling). Opening it directly in a plain browser will work for local-only editing, but persistence and AI features require the Claude Artifact runtime.
