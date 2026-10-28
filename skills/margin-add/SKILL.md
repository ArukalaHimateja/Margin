---
name: margin-add
description: Add text, notes, a file or an image/chart to a page in your Margin notebook (a claude.ai artifact). Use when the user runs /margin-add or asks to add, append, save or log something "to Margin" or "to my Margin page".
argument-hint: "\"<page title>\" <text, or a path to a file or image>"
---

# Add to Margin

Margin is your personal notebook, a claude.ai artifact:

- **Artifact URL:** PASTE-YOUR-MARGIN-ARTIFACT-LINK-HERE
- **Pages collection:** `data/notebook/pages` (one document per page; the document id is the page id)
- **Activity log collection:** `data/notebook/activity` (one document per day, id `YYYY-MM-DD`)

Arguments: `$ARGUMENTS`. The first quoted part is the page title; the rest is what to add. If the user gave no page, ask which page. If they gave no content, ask what to add.

## Steps

1. **Attach Margin.** Read the artifact once with the Artifact tool (`action: "read"`, the URL above) so this session may write to it. If the tool says artifacts aren't available, tell the user to run `/login` with their claude.ai account (not an API key) and stop. The database actions live in the `ArtifactData` tool; load it with ToolSearch if it is deferred.

2. **Find the page.** `ArtifactData` `list` on `data/notebook/pages` (use `out_dir` if there are many). Match the title case-insensitively. Ignore pages whose `trashed` is set, and pages under a trashed parent. If several pages match, show their paths (follow `parent` ids) and ask. If none match, ask whether to create it and where (top level, or inside a named page).

3. **Turn the content into blocks** (see the block format below). Plain text becomes `p` blocks, one per paragraph. Convert Markdown: `#`/`##`/`###` → `h1`/`h2`/`h3`, `- ` → `bullet`, `1. ` → `num`, `- [ ] `/`- [x] ` → `todo` (with `checked`), `> ` → `quote`, fenced code → `code`, `---` → `divider`. For a dated log entry (meeting notes, "worked on…", a status update), start with an `h3` holding today's date, for example `Oct 28, 2026`, unless the user said otherwise.

4. **Files and images.** Upload the file with the Artifact tool: `action: "publish"`, `url` = the Margin URL, `asset: true`, `file_path` = the local file. Use the returned `id`.
   - PNG, JPEG, GIF or WebP → an `image` block with that `asset` id. If the user described it, put a caption in `html`.
   - PDF, MP4, WebM, CSV, TXT, MD or JSON → a `file` block.
   - Any other type is refused by Margin's storage. Say so, and suggest exporting it to PDF or CSV.
   - To add a chart, either save it as a PNG and upload it, or add a `chart` block with a JSON `spec` (format below).

5. **Write.** Re-read the page document to get its current `version`. Append your blocks to `blocks`. If the last block is an empty `p` (html `""`), insert before it so the page keeps its trailing empty line. Then `ArtifactData` `update` with `if_version` set to that version, sending `blocks` and `updated` (current time in epoch milliseconds). Never send a shortened or rebuilt copy of the existing blocks; keep every existing block exactly as read. If the update fails with a version conflict, re-read and redo it. Don't overwrite.
   - **New page:** `set` a new document with `id`, `title`, `icon` (one emoji or ""), `parent` (page id or null), `order` (a number larger than its siblings'), `updated`, `trashed: null`, `template: false`, `daily: null`, `journal: false`, and `blocks` (end with an empty `p`). If it has a parent, also append `{"id": "<new id>", "type": "page", "page": "<new page id>", "html": ""}` to the parent's blocks, using the same re-read and `if_version` rule.

6. **Log it** (best effort; skip silently if it fails). Read `data/notebook/activity/<today YYYY-MM-DD>`. If it exists, append to `entries`; if not, create `{"day": "<YYYY-MM-DD>", "entries": [...]}`. Entry: `{"t": <epoch ms>, "kind": "edited", "page": "<page id>", "title": "<page title>", "note": "added from the terminal"}` (use `"created"` for a new page). Write it back with `if_version` when the document existed. Keep at most the last 500 entries.

7. **Report** in one or two lines: the page title, and what was added (for example "3 bullets and 1 image"). Remind the user that if the page is open in Margin with the cursor in it, they may need to click outside the text or reload to see the change. Typing on that page at the same moment could overwrite this edit.

## Block format

Every block needs a unique `id`: `"b"` followed by about 10 random lowercase letters and digits.

| type | fields |
|---|---|
| `p`, `h1`, `h2`, `h3`, `bullet`, `num`, `quote`, `code` | `html` |
| `todo` | `html`, `checked` (true/false) |
| `callout` | `html`, `icon` (one emoji) |
| `toggle` | `html` (the summary line), `body` (hidden text), `open` (false) |
| `divider`, `toc` | `html: ""` |
| `page` | `page` (a page id), `html: ""` |
| `image` | `asset` (32-char id), `mime`, `name`, `size` (bytes), `html` (caption, may be "") |
| `file` | `asset`, `mime`, `name`, `size`, `html: ""` |
| `chart` | `spec` (below), `html` (caption, may be "") |

**`html` rules.** Escape `&`, `<` and `>` in text. The only tags allowed are `<b>`, `<i>`, `<s>`, `<code>` and `<br>`, plus page links written as `<a class="plink" data-page="<page id>" href="#p-<page id>">Title</a>`. Margin strips anything else. In `code` blocks, keep line breaks as `\n`.

**Chart `spec`.**
- Function line chart: `{"type":"line","title":"y = sin(x)","x":{"from":0,"to":"2*pi","label":"x"},"series":[{"name":"sin(x)","expr":"sin(x)"}]}`. Expressions use `x`, `+ - * / ^`, the functions `sin cos tan asin acos atan sinh cosh tanh exp ln log log10 log2 sqrt abs floor ceil round`, and the constants `pi` and `e`. An optional `"y":{"from":..,"to":..}` fixes the vertical range.
- Data points: give each series `"points":[[x,y],...]` instead of `expr`.
- Bar chart: `{"type":"bar","title":"...","labels":["A","B"],"series":[{"name":"2026","values":[3,5]}]}`.
- Up to 4 series.

## Don't

- Don't edit pages the user didn't name, or delete anything.
- Don't touch `data/users/...`, versions (`data/notebook/pages/<id>/versions`), or any other collection.
- Don't paste the Margin URL into chat output unless asked.
