# Contributing to IssuesSkill

Thanks for taking the time to look here. This project is a Claude Code skill — most of its surface area is plain markdown that Claude reads at runtime — so contributing is approachable even if you've never written a skill before.

There are two main ways to help: **filing an issue** when something looks wrong, and **opening a pull request** when you have a fix or improvement in mind.

## Filing an issue on GitHub

The simplest contribution is a clear bug report or feature request on the project's [GitHub Issues page](https://github.com/brennanMKE/IssuesSkill/issues). Before you file:

1. **Search existing issues** to make sure it isn't already tracked — both open and closed.
2. **Pick the smallest scope** that captures the problem. One bug per issue. If you've found three things, file three issues.

When filing, please include:

- **What you expected to happen** and **what actually happened**. Concrete examples beat abstract descriptions.
- **Steps to reproduce**, if it's a bug. Include the prompt you sent Claude when relevant — the skill is triggered by phrasing, so the exact words matter.
- **Versions**: macOS version, Claude Code version, and (if relevant) the model in use.
- **Logs or screenshots** if they help. Paste them inline or attach them.
- **Whether you'd like to send a PR**, if you'd like to take a stab at it. We'll often leave the issue with you while you work — no need to ask.

For feature requests and design questions, the same template works. Lead with the problem you're trying to solve before proposing a specific solution — sometimes there's a simpler path.

## Opening a pull request

PRs are welcome for fixes, doc improvements, new references, and behavior changes. The flow is the standard GitHub flow:

1. **Fork** the repo and create a topic branch off `main`. Branch names like `fix-em-dash-parser` or `add-video-reference` are fine.
2. **Make focused changes.** One logical change per PR. If you're tempted to bundle two unrelated improvements, split them — it's easier to review and easier to revert if needed.
3. **Test the change** by installing the skill locally (`./install.sh`) and running a Claude Code session against a project with an `issues/` folder. Try the workflow your change affects — file an issue, resolve one, attach a screenshot, generate a status report, whatever fits.
4. **Open the PR** against `main` with:
   - A short, declarative title (e.g. `Fix em-dash detection in title regex`).
   - A summary of what changed and why. If it fixes an existing issue, link it (`Fixes #42`).
   - Notes on anything you considered and rejected, so reviewers don't have to re-derive them.

If your change is large or involves a workflow shift (new status value, new commit convention, new bundled file type), it's worth opening an issue first to discuss the shape before writing the code. Saves both of us time.

### What changes typically look like

Most PRs touch one of three areas:

- **`issues/SKILL.md`** — the always-loaded workflow doc. Edits here change how Claude behaves whenever the skill triggers.
- **`issues/assets/`** — templates copied into projects. Changes here affect new issues going forward, but don't retroactively apply to issues that already exist.
- **`issues/references/`** — load-on-demand documentation. New references go here when a topic is too narrow for SKILL.md but worth capturing.

When you change `SKILL.md`, mirror the change into `assets/Issues-md-template.md` if it affects the workflow that projects need to know about — the project-local guide is intentionally self-contained so it works even when the skill isn't installed. The README has more on this discipline.

### Style and tone

The skill's docs lean toward plain prose with concrete examples and a short *why* for any rule that isn't obvious. Avoid heavy use of `MUST` / `NEVER` / all-caps imperatives unless the constraint really is absolute (e.g. the never-auto-close rule). When you're tempted to write a blunt command, try explaining the reasoning first — Claude follows reasoning more reliably than rules without it.

Match the existing voice when you can. If you spot inconsistencies in voice between sections, calling them out (or fixing them) is welcome.

### Commit messages

Use short, declarative subject lines that describe what changes ("Add video-attachment reference", "Fix relative-path example in template"). Squash WIP commits before opening the PR if it makes the history easier to read — but don't worry about polishing every commit message; the PR title and description are what reviewers focus on.

## Code of conduct

Be kind, be specific, and assume good faith. Disagreements on technical approach are fine and welcome — disagreements on tone aren't. If a thread feels like it's getting heated, step back and come back to it later.

## License

By contributing, you agree that your contributions will be licensed under the project's [MIT License](LICENSE).
