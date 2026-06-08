---
type: resource
created: 2026-06-08
tags:
  - pkm
  - obsidian
  - ai
  - setup
cssclasses:
  - wide-page
---
# Obsidian + AI Vault — Setup Guide

A step-by-step to replicate this vault from scratch: a PARA-organised Obsidian vault with a document-as-ticket board, scaffolding templates, and an **AI coding agent that reads and writes the vault by its own conventions**. Agent-agnostic — works with Claude (Claude Code / Cowork), OpenAI **Codex**, or any agent that can load an instructions file and edit local files. Built for sharing with colleagues who've asked "how do I get the same setup?"

> **The core idea.** The AI integration is *not* a plugin you install — it's a **markdown instruction file** that your agent loads, pointing it at one authoritative rules file in the vault (`conventions.md`). The *file's name and location differ per agent* — a **Skill** (`SKILL.md`) for Claude, an **`AGENTS.md`** for Codex — but the content is the same idea: "read `conventions.md`, follow this protocol." Change the rules → the agent's behaviour changes next session. Everything below builds the vault that instruction file expects.

---

## 0. What you're building (architecture)

```
Obsidian vault  ──(file tools: read/write .md)──  AI agent (Claude Code / Cowork / Codex / …)
   │                                                   │
   ├─ conventions.md  ◄──────── the rulebook ──────────┤  (instruction file reads this first)
   ├─ folders (PARA + Meta)                            │
   ├─ .base boards (kanban over notes)                 └─ instruction file (session protocol):
   ├─ Templater templates (scaffold notes/projects)         SKILL.md (Claude) | AGENTS.md (Codex)
   └─ plugins (board / icons / templates / properties)
```

Three layers:
1. **The vault** — folders, frontmatter schemas, boards, templates (plain Obsidian).
2. **The conventions** — one markdown file (`conventions.md`) that *is* the source of truth for how everything is organised.
3. **The agent instruction file** — installed where your agent looks for instructions (a Skill for Claude, `AGENTS.md` for Codex); tells it when to engage and to obey `conventions.md`.

---

## 1. Prerequisites

- **Obsidian** (desktop) — free. https://obsidian.md
- **An AI agent that can load an instructions file + edit local files**, one of:
  - **Claude Code** (CLI) — instruction files are *Skills* in `~/.claude/skills/`.
  - **Claude Cowork** — Skills installed via the app; vault mounted as a connected folder.
  - **OpenAI Codex** (CLI) — reads `AGENTS.md` instruction files.
  - **Any other agent** with an "always-on instructions" / rules-file mechanism works too (see §8).
- **Obsidian Sync** (optional, paid) — if you want the vault on multiple devices. Git works too.

---

## 2. Create the vault + folder structure

New vault in Obsidian, then create these top-level folders (PARA + a Meta bucket):

```
00 Ideas/         Capture point — freeform unsorted thoughts, triaged later.
01 Projects/      Active projects. One folder per project (hub note + board + feature notes).
02 Areas/         Ongoing responsibilities with no end date.
03 Resources/     Reference material by topic.
04 Archive/       Done/dormant — anything not active.
05 Meta/
  Memory/         Durable agent memory (preferences, ongoing threads).
  Decisions/      Decisions + reasoning.
  People/         Notes about people you interact with.
  System/         conventions.md, templates, board configs, the agent instruction files.
Daily/            Daily notes (YYYY-MM-DD.md).
```

Drop a `.gitkeep` in each if you use git, so empty folders survive.

