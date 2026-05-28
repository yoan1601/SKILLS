# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

This is an **Agentic Workflows** project — a workspace for building, documenting, and organizing AI agent skills and workflows.

## Repository Structure

- `SKILLS/` — reusable skill definitions (currently empty; populate with skill markdown files)
- `.claude/skills/` — Claude Code project-level skill definitions invokable via `/skill-name`

## Skills

Skills live in `.claude/skills/<skill-name>.md`. Each file defines a slash command available within this project. The frontmatter declares the skill name and description; the body is the prompt Claude receives when the skill is invoked.

```
---
name: skill-name
description: One-line description shown in skill picker
---

Prompt body here.
```

Invoke a skill in Claude Code with `/<skill-name>`.

### Active Skills

| Skill | Command | Trigger phrases | Output |
|---|---|---|---|
| skill-builder | `/skill-builder` | "build a skill", "create a new skill", "audit this skill" | `.claude/skills/<name>/SKILL.md` |
| infographic-builder | `/infographic-builder [topic]` | "create an infographic about X", "make a visual explainer", "design an educational infographic" | `projects/infographic-builder/<topic>.png` |
| linkedin-content | `/linkedin-content [topic]` | "write a LinkedIn post about X", "create a carousel on Y", "suggest LinkedIn content ideas", "post about X" | `output/linkedin/YYYY-MM-DD-[slug].md` — no-topic mode pulls pending ideas from Notion Content Engine first |
