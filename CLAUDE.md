# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code plugin that provides an expert Manim animation skill. When a user installs and triggers the skill, Claude loads `skills/manim/SKILL.md` as system context, gaining deep knowledge of both ManimGL and Manim Community APIs.

## File layout and purpose

- **`.claude-plugin/plugin.json`** — Plugin registry metadata: name, description, author, category, version. This is what the plugin marketplace reads.
- **`skills/manim/SKILL.md`** — The skill content itself. Everything between the YAML frontmatter `---` fences is metadata; everything after is the prompt injected into Claude's context when the skill fires.
- **`README.md`** — User-facing documentation shown on the plugin marketplace page.

## Skill activation

The frontmatter in `SKILL.md` defines the trigger conditions. The `description:` field (not `name:`) is what Claude Code reads to decide whether to activate the skill. Trigger phrases currently include: `manim`, `manimgl`, `manim community`, `3Blue1Brown`, `3Brown1Blue`, `3b1b`.

## Key editing considerations

**When updating `SKILL.md`:**
- The YAML frontmatter (`name`, `description`) controls activation — keep trigger phrases accurate.
- The body is a reference guide written for Claude, not end users. Prefer tables and concise code examples over prose.
- The two versions (ManimGL vs Community) diverge on: import path, CLI command, animation names (`ShowCreation` vs `Create`), camera control, and some class names (`Tex`/`TexText` vs `MathTex`/`Tex`). Any update must account for both.

**When updating `plugin.json`:**
- `description` is shown in plugin discovery UI — keep it under ~200 characters.
- `version` should be bumped on any user-visible change.

**When updating `README.md`:**
- This is the marketplace listing. The trigger phrases table and the "About the two versions" section should stay in sync with `SKILL.md` frontmatter.
