---
name: wrap-up
description: Use when the user signals a work session is ending and wants it captured/closed out — triggers on "wrap up", "wrap-up", "close out", "we're done here", "finish up", "end of session", "let's call it", and on end-of-day sign-offs like "good night", "goodnight", "night", "calling it a day", "signing off", "logging off", "that's me for today", or any request to "update my notes / docs / threads" at the end of a stretch of work. Runs the session close-out: daily-log outcome, promotion sweep, project/feature/thread updates, a git/PR reality-check against the notes, and a loose-ends chase. Companion to the obsidian-vault skill — same vault, same conventions, different entry point (close-out vs capture/recall). Do NOT trigger mid-task or for one-off note edits.
---

# Wrap-Up Skill

> **Canonical source.** This file is the source-of-truth copy of the `wrap-up` SKILL.md. Installed copies live at `~/.claude/skills/wrap-up/SKILL.md` (Claude Code) and Cowork's plugin cache. If those get wiped, re-install from here.
>
> **To re-install:**
> - Claude Code: `cp` this file to `~/.claude/skills/wrap-up/SKILL.md`.
> - Cowork: zip inside a folder named `wrap-up/` with `.skill` extension and "Save skill", or copy to the plugin cache.
>
> Edit this file to change the skill. The *rules* it follows live in `conventions.md` — this skill points at them, it does not copy them (duplication causes drift, same as the obsidian-vault skill).

---

## What this is

The explicit, on-demand version of the session-end sweep that `conventions.md §7` describes as automatic but that gets skipped in practice. Invoke it (`/wrap-up`, or just "let's wrap up") to close out a working session: record what happened, reconcile the notes with reality, and chase loose ends — so nothing has to be spelled out each time.

**Scope = full session close-out.** Not just vault hygiene: it also reconciles notes against actual git/PR state and chases loose ends (unpushed branches, PRs missing bodies, stale task chips).

**This skill records and reconciles. It does not start new work** — it won't deploy, merge, or write code. Pushing a branch / opening a PR counts as a loose-end it may *propose*, never do unprompted (see Autonomy).

## First: read the rules

Before acting, read `05 Meta/System/conventions.md` — specifically **§7 (during-session behavior)**: the daily-log format, the promotion-sweep forcing function, the captures/feature/decision rules, and **§8 Hard nos**. Those are authoritative. This skill is the running order; conventions is the rulebook.

Also recall what the session actually did — scroll the conversation, and note which **repos** were touched (cwd may be one of several; this session can span manhattan / bash-core-flutter / bash-nest-js, etc.).

## Triage gate (run first — especially on sign-offs)

Before doing anything, decide whether there's actually a session to close out.

**Was there anything worth capturing?** — and read this broadly, not just as "did we do tasks." It counts if any of these happened: files/code/notes touched, commits/branches/PRs, a decision made, research produced, a problem solved — **but also** a person mentioned substantively, an org/relationship/role detail, a new area of responsibility, a durable preference or working-style signal, a personal/life fact, a useful reference. Scroll the conversation and judge against *all* of it.

- **No trackable work** (pure conversation, Q&A, a quick lookup): **do not run the sequence.** Just acknowledge the sign-off briefly (e.g. "Night — nothing to record today.") and stop. Don't pollute the daily log (conventions §7: skip the log for purely informational sessions).
- **Trackable work happened**: run the full close-out below.

**Trigger sensitivity:**
- **Sign-off triggers** ("good night", "night", "calling it a day", "signing off", …) — gate on the above. A sleepy "good night" after an idle session should *not* spin up the machinery.
- **Explicit invocation** ("/wrap-up", "close out", "update my notes") — the user is asking on purpose, so run it; if there's genuinely nothing to record, say so rather than inventing entries.

When borderline, prefer a one-line "nothing substantive to log — want me to anyway?" over auto-running.

## Autonomy: auto-apply routine, propose the rest

**Auto-apply** (low-judgement, factual — just do it, then report):
- Daily-log outcome line / session block under today's `Daily/YYYY-MM-DD.md` (create if missing).
- Worklog bullets in warm project hubs.
- Feature-note `status:` changes the user **explicitly made or confirmed** this session.
- Adding established facts to notes: PR links, "branch pushed", commit SHAs, ticket/Linear/Notion links surfaced this session.
- `threads.md` updates for a thread that **demonstrably** advanced or resolved.

