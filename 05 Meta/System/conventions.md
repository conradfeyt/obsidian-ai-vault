---
type: meta
category: system
status: active
created: 2026-05-26
updated: 2026-05-26
---

# Vault Conventions

This is the authoritative rules doc that the `obsidian-vault` skill points the agent to. Edit this file like any other note to change how the agent behaves — the next session reads the updated rules.

> **Vault path:** `<YOUR_VAULT_PATH>/`
> **Org style:** PARA + Meta
> **Owner:** <YOUR NAME>
> **Board tool:** Base Board (reads `.base` files; cards are `.md` notes)

---

## 1. Folder layout

```
00 Ideas/        Capture point. Freeform unsorted thoughts land here, triaged manually.
01 Projects/     Active projects. Each project is a folder containing the project hub note + .base + feature notes + assets/ + outputs/.
02 Areas/        Ongoing responsibilities with no end date.
03 Resources/    Reference material organized by topic.
04 Archive/      Completed projects, dormant areas, anything not active.
05 Meta/
  Memory/        Durable agent memory: preferences, ongoing threads.
  Decisions/     Decisions made + the reasoning.
  People/        Notes about people the user interacts with.
  System/        This file, templates, the .base file(s), and any future skill config.
Daily/           Daily notes (YYYY-MM-DD.md). Optional but enabled.
```

Each top-level folder contains a `.gitkeep` so it survives sync even when empty.

---

## 2. The unit-of-work model

The system uses a **Project → Feature → Subtask** hierarchy:

- **Project** — a folder under `01 Projects/` with a hub note. The container.
- **Feature** — a markdown note inside a project folder. **This is the ticket.** Each Feature appears as a card on Base Board, with its own `status:`.
- **Subtask** — an inline `- [ ]` checkbox inside a Feature note's body. Rendered + toggleable natively by Obsidian (no plugin needed). **Subtasks do NOT appear on Base Board** — they're sub-items within their parent Feature.

Mental model: a Feature is to a Linear issue what a project is to a Linear project. Subtasks inside a Feature are the acceptance criteria.

**Project-root rule.** Inside a project folder, the only `.md` files allowed at the root are (a) the hub note and (b) Feature notes. Nothing else. Other documents go in subfolders:

- `outputs/` — working / refinement artifacts the agent (or the user) produces during a session. Synthesis docs, deep-dives, applied critiques, feature specs, decision drafts. Promote a mature output to a Feature or Decision note when appropriate; otherwise it lives in `outputs/`.
- `findings/` — supportive reference research that *informs* the spec or features. Raw research, evidence quotes, competitor breakdowns, downloaded assets. Created on demand per project; not a default scaffold sibling.
- `assets/` — visual / binary assets (images, mockups, logos, PDFs). Default scaffold sibling.

This rule keeps the project root scannable: you open the folder and see the spec, the board, and the active tickets — never an undifferentiated pile of working drafts.

---

## 3. Frontmatter schemas

Every note the user cares about should have frontmatter matching one of these.

### Project — `01 Projects/<Project>/<Project>.md`

```yaml
---
type: project
status: Active        # Active | On Hold | Done | Dropped
created: YYYY-MM-DD
target: YYYY-MM-DD    # optional
area: "[[Area Name]]" # optional wikilink to parent area
tags: []
---
```

### Feature — `01 Projects/<Project>/<Feature Name>.md`

```yaml
---
type: feature
status: Unplanned     # Unplanned | Next | Doing | Waiting | Done
parent: "[[Project Name]]"
project: "[[Project Name]]"   # always set to the top-level project
created: YYYY-MM-DD
tags: []
---
```

Body of a Feature note holds:
- Description / context (like a Linear issue body)
- A `## Subtasks` section with inline `- [ ]` items (acceptance criteria, sub-steps)
- Notes added over time

### Idea NOTE — `00 Ideas/YYYY-MM-DD HHmm <slug>.md`

```yaml
---
type: idea
created: YYYY-MM-DD
---
```

Freeform unsorted thought. Triaged manually — gets promoted to a Feature in a project, converted to a Resource, or deleted. **Does NOT appear on Base Board** (only Features do). Distinct from a Feature with `status: Unplanned`, which is a ticket on the board that hasn't been thought through yet.

