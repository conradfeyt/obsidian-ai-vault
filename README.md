# AI Knowledge Base — Obsidian Vault Starter

A ready-to-clone **Obsidian vault** wired for an **AI coding agent** (Claude, Codex, or any agent that can load an instructions file and edit local files). PARA-organised, with a document-as-ticket kanban board, note scaffolds, and an agent that reads + writes the vault by one authoritative rulebook.

> **The core idea.** The AI integration is *not* a plugin — it's a **markdown instruction file** the agent loads (`SKILL.md` for Claude, `AGENTS.md` for Codex) that points it at one rules file, `05 Meta/System/conventions.md`. Edit the rules → the agent's behaviour changes next session.

```
Obsidian vault  ──(read/write .md)──  AI agent (Claude / Codex / …)
   │                                      │
   ├─ 05 Meta/System/conventions.md ◄──── the rulebook (agent reads this first)
   ├─ folders (PARA + Meta)               │
   ├─ .base boards (kanban over notes)    └─ instruction file: SKILL.md (Claude) | AGENTS.md (Codex)
   ├─ Templater templates
   └─ community plugins (board / icons / templates / properties)
```

## Quick start

1. **Clone** this repo and open the folder as a vault in [Obsidian](https://obsidian.md).
2. **Enable core plugins:** Bases, Daily notes (location → `Daily`), Templates, Properties.
3. **Install community plugins** (Settings → Community plugins → Browse): `base-board`, `templater-obsidian`, `obsidian-icon-folder`, `pretty-properties`, `cmdr`.
4. **Point Templater** at `05 Meta/System/Templates`.
5. **Install the agent instruction file** for your tool:
   - **Claude Code:** `cp "05 Meta/System/obsidian-vault.SKILL.md" ~/.claude/skills/obsidian-vault/SKILL.md` (and the same for `wrap-up.SKILL.md`), then restart.
   - **Codex:** use `05 Meta/System/AGENTS.md` (copy to the vault root or your global Codex instructions).
6. **Set your vault path:** in `conventions.md` and the instruction file, replace `<YOUR_VAULT_PATH>` and `<YOUR NAME>` with your own.
7. Open `Active Work.base` to see the board. Try: *"what am I working on?"* or *"note this down: …"*.

📖 **Full walkthrough:** [`SETUP.md`](./SETUP.md) — step-by-step, agent-agnostic, with the why behind each piece.

## What's inside

```
00 Ideas/          Capture point — freeform unsorted thoughts, triaged later.
01 Projects/       Active projects (one folder each: hub + board + feature notes).
   Example Project/ ← a worked example to show the model; delete when you're set up.
02 Areas/          Ongoing responsibilities with no end date.
03 Resources/      Reference material by topic.
04 Archive/        Done / dormant.
05 Meta/
   Memory/         Durable agent memory (preferences.md, threads.md) — fill these in.
   Decisions/      Decisions + reasoning.
   People/         Notes about people you work with.
   System/         conventions.md (the rulebook), Templates/, the SKILL/AGENTS instruction files.
Daily/             Daily notes (YYYY-MM-DD.md).
Active Work.base   Vault-wide kanban board over all Feature notes.
```

## The model in one minute

- **Project** = a folder with a hub note (the container).
- **Feature** = a note inside a project = **the ticket** (a card on the board; has a `status:`).
- **Subtask** = an inline `- [ ]` checkbox inside a Feature (not on the board).
- **Status flow:** `Unplanned → Next → Doing → Waiting → Done`. Drag a card on the board → it writes the new `status:` back into the note.

## Customising

Everything the agent does is governed by [`05 Meta/System/conventions.md`](./05%20Meta/System/conventions.md) — it's a plain note you edit. The instruction files (`SKILL.md` / `AGENTS.md`) deliberately **don't** restate the rules; they point at `conventions.md` so there's a single source of truth. Start by filling in `05 Meta/Memory/preferences.md`, then delete the `Example Project`.

## License

MIT — see `LICENSE` (add your own). Attribution welcome but not required.
