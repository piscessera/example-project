# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository type

This is **not a software codebase** — it is an Obsidian vault used as a project documentation system. There is no source code, build system, linter, or test suite. All content is Markdown, and most existing content is written in Thai. There are no commands to build, lint, or test; work here consists of reading, creating, and cross-linking Markdown notes under `docs/`.

## Structure and workflow

`docs/` is organized as a numbered pipeline that mirrors a project's lifecycle, from requirements through to retrospective. Each stage's `index.md` explains its purpose (in Thai) and links forward/backward to adjacent stages via Obsidian `[[wikilink]]` syntax:

- `01-requirements/` — requirements, split into:
  - `01-spec/` — source-of-truth feature requirements, user stories, business rules, scope
  - `02-plan/` — roadmap, milestones/phases, priorities (derived from `01-spec`)
  - `03-task/` — concrete task breakdown with status/owner/deadline (derived from `02-plan`)
- `02-design/` — design work, split into:
  - `01-prototypes/` — UI/UX wireframes, mockups, user flow, design system basics
  - `02-technical/` — architecture, database schema, API/data contracts, tech choices
- `03-testing/` — testing, split into:
  - `01-test-plan/` — test cases, test data, scope, derived from spec + design
  - `02-test-result/` — actual pass/fail results and bugs found
- `04-retrospectives/` — end-of-phase/sprint/milestone retrospectives (what went well, what to improve, action items), sourced from test results and the log
- `05-log/` — chronological changelog / decision log / notable events, used as an evidence trail for retrospectives
- `00-archived/` — superseded or cancelled documents; **never delete docs from the vault — move them here instead** to preserve decision history

## Conventions to follow

- Each folder has an `index.md` describing its purpose and linking to related folders — read the relevant `index.md` before adding a new document to a folder, and update it if the folder's scope changes.
- Cross-link related documents using Obsidian wikilink syntax: `[[relative/path/index|Display Text]]`.
- Keep the upstream→downstream flow intact when adding content: spec → plan → task → design (prototype → technical) → test plan → test result → retrospective, with the log capturing decisions along the way. New documents should live in the stage they belong to and link back to the stage(s) that motivated them.
- Follow the existing language convention (Thai) for consistency unless the user directs otherwise.
- When a document becomes obsolete, move it into `00-archived/` rather than deleting it.
