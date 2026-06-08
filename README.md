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
3. **Install community plugins** (Settings → Community plugins → Browse): `base-board`, `templater-obsidian`, `obsidian-icon-folder` (Iconize), `pretty-properties`, `cmdr`. They're pre-listed in `.obsidian/community-plugins.json`, so they **auto-enable once installed**.
   - **Folder/file icons** are pre-mapped (in `.obsidian/plugins/obsidian-icon-folder/data.json`) using native Lucide icons — no icon packs needed. They appear as soon as Iconize is installed; restart Obsidian if they don't show immediately.
4. **Templater is pre-pointed** at `05 Meta/System/Templates` (shipped config) — nothing to set. *(Optional: bind a hotkey to the `New Project` template under Settings → Templater → Template Hotkeys.)*
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

## Using it with an agent

Once the instruction file is installed, you talk to your agent in plain language — it maps what you say onto the right note type and files it by the conventions. You don't name folders or set frontmatter; the agent does. A few principles it follows:

- **Capture-by-default.** A loose thought lands in `00 Ideas/` unless you say where it goes — it won't guess a home.
- **Propose, don't silently file.** For anything structural (a new Decision, Person, Area, or archiving), it suggests and waits for your yes.
- **Recall reads first.** On a question it reads `conventions.md` → memory → latest daily → warm projects, then answers — so it knows your current state, not just the repo.
- **Same words, any agent.** The phrasings below work whether you're on Claude or Codex; only *how you invoke* differs — see [Invoking across agents](#invoking-across-agents).

#### Capture & triage

| You say… | The agent… | Lands in |
|---|---|---|
| *"Note this down: webhooks should retry with backoff"* | Captures a freeform thought | `00 Ideas/` (`type: idea`) |
| *"Brain-dump: three things I want to try this quarter…"* | Captures each as an idea | `00 Ideas/` |
| *"Promote that idea to a ticket on Website Redesign"* | Converts an Idea → Feature on a project | `01 Projects/Website Redesign/…md` (`type: feature`) |
| *"That idea's not worth keeping"* | Deletes / clears it from the inbox | — |

#### Projects & tickets

| You say… | The agent… | Lands in |
|---|---|---|
| *"Start a project called Website Redesign"* | Scaffolds a project folder + hub + board | `01 Projects/Website Redesign/` |
| *"Add a ticket to Website Redesign: migrate the contact form"* | Creates a Feature (a board card) | `…/Migrate Contact Form.md` (`type: feature`, `status: Unplanned`) |
| *"On that ticket, add subtasks: audit fields, build endpoint, test"* | Adds inline `- [ ]` checkboxes | inside the Feature (not on the board) |
| *"Tick off 'audit fields'"* | Marks a subtask `- [x]` | inside the Feature |
| *"Archive the Website Redesign project — it shipped"* | Proposes moving the folder to Archive | `04 Archive/Website Redesign/` (drops off the board) |

#### Status & board flow

| You say… | The agent… | Lands in |
|---|---|---|
| *"I'm starting on the contact form"* | Sets `status: Doing` | Feature frontmatter (card moves) |
| *"That one's blocked / waiting on design"* | Sets `status: Waiting` | Feature frontmatter |
| *"Queue it up next"* | Sets `status: Next` | Feature frontmatter |
| *"Contact form's done"* | Sets `status: Done` | Feature frontmatter |
| *(or just drag the card on the board)* | Obsidian writes the new `status:` back into the note | Feature frontmatter |

#### Knowledge, people & areas

| You say… | The agent… | Lands in |
|---|---|---|
| *"We decided to use Postgres over Mongo — here's why…"* | Proposes a Decision (choice + alternatives + reasoning) | `05 Meta/Decisions/YYYY-MM-DD …md` |
| *"Sarah from design is the reviewer on this"* | Proposes a Person note on first substantive mention | `05 Meta/People/Sarah.md` |
| *"Client billing is an ongoing thing I own"* | Proposes an Area (responsibility, no end date) | `02 Areas/Client Billing.md` |
| *"Save this for reference: the deploy runbook is…"* | Proposes a Resource note | `03 Resources/…` |

#### Memory & recall

| You say… | The agent… | Lands in |
|---|---|---|
| *"Remember that I prefer short, skimmable docs"* | Records a durable preference | `05 Meta/Memory/preferences.md` |
| *"We're still figuring out the auth approach — keep that in mind"* | Logs an ongoing thread | `05 Meta/Memory/threads.md` |
| *"What am I working on?"* / *"what's blocked?"* | Reads board + memory + dailies, answers | (no write — recall) |
| *"What did I do yesterday?"* / *"remind me about X"* | Reads the relevant daily / note, answers | (no write — recall) |
| *"Log that I spent today on the contact form migration"* | Appends to today's daily note | `Daily/YYYY-MM-DD.md` |

### Example exchanges

**Capture a thought (fastest path):**
> **You:** note this down — idea for the board: a "waiting on someone else" swimlane
> **Agent:** Captured to `00 Ideas/` as *Waiting-On-Someone-Else Swimlane*. Want me to promote it to a Feature on a project, or leave it for triage?

**Spin up work:**
> **You:** new project "Home Server", and add a ticket to set up automated backups
> **Agent:** Created `01 Projects/Home Server/` (hub + board), and a Feature *Set Up Automated Backups* at `status: Unplanned`. Move it to `Next` when you're ready to start.

**Recall:**
> **You:** what's on my plate and what's blocked?
> **Agent:** *Doing:* Migrate Contact Form. *Waiting:* nothing. *Next:* Set Up Automated Backups. *Unplanned:* 2 ideas not yet triaged. (read from the board + latest daily)

**Close out the session:**
> **You:** good night
> **Agent:** Logged today to `Daily/`, moved *Migrate Contact Form* notes into the project worklog, and — you mentioned Sarah is reviewing — want a Person note for her? Open loose end: the backups ticket is still `Unplanned`.

> **Tip:** the daily log and the end-of-session **promotion sweep** (lifting cross-cutting bits into Resources/Decisions/People) happen automatically per the conventions — you mostly just work, and confirm the agent's proposals.

### Invoking across agents

The *what you say* above is identical across agents; the *how it kicks in* differs:

- **Claude (Claude Code / Cowork)** — the `obsidian-vault` skill auto-triggers on capture/recall phrasing, and `wrap-up` fires on close-out cues like *"good night"* or `/wrap-up`. Largely hands-off.
- **Codex / other agents** — the `AGENTS.md` is always-on while you work in the vault, so capture/recall work the same; but there's no skill auto-trigger, so **invoke routines explicitly** — e.g. *"wrap up the session per the conventions"* instead of relying on *"good night"*.
- **Any agent** — if it ever doesn't follow a rule, just say *"check `conventions.md` first"*; that file is the single source of truth.

### Without an agent (plain Obsidian)

The vault works on its own, too:
- **Drag a card** on any `.base` board → the note's `status:` updates automatically.
- **`Cmd+P → Templater: Create new note from template → New Project`** (or a bound hotkey) scaffolds a full project by hand.
- **Insert any template** (Idea, Feature, Decision, Person, …) via Templater for a manually-created note.

## Customising

Everything the agent does is governed by [`05 Meta/System/conventions.md`](./05%20Meta/System/conventions.md) — it's a plain note you edit. The instruction files (`SKILL.md` / `AGENTS.md`) deliberately **don't** restate the rules; they point at `conventions.md` so there's a single source of truth. Start by filling in `05 Meta/Memory/preferences.md`, then delete the `Example Project`.

## License

MIT — see `LICENSE` (add your own). Attribution welcome but not required.
