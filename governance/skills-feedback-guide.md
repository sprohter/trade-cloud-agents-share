---
deprecated: true
migrated_to: governance/skills-handbook.md
migrated_at: 2026-04-20
note: 低频使用，治理层合并后不再独立维护。核心原则已纳入 skills-handbook.md
---

# Skills Feedback Guide

> ⚠️ 已 deprecated（2026-04-20）。核心原则已纳入 `governance/skills-handbook.md`，本文件保留以防旧引用，不再维护。

Use this guide to capture lightweight feedback from real skill usage without over-designing the process.

## Goal

Record only the information that helps answer these questions later:

- Which skills are actually being used
- Which skills save time
- Which skills are too empty
- Which skills are too rigid
- Which repeated work is mature enough to become a new skill

## Principles

1. Record feedback only after real usage.
2. Prefer short notes over complete reports.
3. Focus on friction, missing guidance, and repeated value.
4. Do not create a new skill from one isolated case.
5. Review patterns after a batch of real cases, not after every single one.

## When To Record

Record feedback when at least one of these is true:

- A skill clearly helped finish the task faster
- A skill was triggered but did not help much
- The same missing guidance appeared again
- The workflow felt too detailed or too restrictive
- A repeated task looks stable enough to split into a new skill later

## What To Record

Keep each entry short and practical:

- Date
- Skill name
- Task summary
- Result: helpful / partly helpful / not helpful
- What worked
- What was missing
- Whether to update now, observe more, or ignore

## Decision Rules

Use these rules when reviewing accumulated feedback:

- `Observe more`: one-off issue or weak pattern
- `Update existing skill`: repeated gap inside an existing skill
- `Create new skill candidate`: repeated workflow with stable inputs and outputs
- `Do nothing`: low-value or too rare

## Suggested Review Rhythm

- Quick capture: after a meaningful task
- Light review: every 2 to 4 weeks
- Structural change: only after multiple similar cases

## Storage

- Template: [skills-feedback-entry-template.md](/<workspace-root>/.agents/templates/skills-feedback-entry-template.md)
- Recommended log file: `<workspace-root>\.kiro\temp\skills-feedback-log.md`

If the log file does not exist yet, create it from the template or copy one entry at a time.
