# skills

My personal **Agent Skills** library — one git repo that plugs into both
**Claude Code** and **claude.ai**.

> 个人 skill 仓库。一处编写,两处使用:`bin/sync` 软链到 Claude Code,`bin/package`
> 打包上传 claude.ai。改内容即时生效;新增 / 改名 / 删除后再跑一次 `bin/sync`。
> `wip/` 是草稿区,不会被同步。

[Quickstart](#quickstart) · [What's a skill?](#whats-a-skill) · [Layout](#layout) · [Commands](#commands) · [Claude Code](#use-in-claude-code) · [claude.ai](#use-in-claudeai) · [Git](#git-workflow)

---

## Quickstart

```bash
bin/new personal hello              # 1. scaffold personal/hello/SKILL.md from the template
$EDITOR personal/hello/SKILL.md     # 2. write the skill
bin/sync                            # 3a. use it in Claude Code (symlinks into ~/.claude/skills/)
bin/package personal/hello          # 3b. …or zip it for a claude.ai upload
```

That's the whole loop. Details below.

---

## What's a skill?

A **skill** is a folder with a `SKILL.md` file. Claude reads the frontmatter to
decide *when* to use it, then follows the body as instructions.

```
personal/hello/
├── SKILL.md          # required — frontmatter + instructions
├── reference.md      # optional — extra docs Claude reads on demand
└── scripts/          # optional — helper code the skill can run
```

`SKILL.md`:

```markdown
---
name: hello
description: What the skill does and WHEN to use it. This single line is all Claude
  sees when deciding whether to invoke the skill — lead with concrete triggers.
---

# hello

Instructions Claude follows when the skill is active...
```

| Field | Required | Notes |
|-------|----------|-------|
| `name` | ✅ | Lowercase letters, digits, hyphens. ≤ 64 chars. **Must match the folder name.** |
| `description` | ✅ | The trigger. ≤ 1024 chars. The one thing Claude sees up front — be specific. |
| `allowed-tools` | — | Optional (Claude Code only): restrict which tools the skill may use. |

> **Progressive disclosure** — keep `SKILL.md` short. Put long material in sibling
> files and tell Claude to read them when needed; it only loads them on demand.

---

## Layout

| Folder | Purpose | Synced to Claude Code? |
|--------|---------|:----------------------:|
| `engineering/` | Coding / dev-workflow skills | ✅ |
| `personal/` | Everything else | ✅ |
| `wip/` | Drafts & scratch — not ready to use | ❌ (kept private) |
| `templates/` | Starter template for `bin/new` | ❌ |
| `bin/` | The three helper scripts | — |
| `dist/` | Generated zips (git-ignored) | — |

Move a skill out of `wip/` into `engineering/` or `personal/` when it's ready, then
run `bin/sync`.

---

## Commands

| Command | What it does |
|---------|--------------|
| `bin/new <category> <name>` | Scaffold `<category>/<name>/SKILL.md` from the template. Validates the category and name. |
| `bin/sync` | Symlink every skill in `engineering/` + `personal/` into `~/.claude/skills/`, and prune stale links. Skips `wip/`. |
| `bin/package <category>/<name>` | Zip the skill into `dist/<name>.zip`, ready to upload to claude.ai. |

---

## Use in Claude Code

```bash
bin/sync
```

Symlinks each live skill into `~/.claude/skills/<name>`. Because they're **symlinks**:

- **Editing a skill's content is instant** — no re-sync needed.
- **Re-run `bin/sync` only after adding, renaming, or deleting** a skill. It also
  prunes links whose target you removed.
- `wip/` is never synced.

Claude then picks the skill up automatically by its `description`, or you can invoke
it by name.

**Safe by design:** `bin/sync` never overwrites a real directory in
`~/.claude/skills/`. If a name already exists as a real folder (e.g. one of your
gstack skills), it's skipped with a warning — the script only ever manages the
symlinks it created. Retarget it with:

```bash
CLAUDE_SKILLS_DIR=/some/other/path bin/sync
```

(Point `CLAUDE_SKILLS_DIR` at a project's `.claude/skills/` to expose skills
project-scoped instead of globally.)

---

## Use in claude.ai

```bash
bin/package personal/hello          # → dist/hello.zip
```

Upload the zip at **claude.ai → Settings → Capabilities → Skills → Upload skill**.
The archive contains `hello/SKILL.md` (plus any sibling files) — the structure
claude.ai expects.

Unlike Claude Code's symlinks, claude.ai keeps its own copy, so re-package and
re-upload whenever you change the skill.

---

## Git workflow

Everything except `dist/` and local cruft is plain text, so skills diff and review
like code.

```bash
git add personal/hello
git commit -m "add hello skill"
git push
```

Git-ignored: `dist/`, `.DS_Store`, editor folders, `.claude/settings.local.json`.

Remote: `git@github.com:Lei-Duan/skills.git`