### Area — `02 Areas/<Area>/<Area>.md`

```yaml
---
type: area
status: active        # active | dormant
created: YYYY-MM-DD
tags: []
---
```

### Resource — `03 Resources/...`

```yaml
---
type: resource
created: YYYY-MM-DD
source: https://...   # optional
tags: []
---
```

### Daily — `Daily/YYYY-MM-DD.md`

```yaml
---
type: daily
date: YYYY-MM-DD
---
```

### Memory / decision / person — `05 Meta/...`

```yaml
---
type: memory
category: preferences # preferences | thread | person | decision
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

---

## 4. Naming conventions

- Title Case, no underscores or kebab-case. "Add 2FA.md", not "add-2fa.md".
- Daily notes: `YYYY-MM-DD.md`.
- Idea captures: `YYYY-MM-DD HHmm <slug>.md` so they sort chronologically.
- Decisions: `YYYY-MM-DD <slug>.md` in `05 Meta/Decisions/`.
- People: just the name — "Jane Doe.md".
- Feature names: concise, ticket-like — "Add 2FA", "Migrate sessions table", "Fix login redirect bug".

---

## 5. Wikilinks vs. tags

- Use `[[wikilinks]]` for entities (projects, features, people, areas).
- Use `#tags` for cross-cutting concerns (`#question`, `#idea`, `#follow-up`).
- Rule of thumb: if it's a *thing*, wikilink. If it's a *quality of a thing*, tag.
- Don't add tags or wikilinks that aren't justified — sparse beats noisy.

**Status is a frontmatter property on Features, NOT a tag.** There is no `#status/*` tag family in this vault. Anything that needs board placement uses the `status:` property in its frontmatter.

**Subtasks inside Features** are just `- [ ]` (not done) and `- [x]` (done). No tags. The Feature's status is the source of truth for board placement; subtasks are just rolled-up done/not-done items within it.

---

## 6. Session-start protocol (read before answering)

At the start of every Obsidian-related session, read in this order:

1. **All files in `05 Meta/Memory/`** — durable facts about the user, preferences, ongoing threads.
2. **The most recent file in `Daily/`** — what just happened.
3. **Any note in `01 Projects/**/` with `status: Active` modified in the last 7 days** — what's currently warm.

That's it. Do not read the whole vault. Do not read archived projects unless asked.

If the relevant context isn't in those files, ask the user rather than guessing.

---

## 7. During-session behavior

### Recognizing the underused sections (route proactively)

The schemas in §3 define *where* things go; these are the *tells* for spotting them mid-session. Don't wait to be told — when you notice one of these, **propose** the note (create it on a yes; never silently auto-file). The default for execution work stays Projects/Daily/Memory; this list is what pulls the rest of PARA into play.

| When you notice during a session… | → Section | Action |
|---|---|---|
| A reusable fact, pattern, or gotcha that outlives the current project; a how-to; an external reference worth keeping | `03 Resources/` | Propose a Resource note (`type: resource`); cross-link it from the project that surfaced it |
| A non-trivial choice made with alternatives weighed | `05 Meta/Decisions/` | Log a Decision note — *the rule already exists below but rarely fires; the session-end sweep is its enforcement* |
| A named collaborator, reviewer, or stakeholder mentioned substantively | `05 Meta/People/` | Propose a Person note on first substantive mention |
| An ongoing responsibility with no end date (not a project — no finish line) | `02 Areas/` | Propose an Area note |

**Anti-nag rule.** Surface inline only for high-signal cases (a real decision being made; a first substantive mention of a person). Everything else rolls up to the session-end sweep — don't interrupt execution flow to file reference material.

**Denormalize-then-promote is fine.** Capturing a gotcha inside a project hub during execution is the right fast path. The sweep is what later lifts cross-cutting items out into Resources/Decisions so they're reusable beyond the project.


### Captures
When the user says "remember/note/capture this" without specifying where:
- Create `00 Ideas/YYYY-MM-DD HHmm <slug>.md` with the Idea NOTE schema.
- Don't try to file directly to Projects / Areas / Resources / as a Feature unless the user explicitly says so.
- Captures-by-default rule (formerly Inbox-by-default) until the user changes it.

