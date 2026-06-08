---
name: obsidian-vault
description: Use whenever the user wants to capture, recall, track, or organize anything in their Obsidian vault. Triggers on any mention of Obsidian, the vault, "my notes", projects, features, tasks, board, daily log, idea, or capture/recall verbs like "remember this", "note this", "what was I working on", "add a feature", "what's on my board". Also triggers when the user asks about ongoing projects, features, or decisions. ALSO triggers on recall questions about a specific named thing the user treats as familiar — e.g. "what can you tell me about X", "what's the status of X", "remind me about X" — where X is a project, product, person, side-project, or codename, not general world knowledge. If the user names something familiar-to-them that you don't recognize, check the vault before answering from training knowledge or searching Slack/web. Do NOT trigger for general-knowledge questions (e.g. "what is photosynthesis").
---

# Obsidian Vault Skill

> **Canonical source.** This file is the source-of-truth copy of the `obsidian-vault` SKILL.md. The actual installed copies live elsewhere (Cowork's plugin cache + `~/.claude/skills/obsidian-vault/`). If those ever get wiped, re-install from this file.
>
> **To re-install:**
> - Cowork: zip this file inside a folder named `obsidian-vault/` with `.skill` extension and use "Save skill", OR copy directly to Cowork's plugin cache.
> - Claude Code: `cp` this file to `~/.claude/skills/obsidian-vault/SKILL.md`.
>
> Edit this file when you want to change the skill itself (triggering description, session-start protocol, etc.). Edit `conventions.md` when you want to change how the agent behaves *inside* a session.

---

This skill governs how to interact with the user's Obsidian vault — for both capture (creating notes) and recall (reading notes to answer questions).

## Vault location

`<YOUR_VAULT_PATH>/`

In Cowork the vault is typically mounted as a connected folder. In Claude Code, use absolute paths from anywhere — the vault path is the same regardless of working directory.

## Source of truth — conventions.md

All behavioral rules — folder layout, frontmatter schemas, naming, capture rules, tag taxonomy, CardBoard config, hard nos — live in **one place**:

`<YOUR_VAULT_PATH>/05 Meta/System/conventions.md`

That file is a regular markdown note the user can edit at any time. It is authoritative. This skill deliberately does NOT duplicate any of its rules — duplication causes drift. Read conventions.md first, then act according to what it says.

## Session-start protocol (do this before answering)

When this skill activates, read in this exact order:

1. `05 Meta/System/conventions.md` — the rules.
2. Every file in `05 Meta/Memory/` — durable facts about the user, preferences, ongoing threads.
3. The most recent file in `Daily/` — what just happened.
4. Any note in `01 Projects/**/` with `type: project` AND `status: Active`, modified in the last 7 days — what projects are currently warm.
5. Any note in `01 Projects/**/` with `type: feature` AND `status` ∈ (`Doing`, `Next`), modified in the last 7 days — what tickets are actively being worked or queued next.

Do **not** read the whole vault. Do **not** read archived projects unless explicitly asked.

If relevant context isn't in those reads, ask the user rather than guess.

## When the conventions file is missing

If `05 Meta/System/conventions.md` doesn't exist (fresh install, vault not yet scaffolded, or wrong vault path), tell the user: "I don't see the conventions doc at the expected path. Should I scaffold a fresh vault here, point me at a different path, or proceed without conventions?" Then wait for an answer. Do not guess at conventions.