**Propose first, apply on yes** (judgement calls — list them, wait):
- Any **new** note (Resource / Decision / Person / Area) — the promotion sweep.
- Marking a feature **Done** when the user didn't say so (a Done call is the user's).
- Archiving anything.
- Writes to the separate `~/.claude/.../memory/` store (durable preferences/feedback).
- Acting on a loose end (push a branch, open/fill a PR) — surface it, don't do it.

When unsure which bucket: treat it as propose.

## The close-out sequence

1. **Daily log** — append/extend today's note: a `## HH:MM — <summary>` block with 2–4 bullets of what the session did, then an **Outcome** line. (auto)

2. **Project + feature notes** — for each warm project touched: add worklog bullet(s) to the hub; update feature-note statuses the user confirmed; thread in PR/branch/ticket links. (auto for confirmed facts)

3. **Threads** — update `05 Meta/Memory/threads.md` if an ongoing thread moved or closed. (auto if demonstrable)

4. **Reality-check (the differentiator)** — reconcile notes against actual state, per touched repo:
   - `git` — does the branch the notes claim exist / is it pushed? `git branch -r --contains <sha>`, `git ls-remote --heads origin '<pattern>'`. Verify current branch, don't trust a stale snapshot.
   - `gh pr view <n>` / `gh search prs --author=@me --state=open` — do the PRs the notes mention exist, have bodies, match the recorded numbers?
   - Where a note disagrees with reality, **fix the note** to match (auto) and call out the discrepancy in the summary.

5. **Capture sweep — not just work.** This is the step that catches everything that *isn't* a todo or project update. Don't tunnel on the code/tickets; deliberately re-read the session for incidental signal across **every** PARA + Meta bucket, and **propose** the right home for each (never auto-file — conventions §7 + §8):
   - **People** (`05 Meta/People/`) — anyone mentioned substantively: a colleague, reviewer, stakeholder, their role/team/reporting line; a personal relationship or life detail about someone. First substantive mention → propose a Person note (or an update to an existing one).
   - **Areas** (`02 Areas/`) — an ongoing responsibility with no finish line that surfaced (a team, a domain you own, a recurring duty).
   - **Decisions** (`05 Meta/Decisions/`) — a non-trivial choice made with alternatives weighed.
   - **Resources** (`03 Resources/`) — a reusable gotcha, pattern, how-to, or external reference worth keeping.
   - **Personal / life facts** — not everything is work. Preferences, plans, relationships, context about the user's life — route to the right place (a Person note, an Area, or memory; see step 7).
   - When something spans buckets or has no obvious home, propose the best fit and say why. The point of this step: *assume there's non-work signal and go looking for it*, rather than only logging what was on the board.

6. **Loose-ends chase** — surface, don't action: unpushed branches, committed-but-no-PR, PRs with empty bodies, stale/contradicted task chips, TODOs you confirmed real, follow-ups the user named but didn't ticket. List them as a short "open / next" set; offer to action on a yes.

7. **Memory check** — if a durable fact about the user surfaced (a preference, working-style feedback, who he is, an ongoing constraint), **propose** capturing it. Two stores — route deliberately:
   - **`~/.claude/.../memory/`** (cross-session agent memory) — how to work with the user, his preferences/feedback, durable project constraints. Distinct from the vault.
   - **The vault** — when the fact is really a *person* (→ Person note), an *area* (→ Area note), or a *decision* (→ Decision note), it belongs there instead. A fact can warrant both (e.g. a colleague's role → Person note, plus a memory pointer if it changes how you'd act).
   Don't duplicate what the repo / CLAUDE.md / git / an existing note already records — update in place rather than creating a near-duplicate.

## Output — one close-out summary

End with a compact report, not a wall of text:
- **Recorded** (auto-applied): bullet list of notes written/updated, as clickable links.
- **Discrepancies fixed**: any note-vs-reality mismatches reconciled.
- **Proposed** (awaiting yes): promotions, Done calls, archives, memory writes.
- **Open / next**: the loose-ends set + the single most useful next action.

Match the user's doc-style preference: skimmable, links over prose, no narration of steps that produced nothing.

## Hard nos

- Don't duplicate `conventions.md` rules here — reference §7/§8.
- Don't create notes, archive, or mark a feature Done without proposing first.
- Don't push/merge/deploy or write code — this skill closes out, it doesn't open new work.
- Don't pollute the daily log for a purely informational session — skip the log if nothing was done.
- Don't `Write` over a note when `Edit` would do (conventions §8).