### Adding a Feature
When the user says "add a task," "I need to do X," "add this to project Y":
- Create `01 Projects/<Project>/<Feature Name>.md` with the Feature schema.
- Default `status: Unplanned` unless the user signals it's ready to do.
- Set `parent:` and `project:` wikilinks correctly.
- Body starts with a brief description; add a `## Subtasks` section if the user lists sub-items.

### Moving Features through the board
- the user drags cards in Base Board → Base Board edits the underlying `.md`'s `status:` automatically. No agent action needed.
- When the user says "move X to Doing" / "I'm starting on X" / "X is blocked": edit the Feature note's `status:` frontmatter property.

### Subtasks
Inside a Feature, just use `- [ ]` and `- [x]`. Obsidian renders them natively with click-to-complete. No status tags. Subtasks are not on the board.

### Working artifacts during a session
When the agent produces a document during a session that isn't a Feature ticket or a Decision note, place it in `01 Projects/<Project>/outputs/`. This includes synthesis docs, deep-dives, applied critiques, feature specs, draft material. If the artifact later matures into something canonical (a Feature, a Decision, a Resource), promote it then. The default home is `outputs/` so working content never accretes at the project root.

Supportive *research* (raw research, evidence quotes, competitor breakdowns, downloaded reference material) goes in `01 Projects/<Project>/findings/` instead — read §2 for the distinction.

### New projects
Create `01 Projects/<Name>/<Name>.md` with the project schema. The folder makes room for Feature notes, meeting notes, scratch work, etc.

### Memory updates
- Preferences → append/update `05 Meta/Memory/preferences.md`
- Ongoing threads → `05 Meta/Memory/threads.md`
- People → `05 Meta/People/<Name>.md`
- Decisions → new file at `05 Meta/Decisions/YYYY-MM-DD <slug>.md`

When updating an existing memory file, **prefer `Edit` over `Write`**. Surgical changes, not full rewrites — preserve the user's handwritten content.

### Daily log (auto-on)
At the **start** of any non-trivial session, append a timestamped block to today's daily note (`Daily/YYYY-MM-DD.md`, create if missing):

```markdown
## HH:MM — <one-line summary>
<2-4 bullets of what we did or are about to do>
```

