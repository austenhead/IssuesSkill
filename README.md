# IssuesSkill

A [Claude Code](https://claude.com/claude-code) skill for managing a project's `issues/` folder — a lightweight, repo-local bug tracker built on plain markdown files.

## Overview

Each project keeps an `issues/` folder containing zero-padded `NNNN.md` files (`0001.md`, `0002.md`, …) plus an `issues/Issues.md` config file. When you describe a bug to Claude — verbally, or by pasting a screenshot — Claude files it as a markdown file so the conversation can move on without the bug being lost. When you ask to update status, attach a screenshot, or summarize what's open, Claude works on the same files.

The skill lives entirely on the conventions: file format, status vocabulary, the rule that nothing gets closed without your explicit confirmation, and the macOS screenshot copy gotcha (filenames contain a U+202F narrow no-break space). It bundles the issue template and a self-contained project-local guide (`Issues.md`) so projects stay consistent across sessions.

## How it fits with the Mac app

The companion [Issues.app](https://github.com/brennanMKE/Issues) is a native macOS viewer that watches the `issues/` folder and renders the current state — swimlane, timeline, and list views, with status / module / platform filters. Markdown files are the source of truth; the Mac app re-reads them on every change.

The two projects are complementary and independent:

- **This skill** is how Claude Code (or a subagent) authors and edits the markdown.
- **Issues.app** is how you read and triage them.

You don't need the Mac app to use the skill, and you don't need the skill to use the Mac app — but together they cover both sides of the workflow.

## Installation

```sh
./install.sh
```

The installer symlinks the skill into `~/.claude/skills/` so it's available across all your Claude Code sessions. If you've already installed it, the script will offer to replace the existing version.

## Usage

Once installed, the skill triggers automatically when you describe a bug, ask to update an issue, or query what's open. You don't need to invoke it explicitly.

Examples of prompts that trigger it:

- *"the like button doesn't persist when I tap a post — file this"*
- *"mark 0046 as in progress, I'm starting on it"*
- *"what's still open in BlueskyFeed?"*
- *"set up issue tracking for this project"*

When you describe a bug for the first time in a project, Claude will create `issues/Issues.md` from the bundled template and then file the issue. From that point on, every issue lives as `issues/NNNN.md` with a metadata table (Status, Module, Platform, First seen) followed by Description, Steps to reproduce, etc.

Status values: `open` · `in-progress` · `resolved` · `closed` · `wontfix`. The `resolved` → `closed` distinction is deliberate — `resolved` says "work landed", `closed` says "user confirmed". Claude (and any subagent it spawns) will never set `closed` on its own.

## Project layout

```
IssuesSkill/
├── install.sh                       # symlinks the skill into ~/.claude/skills/
├── LICENSE                          # MIT
├── README.md                        # this file
└── issues/                          # the skill itself
    ├── SKILL.md                     # workflow + rules, always loaded by Claude
    ├── assets/
    │   ├── issue-template.md        # template for a new NNNN.md
    │   └── Issues-md-template.md    # template for a project's issues/Issues.md
    └── references/
        └── parsing.md               # Mac app regex details (debug-only)
```

## Maintaining the skill

When the workflow described in `issues/SKILL.md` changes, mirror the change in `issues/assets/Issues-md-template.md`. The two files intentionally duplicate the workflow content so projects can use `Issues.md` as a self-contained guide even when the skill isn't installed. Without that discipline, the project-local guide drifts from the skill and the two start contradicting each other.

The smaller files — `assets/issue-template.md` and `references/parsing.md` — are the canonical source for their respective concerns and don't need to be mirrored.

## License

[MIT](LICENSE) © Brennan Stehling