**The unit-of-work model** (worth internalising — it's what makes the board work):
- **Project** = a folder with a hub note (the container).
- **Feature** = a markdown note inside a project = **the ticket** (one card on the board, has a `status:`).
- **Subtask** = an inline `- [ ]` checkbox inside a Feature note (not on the board).

---

## 3. Enable core plugins

Settings → Core plugins — turn on:
- **Bases** — the engine behind `.base` board files. *(Required.)*
- **Daily notes** — set "New file location" to `Daily`.
- **Templates** — can stay on, but Templater (below) does the real work.
- **Properties** — frontmatter UI.
- Handy extras already on by default: Backlinks, Outgoing links, Tag pane, Outline, Bookmarks, Graph.

---

## 4. Install community plugins

Settings → Community plugins → Browse. Install + enable these **five** (the replicable core):

| Plugin                | id                     | What it does here                                                                                                                       |
| --------------------- | ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Base Board**        | `base-board`           | Renders a `.base` file as a drag-and-drop **kanban board**. Cards = notes; dragging a card writes the new `status:` back into the note. |
| **Templater**         | `templater-obsidian`   | Scripted templates (`<% %>` syntax). Powers the note scaffolds + the one-shot "New Project" creator. *(Not the core Templates plugin.)* |
| **Iconize**           | `obsidian-icon-folder` | Per-file/-folder icons. Used to give every project a consistent file-tree look.                                                         |
| **Pretty Properties** | `pretty-properties`    | Nicer rendering of frontmatter properties.                                                                                              |
| **Commander**         | `cmdr`                 | Custom command buttons / macros in the UI.                                                                                              |

> Plugins in *this* vault that you should **skip** when replicating: `realclaudian` + `claudian-side-note` are bespoke (own repos — see §10, optional), `3d_embeds` is unrelated, `side-note` is legacy/disabled.

**Configure Templater:** Settings → Templater → Template folder location = `05 Meta/System/Templates`. (Optional but recommended: bind a hotkey to the *New Project* template under Template Hotkeys.)

---

## 5. Write `conventions.md` — the rulebook (the heart of it)

Create `05 Meta/System/conventions.md`. This is the single authoritative doc the AI reads first and obeys. It's a normal note you can edit anytime; the next agent session picks up the change. It should define:

- **Folder layout** (from §2).
- **The unit-of-work model** (Project → Feature → Subtask).
- **Frontmatter schemas** for each note type. The essentials:

```yaml
# Project — 01 Projects/<Project>/<Project>.md
type: project
status: Active        # Active | On Hold | Done | Dropped
created: YYYY-MM-DD
area: "[[Area Name]]" # optional

# Feature (the ticket) — 01 Projects/<Project>/<Feature>.md
type: feature
status: Unplanned     # Unplanned | Next | Doing | Waiting | Done
project: "[[Project Name]]"
created: YYYY-MM-DD

# also: type: area | resource | decision | memory | person | idea | daily
```

- **Naming** (Title Case notes; `YYYY-MM-DD.md` dailies).
- **Wikilinks vs tags** (entities = `[[links]]`; qualities = `#tags`; status is a *property*, not a tag).
- **The session protocol** the agent follows at session start (see §8).
- **During-session behaviour**: capture rules, how features move on the board, the **session-end promotion sweep**, the daily-log habit.
- **Hard nos** (don't read the whole vault every session; don't reorganise/archive without asking; don't bulk-edit without a diff).

> **Fastest path:** copy this vault's `05 Meta/System/conventions.md` as your starting point and edit names/areas to taste. It's deliberately the *only* place rules live — the agent instruction file (Skill / `AGENTS.md`) points at it rather than duplicating, so they never drift.

---

## 6. Create the templates

In `05 Meta/System/Templates/`, one template per note type (Templater syntax). This vault ships ten:

```
Idea.md · Project.md · Project.base · New Project.md · Feature.md
Area.md · Resource.md · Decision.md · Person.md · Memory.md
```

Key syntax: `<% tp.file.title %>` (filename), `<% tp.date.now('YYYY-MM-DD') %>` (today), `<%* … %>` (scripted block).

- **Quote every `<% %>` in YAML frontmatter** (e.g. `created: "<% tp.date.now('YYYY-MM-DD') %>"`) — unquoted braces confuse Obsidian's Properties parser.
- **`New Project.md`** is a Templater *driver script*: prompts for a project name, then scaffolds the folder + hub note + `.base` board + `assets/` + `outputs/` subfolders, and sets folder icons via Iconize — all in one command (`Cmd+P → Templater: Create new note from template → New Project`).

---

## 7. Set up the boards (`.base` files)

Two kinds, both rendered by Base Board:

**Vault-wide board** — `Active Work.base` at the vault root:
```yaml
filters:
  and:
    - type == "feature"
    - '!file.inFolder("05 Meta")'
    - '!file.inFolder("04 Archive")'
views:
  - type: kanban
    name: Active Work
    groupBy:
      property: status
    boardColumns: [Unplanned, Next, Doing, Waiting, Done]
```

**Per-project board** — `01 Projects/<Project>/<Project>.base`, filtered to that project:
```yaml
filters:
  and:
    - type == "feature"
    - 'project == "[[<Project Name>]]"'
views:
  - type: kanban
    groupBy:
      property: status
```

Open a `.base` file and Base Board shows the kanban. Drag a card → it writes the new `status:` into the underlying note. (The New Project template stamps a per-project board automatically.)

---

## 8. Wire up your AI agent (the integration)

This is what turns a plain vault into an AI-managed one. The portable artifact is **one instruction file** that says: *here's the vault, read `conventions.md` first, follow this session protocol, obey those rules.* Author it once; install it wherever your agent looks. Keep the master copy in the vault (`05 Meta/System/`) so it's synced and re-installable.

**What the instruction file must contain** (agent-agnostic):
- The **vault path**.
- A **session-start protocol**: on engaging, read in order — (1) `conventions.md`, (2) everything in `05 Meta/Memory/`, (3) the most recent `Daily/` note, (4) active projects/features touched in the last 7 days. *Do not read the whole vault.*
- A pointer that **`conventions.md` is authoritative** — the instruction file does **not** restate the rules (no duplication = no drift).
- *(Claude only)* a `description:` in frontmatter that triggers on capture/recall intent ("remember this", "what's the status of X", "add a feature"). Codex/rules files are always-on, so they don't need a trigger — instead **scope them** to the vault (see below) so they don't fire on unrelated repos.

Then install for your agent:

**Claude Code** — instruction file = a *Skill*:
```bash
mkdir -p ~/.claude/skills/obsidian-vault
cp "<vault>/05 Meta/System/obsidian-vault.SKILL.md" ~/.claude/skills/obsidian-vault/SKILL.md
```
Restart the session (Claude Code does **not** hot-reload skills). `/obsidian-vault` then appears.

**Claude Cowork** — install the Skill via the app (zip `SKILL.md` inside a folder named `obsidian-vault/` with a `.skill` extension → "Save skill"), and mount the vault as a connected folder.

**OpenAI Codex** — instruction file = `AGENTS.md`. Codex loads `AGENTS.md` from the working directory (and merges a global one). Two options:
- *Vault-scoped (recommended):* put an `AGENTS.md` at the **vault root** with the same content; run Codex with the vault as the working directory when doing vault work. Scoped automatically — it won't affect your code repos.
- *Global:* add the same block to your global Codex instructions (`~/.codex/AGENTS.md` / the instructions path in your Codex config) — but then guard it with "only when working inside the Obsidian vault at `<path>`" so it stays dormant elsewhere.
Drop the Claude-style `description:` frontmatter for the Codex copy (it's Claude-specific); keep everything else verbatim. Check Codex's own docs for the exact global-instructions location, as it evolves.

**Any other agent** — put the same instruction block in whatever "always-on instructions / rules file" the agent supports (system prompt, project rules, etc.), scoped to the vault.

> **Keep canonical copies in the vault** (`05 Meta/System/obsidian-vault.SKILL.md`, and an `AGENTS.md` if you use Codex) so a wiped install is one `cp` away from restored.

---

## 9. (Optional) The wrap-up skill

A companion instruction file, `wrap-up`, gives you an on-demand **session close-out** (`/wrap-up` on Claude, or it fires on "good night" / "let's wrap up"): it writes the daily-log outcome, updates project/feature/thread notes, reconciles notes against real git/PR state, runs a capture sweep for people/areas/decisions/resources, and lists loose ends. Same install pattern as §8 (a second Skill for Claude, or a second `AGENTS.md` section for Codex, + a canonical copy in `05 Meta/System/`). It references `conventions.md §7/§8` rather than duplicating the rules.

---

## 10. (Optional, advanced) AI *inside* Obsidian

This vault also runs Claude *embedded in Obsidian* via two bespoke plugins — **`realclaudian`** (a Claude chat panel) and **`claudian-side-note`** (inline review comments you can address with `@claude`, replies written back into the plugin's `data.json`). These are **custom, not in the community store** — skip them for a standard replication. The §8 integration (an external agent — Claude Code / Cowork / Codex — driving the vault from outside) is the portable approach; the in-app plugins are a personal, Claude-specific extension layer.

---

## 11. First run — verify it works

1. **Scaffold a project:** `Cmd+P → Templater → New Project` → name it. Confirm the folder, hub note, `.base`, and `assets/`+`outputs/` appear with icons.
2. **Add a feature:** create a note in the project with `type: feature` + `status: Next`. Open the project `.base` — it should show as a card.
3. **Drag it** to another column → reopen the note → `status:` changed. ✅
4. **Talk to the agent:** in your agent (Claude Code / Cowork / Codex), "what am I working on?" or "remember: <X>". It should read `conventions.md` + memory + the latest daily, then answer/capture by your rules.
5. **Capture test:** "note this down: …" → expect a `00 Ideas/` note in the right schema.

If the agent ignores the rules, check: the instruction file is installed in the right place (Claude Skill / Codex `AGENTS.md`), the session was restarted, and `conventions.md` exists at the path the instruction file names.

---

## Cheat-sheet: the files that matter

| File | Role |
|---|---|
| `05 Meta/System/conventions.md` | The rulebook. Authoritative. Edit to change agent behaviour. |
| `05 Meta/System/obsidian-vault.SKILL.md` | Canonical agent instructions (triggering + session protocol). Copy for Codex as an `AGENTS.md`. |
| `05 Meta/System/wrap-up.SKILL.md` | Optional close-out instructions. |
| `05 Meta/System/Templates/*` | Note + project scaffolds (Templater). |
| `Active Work.base` / `<Project>.base` | Kanban boards over your notes. |
| installed instruction file | Claude: `~/.claude/skills/<name>/SKILL.md` (restart after changes). Codex: `AGENTS.md` (vault root or global). |

**One-line summary for the curious:** *PARA folders + a document-as-ticket model + a single `conventions.md` rulebook that an AI agent (Claude or Codex) obeys — so the agent reads, writes, and tidies the vault the same way you would.*
