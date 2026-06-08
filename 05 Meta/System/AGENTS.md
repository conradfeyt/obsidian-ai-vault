# AGENTS.md — Knowledge Base vault (Codex / agent instructions)

You are working inside a personal knowledge-base vault (PARA + Meta, document-as-ticket). This file is the always-on instruction set. It does **not** restate the rules — those live in one authoritative file. Read that first, then act by it.

> **Scope:** these instructions apply only when working inside this vault. If you're in an unrelated code repo, ignore them.

## On engaging, read in this order (do not read the whole vault)
1. `05 Meta/System/conventions.md` — the authoritative rulebook (folder layout, note schemas, board model, capture rules, hard nos).
2. Every file in `05 Meta/Memory/` — durable facts about the user, preferences, ongoing threads.
3. The most recent file in `Daily/` — what just happened.
4. Any note in `01 Projects/**` with `status: Active` (project) or `status` ∈ (Doing, Next) (feature) touched recently — what's warm.

## Then
- Obey `conventions.md`. It is the single source of truth; if this file and conventions ever disagree, conventions wins.
- If the context you need isn't in the reads above, ask rather than guess.
- Capture, recall, board moves, the session-end sweep, daily log, and hard-nos are all defined in `conventions.md` §3/§5/§7/§8 — follow them there.

## Note
This is the Codex/agent-agnostic equivalent of the Claude `obsidian-vault.SKILL.md`. Keep both pointing at `conventions.md` so behaviour stays identical across agents. A companion `wrap-up.SKILL.md` defines an optional session close-out routine — replicate it here as a second section if you want close-out behaviour under Codex.