At the **end** of the session (or when the user signals it's wrapping), append a brief outcome line under the same heading.

If a session is purely informational ("what's the capital of France"), skip the log — don't pollute it.

### Session-end promotion sweep (forcing function)

At the end of any non-trivial session (alongside the daily-log outcome line), run a quick sweep before wrapping:

1. Review what the session produced or learned — new gotchas, reusable patterns, decisions, people, reference material.
2. Surface anything currently living *only* inside a project that's actually cross-cutting:
   > "This session produced X, Y, Z. Want me to promote any — X looks like a Resource, Y like a Decision?"
3. On a yes, create the note (correct schema per §3) and cross-link it from the project. On a no, leave it.

This is the safety net for the recognition rules above: mid-flow noticing gets skipped, so the sweep is where the starved sections actually get fed. Promotion only — never silent bulk creation (see Hard Nos §8). Skip entirely for purely informational sessions.

### Decisions
When we decide something non-trivial during a session, log it as a decision note in `05 Meta/Decisions/` with:
- The decision itself
- Alternatives considered
- Why this won
- Date

---

## 7a. Boards (.base files)

Two kinds of boards, both Base Board–rendered:

**Active Work (vault-wide, helicopter view)** — `Active Work.base` at vault root.
- Filter: `type == "feature"` AND `!file.inFolder("05 Meta")` AND `!file.inFolder("04 Archive")`
- Columns: Unplanned → Next → Doing → Waiting → Done (matches the Feature status vocabulary).
- Shows every active Feature across every project, grouped by status. The `04 Archive` exclusion keeps archived projects' Features off the board.

**Per-project boards** — `01 Projects/<Project Name>/<Project Name>.base` alongside each project hub note.
- Filter: `type == "feature"` AND `project == "[[<Project Name>]]"`
- Shows only that project's Features. Used when working inside a single project.

Both use the same column scheme — Unplanned → Next → Doing → Waiting → Done — and the same group-by (`status`). Column order is set in the Base Board UI by dragging columns left/right.

Each card on any board is a Feature note. Drag a card → Base Board writes the new `status` value into the underlying Feature's frontmatter automatically. The same Feature appears on Active Work AND its project's board — same data, two lenses.

**The `05 Meta/` folder is excluded** from Active Work (via the filter) so documentation examples in conventions.md, the SKILL.md, the Feature template, etc. don't leak onto the board. Per-project boards filter on `project ==` so the exclusion isn't strictly needed there, but it's harmless to keep.

A `.base` is created for every new project by the **New Project** Templater driver (see §7c). For existing projects, the file can be hand-written from the `Project.base` template.

---

## 7b. Templates

the user uses the **Templater** community plugin (not the core Templates plugin) pointed at:

`<YOUR_VAULT_PATH>/05 Meta/System/Templates/`

Templater syntax: `<% tp.file.title %>` for the filename, `<% tp.date.now('YYYY-MM-DD') %>` for today's date, `<%* %>` for scripted blocks.

Available templates:

| Template | Use when the user is | Produces |
|---|---|---|
| `Idea` | Quickly noting an unsorted thought | `type: idea` note for `00 Ideas/` |
| `Project` | Starting a new project hub note (called automatically by `New Project` driver) | `type: project` hub note |
| `Project.base` | Project board config (called automatically by `New Project` driver) | `.base` file scoped to that project's Features |
| `New Project` | Scaffolding a whole new project in one step | Folder + hub note + `.base` file in `01 Projects/<Project Name>/` |
| `Feature` | Adding a ticket / unit of work to a project | `type: feature` card with Description + Subtasks sections |
| `Area` | Defining an ongoing responsibility | `type: area` note |
| `Resource` | Capturing reference material | `type: resource` note |
| `Decision` | Recording a decision and the reasoning | `type: memory, category: decision` |
| `Person` | First note about someone | `type: memory, category: person` |
| `Memory` | Other durable agent memory | Bare `type: memory` for the user to categorise |

**All `<%...%>` values in YAML frontmatter must be quoted** (e.g. `created: "<% tp.date.now('YYYY-MM-DD') %>"`) — unquoted `<% %>` can confuse YAML parsers and Obsidian's Properties UI.

**Agent contract:** when the agent creates notes programmatically (without going through Templater), it produces frontmatter with the *rendered* values (`created: 2026-05-26`, etc.) matching what these templates would produce. The templates are the visual reference for what each note type should look like.

---

## 7c. The New Project scaffold workflow

The `New Project.md` driver is a Templater script that creates a complete project in one invocation. the user's workflow:

1. From the Command Palette (`Cmd+P`): **Templater: Create new note from template**.
2. Pick `New Project` from the list.
3. **Only prompt: project name** (e.g., `Refactor Auth`).
4. The driver creates the full project structure and opens the hub note.

What gets scaffolded:

```
01 Projects/Refactor Auth/
  Refactor Auth.md         ← hub note (type: project, status: Active)
  Refactor Auth.base       ← project board (filter: project == [[Refactor Auth]])
  assets/                  ← empty subfolder for images/PDFs/attachments
  outputs/                 ← empty subfolder for session-produced markdown (drafts, scratch, agent dumps)
```

The driver always sets the following icons via the **obsidian-icon-folder** plugin — same scheme for every project, so the file tree reads consistently across projects:

| File / folder | Icon | Color |
|---|---|---|
| Hub note (`.md`) | `LiFilePenLine` | `#0dbd00` (green) |
| Board (`.base`) | `LiSquareKanban` | `#650094` (purple) |
| `assets/` folder | `LiImage` | `#cc0000` (red) |
| `outputs/` folder | `LiPencilRuler` | `#e8a317` (amber) |

All three icons go via the icon-folder plugin's `data.json` (uniform mechanism). The driver disables the plugin first (so it flushes in-memory state to disk), then writes the three entries on top, then re-enables (so the plugin reads the updated data.json and renders). Icons appear immediately without a vault restart. The hub note's frontmatter does NOT carry `icon:` or `iconColor:` fields — keeping the assignment in one place avoids drift between two sources of truth.

To change the icon scheme across all future projects, edit the `HUB_ICON`, `BOARD_ICON`, `ASSETS_ICON`, `OUTPUTS_ICON` constants at the top of `New Project.md`. To change icons on an existing project, use the icon-folder plugin's right-click → "Change icon" menu.

### What goes in `outputs/`?

Session-produced markdown that's part of the project's refinement process — anything from transient drafts and scratches up to substantial synthesis documents (feature specs, persona analyses, applied critiques, design system drafts). The defining quality is "made during a session" rather than "promoted to canonical project artifact." By convention:

- **No schema required.** Plain markdown. If you want a note to be queryable later, add `type: output` (or `type: resource` for more substantial pieces) in its frontmatter — not mandatory.
- **Filenames stay loose** for drafts (timestamped or topical, whatever fits) but use descriptive Title Case for substantial synthesis docs (e.g., `V1 Feature Spec.md`).
- **Excluded from the board** automatically because these notes don't have `type: feature`.
- **Excluded from session-start reads** because the protocol only looks for `type: project` and `type: feature`.

If a note in `outputs/` matures into something durable, promote it: turn it into a Feature, move it to Resources, or merge its content into the hub. But substantial synthesis docs that don't have a more specific canonical home (specs, manifestos, design decisions still being worked through) can stay in `outputs/` indefinitely. `outputs/` is the staging area — most things move on, but it's also the home for ongoing working material.

**Distinct from `findings/`.** A sibling folder, created on demand (not by the New Project driver). `findings/` holds *supportive reference research* — raw evidence, competitor breakdowns, downloaded materials — material that informs the spec or features but isn't itself part of the project's plan. The line: `outputs/` = "stuff you'd reference while building," `findings/` = "the research the stuff was built on top of."

**Important:** use `Templater: Create new note from template` (not `Open Insert Template modal`). The Insert command requires an active editor to insert into; the driver outputs nothing, so it errors with "No active editor, can't append templates." The Create-new-note command bypasses that.

**Recommended:** bind a hotkey directly to the `New Project` template via **Settings → Templater → Template Hotkeys**. With a hotkey assigned (e.g., `Cmd+Shift+N`), the driver runs in one keystroke without navigating the command palette. The template also becomes searchable as `Templater: New Project` in the palette.

If you want different icons per file (not the defaults above), set them manually after scaffolding via the icon-folder plugin's right-click → "Change icon" menu.

**Agent contract:** when the agent creates a new project programmatically, it should mirror this output: scaffold the same folder + two files, with the same content shape. The `New Project.md` script is the canonical reference for what "create a new project" produces.

---

## 8. Hard nos

- Don't read the whole vault on every session.
- Don't reorganize folders without asking.
- Don't archive without asking.
- Don't bulk-edit notes without showing a diff/summary first.
- Don't add plugins or change `.obsidian/` settings.
  - **Exception:** writes to `.obsidian/plugins/obsidian-icon-folder/data.json` are allowed *only* for assigning the documented project-scaffold icons (hub note `LiFilePenLine` #0dbd00, board `LiSquareKanban` #650094, `assets/` `LiImage` #cc0000, `outputs/` `LiPencilRuler` #e8a317, Feature `LiTicketCheck` #00aaff, and per-project root icons). Settings keys, packs config, rules, and unrelated entries remain off-limits. When writing while Obsidian is running, follow the disable→write→re-enable order from §7c — otherwise in-memory state flushes over the new entries.
- Don't `Write` over an existing note when `Edit` would do.
- Don't add `#status/*` tags — status is a frontmatter property on Features only.

---

## 9. Cross-client notes

The `obsidian-vault` skill is installed at the user level (`~/.claude/skills/obsidian-vault/`) so it works in both Cowork and Claude Code.

In Claude Code: the working directory is usually a code repo, not the vault. That's fine — use absolute paths (`<YOUR_VAULT_PATH>/...`) for all vault operations.

In Cowork: the vault is typically mounted as a connected folder. Use the file tools (Read/Write/Edit) directly.

The conventions in this doc are the single source of truth in either client. Edit this file from either client and both will pick up the change.
