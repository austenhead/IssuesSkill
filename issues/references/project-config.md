# Project config (`issues/project.json`)

A small JSON file at `issues/project.json` that names the project and links to its repo. This is the **canonical source** for the project's identity — replacing earlier reliance on the parent folder name as an implicit signal.

## Why this file exists

Two reasons:

1. **The folder name isn't always the project name.** A repo cloned to `~/work/oss/IssuesSkill-fork` shouldn't show up as "IssuesSkill-fork" in the Mac app or in commit messages. The folder is incidental; the name in `project.json` is intentional.
2. **Issues need a way to reach the outside.** When generating PR descriptions, status reports, or external bug references, the skill needs a URL pointing at the project's home. Inferring it every time from `git remote -v` is fine but redundant — recording it once means downstream consumers (Issues.app, future tooling) don't have to re-derive it.

## Schema

```json
{
  "name": "IssuesSkill",
  "url": "https://github.com/brennanMKE/IssuesSkill"
}
```

Two required fields:

- **`name`** — the project's human-readable name. Use the form a human would write in prose ("IssuesSkill", "Bluesky for SwiftUI"), not the slug ("issues-skill", "bluesky-swiftui").
- **`url`** — the project's canonical web URL. Typically a GitHub HTTPS URL, but anything pointing at the project's source-of-truth works (GitLab, Bitbucket, Gitea, etc.). Use the **HTTPS** form (`https://github.com/user/repo`), not the SSH form (`git@github.com:user/repo.git`).

Don't add other fields casually. If a future need arises (default module, build command, etc.), prefer adding it where it already lives — `Issues.md` for human-readable workflow, `CLAUDE.md` for code conventions — rather than expanding `project.json` into a kitchen sink.

## When to create the file

Create `issues/project.json` whenever you create `issues/Issues.md`. The two are siblings and the project setup isn't complete without both.

If you walk into a project that has `issues/Issues.md` but no `project.json`, create it on the next operation that touches the issues folder. It's a small, one-time cost.

## How to populate the fields

### `name`

In order of preference:

1. Ask the user, if you're already in conversation with them about setting up issue tracking.
2. Read the README's `# Heading` (the first H1). README headings are usually the project's display name.
3. Fall back to the folder basename, but flag it: tell the user "I used the folder name `<basename>` as the project name — change `issues/project.json` if that's wrong."

### `url`

Read it from the git remote:

```bash
git remote get-url origin
```

If the remote is in SSH form (`git@github.com:brennanMKE/IssuesSkill.git`), convert to HTTPS:

```
git@github.com:brennanMKE/IssuesSkill.git
→ https://github.com/brennanMKE/IssuesSkill
```

Steps: drop the `git@`, replace `:` between host and path with `/`, prefix `https://`, drop the trailing `.git`.

If the remote isn't set, or there's no git repo, set `"url": ""` (empty string) and note it in the user-visible confirmation. Don't omit the field — keeping it present keeps the JSON shape stable for consumers.

### Worked example

```bash
# In a repo where origin = git@github.com:brennanMKE/IssuesSkill.git
git remote get-url origin
# → git@github.com:brennanMKE/IssuesSkill.git
```

Resulting `issues/project.json`:

```json
{
  "name": "IssuesSkill",
  "url": "https://github.com/brennanMKE/IssuesSkill"
}
```

## Reading the file

Before any non-trivial issue operation (filing, querying, generating a status report, dispatching a subagent), read `project.json` if it exists. Treat it as authoritative for the project's name and URL — don't second-guess from the folder path.

If `project.json` is missing, create it (using the population rules above) before continuing. This is a one-time setup cost; once it exists, every subsequent operation is fast.

## Updating the file

Edits are rare but valid:

- The repo moves to a new URL (org rename, fork promotion).
- The project's display name changes intentionally.

When this happens, edit `project.json` directly. The Mac app picks up the change on the next folder scan. Don't touch this file as a side-effect of routine issue work.

## Git tracking

`project.json` follows the same git rules as the rest of `issues/`:

- **`issues/` tracked**: commit it. The first commit creating the file uses message `Add project config` (or bundle it with the initial `Issues.md` commit if both are new). Subsequent edits use a descriptive message like `Update project URL` or `Rename project`.
- **`issues/` ignored or no repo**: write the file; no commit.

## Anti-patterns

- **Don't infer the project name from the folder path** when `project.json` exists. The whole point of this file is to avoid that ambiguity.
- **Don't store secrets in `project.json`.** It's plain text checked into the repo (when issues/ is tracked). Anything sensitive belongs in environment variables or a separate ignored file.
- **Don't expand the schema unilaterally.** New fields should land via a deliberate change to this reference, not by an agent guessing what extra metadata might be useful.
- **Don't keep an SSH URL in the `url` field.** SSH URLs aren't openable in a browser; HTTPS is the form humans and tooling expect.
