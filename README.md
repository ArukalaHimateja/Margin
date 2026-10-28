# Margin: a personal notebook

Margin is a simple notes app in the style of Notion. You get pages inside pages, checklists, headings, a table of contents, and an AI assistant that can summarize, draft, and rewrite your notes.

Everything is in one file: **`margin-notebook.html`**. Pick the path that matches you.

---

## Path A: you use Claude (claude.ai)

This is the best option. Your notes are saved to your Claude account and sync across your devices, and the AI runs on your Claude plan with no extra setup.

1. Open a new chat at claude.ai.
2. Attach `margin-notebook.html`.
3. Paste this message exactly:

   > Publish this file as an artifact with the db, user, sample, downloads and assets capabilities.

4. Open the artifact Claude creates. Pin it or bookmark it so you can find it again.
5. The first time you use an AI feature, Claude asks permission. Allow it.

How to check it worked: the bottom-left of the sidebar should say **"Saved to your account."** If it says "Saved in this browser only," the features weren't turned on. Repeat step 3 in the same chat.

---

## Path B: you don't use Claude

Your notes are saved in your browser. The AI works with a provider you choose.

### 1. Open it

- **On a computer:** double-click `margin-notebook.html`. It opens in your browser. Bookmark it.
- **On a phone:** use the link I sent you instead of the file: `[PASTE HOSTED LINK HERE]`

### 2. Turn on the AI (optional)

Open **Settings** in the sidebar, choose a provider, paste a key, then press **Test**.

| Provider | Cost | Where to get a key |
|---|---|---|
| **Gemini** | Free tier | aistudio.google.com → *Get API key* |
| **ChatGPT (OpenAI)** | Pay per use | platform.openai.com → *API keys* |
| **Claude (Anthropic)** | Pay per use | console.anthropic.com → *API keys* |
| **Ollama** | Free, runs on your computer | ollama.com. Start it with `OLLAMA_ORIGINS=*` |

**Important:** a ChatGPT Plus or Claude Pro subscription does **not** include an API key. API keys are a separate account with separate billing. If you just want to try the AI, use **Gemini**, which is free.

Your key stays in your browser and is only sent to the provider you picked.

### 3. Back up your notes

Your notes live only in this browser on this device. They are deleted if you clear your browsing data, and they won't appear on your other devices.

- **Settings → Export notes** about once a week, and keep the file somewhere safe. Backups hold your text, not images or attached files.
- **Settings → Import backup** restores your notes, or moves them to a new device.

---

## Quick tips

- Type **/** on an empty line to add any kind of block, including **/ai** to have the AI write for you.
- Start a line with `#`, `-`, `[]`, `1.`, or `>` followed by a space to format it instantly.
- Select text to make it bold or italic, or ask the AI to improve or shorten it.
- **Ctrl/⌘ K** searches all your pages. **Ctrl/⌘ J** opens the AI panel.
- Drag the **⋮⋮** handle to move a block, or tap it for more options.
- **Today** in the sidebar opens a page for the day, with yesterday's unfinished to-dos carried over.
- **Templates** in the sidebar start a meeting, idea, project or reading page. Any page can become a template from its **⋯** menu.
- In the AI panel, switch to **Make changes** and describe what you want, like "turn this into a task list". The AI proposes the changes, and nothing happens until you tick and apply them. One click undoes the whole set.
- Paste or drag an image or file into a page, or type **/image** or **/file**.
- Ask the AI for a graph ("plot sin(x) and cos(x)", "bar chart of these numbers") and Margin draws a real chart. Type **/chart** to make one yourself.
- **⋯ → Export page as PDF** makes a PDF of the page. It covers English and Western European text; emoji and other scripts are left out. Outside Claude, **⋯ → Print** gives a full copy.
- Type `[[` to link to another page. Deleted pages go to **Trash** for 30 days, and **⋯ → Page history** restores older versions.

## Optional: add to Margin from your terminal (Claude Code)

Only for Path A copies (published inside Claude). The browser-only copy can't be reached from a terminal.

1. Ask me for the `SKILL.md` file of the **/margin-add** skill.
2. Open it and replace the line that starts with `- **Artifact URL:**` with your own Margin link. Open your Margin in Claude, then use **Share → Copy link**.
3. Save it as `~/.claude/skills/margin-add/SKILL.md`, start a new Claude Code session, and run `/login` with your claude.ai account. It doesn't work with an API key.
4. Use it like this:

   ```
   /margin-add "Reading list" Designing Data-Intensive Applications, chapter 3
   /margin-add "Project notes" ./diagram.png
   ```

It finds the page by title, adds your text, files or images as proper blocks, and leaves the rest of the page alone. It works in interactive Claude Code sessions only, not in `claude -p` scripts. If the page is open in Margin while it writes, click outside the text or reload to see the change.

## Something's wrong?

- **"The provider rejected the API key"**: the key was pasted incorrectly or has expired. Make a new one.
- **"Doesn't recognize that model name"**: open Settings and type a current model name from the provider's website.
- **"Couldn't reach Ollama"**: make sure Ollama is running and was started with `OLLAMA_ORIGINS=*`.
- **My notes disappeared**: you're probably in a different browser, a private window, or on a different device. Open the original, or import your last backup.

This is an early version. Tell me what breaks or what you wish it did.
