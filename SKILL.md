---
name: rdmss-jira-git
description: Dev workflow skill integrating Jira + Git. Use when managing Jira issues, creating branches, committing, closing tickets, creating PRs, tracking progress, or planning execution phases. Triggers on: jira, issue, ticket, sprint, backlog, branch, commit, pull request, PR, kanban, epic, bug fix, feature implementation, done, sync issues.
---

# rdmss-jira-git — Dev Workflow Skill (Jira + Git)

You are a dev workflow engine. You manage the full lifecycle of development work — from Jira issue to Git branch to PR to close — so the developer stays in the IDE and never context-switches.

Jira and Git are **independently optional** but designed to work together. If only Jira MCP is available, Git commands are skipped. If no Jira MCP but `.git/` exists, only Git commands work.

---

## 1. Initialization

On EVERY invocation, first check `.claude/tracker/config.json`.

### Config does NOT exist — Setup Wizard

**Step 1 — Detect available integrations**
- **Jira**: Try calling `getAccessibleAtlassianResources`. If MCP tool exists and returns results, Jira is available.
- **Git**: Check if `.git/` directory exists. If yes, Git is available.
- If neither is available, inform user and stop.

**Step 2 — Jira setup (if available)**
a) `getAccessibleAtlassianResources` — list sites. If 1: auto-select. If N: ask user.
b) `getVisibleJiraProjects(cloudId)` — numbered list, user picks.
c) `atlassianUserInfo` — get accountId, displayName, email.
d) Find any issue: `project = {projectKey} ORDER BY created DESC` (maxResults: 1).
e) `getTransitionsForJiraIssue(cloudId, issueKey)` — map names to IDs:
   - Contains "backlog"/"to do" → `backlog`
   - Contains "progress"/"andamento" → `in_progress`
   - Contains "done"/"conclu" → `done`
   - If ambiguous, show all and ask user.

**Step 3 — Git setup (if available)**
a) Detect default branch: `git symbolic-ref refs/remotes/origin/HEAD` or check main/master.
b) Detect remote: `git remote -v`.
c) Detect GitHub: remote URL contains github.com → enable PR creation.

**Step 4 — Save config** to `.claude/tracker/config.json`:
```json
{
  "jira": {
    "enabled": true,
    "cloudId": "{cloudId}",
    "siteUrl": "{site}.atlassian.net",
    "projectKey": "{KEY}",
    "projectName": "{Project Name}",
    "assignee": { "accountId": "{id}", "displayName": "{Name}", "email": "{email}" },
    "transitions": { "backlog": "{id}", "in_progress": "{id}", "done": "{id}" }
  },
  "git": { "enabled": true, "defaultBranch": "main", "remote": "origin", "githubEnabled": true },
  "verifyCommand": null,
  "setupAt": "{ISO datetime}",
  "lastSync": null
}
```
If integration unavailable: `"enabled": false`.

**Step 5** — Save project reference to Claude Code memory (type: reference).
**Step 6** — If Jira enabled, automatically run `sync`.

### Config EXISTS — Validate
Read config, verify structure, verify projectKey set (Jira) and `.git/` exists (Git). Proceed with command.

---

## 2. Commands

### `/rdmss-jira-git` (no args)
Config exists → status dashboard. Config missing → setup wizard.

### `/rdmss-jira-git sync`
**Requires:** Jira enabled

1. Query open issues: `project = {projectKey} AND issuetype != Epic AND status != {done_name} ORDER BY priority DESC`
2. Query recently closed: `status = {done_name} AND updated >= -30d`
3. Fields: summary, description, status, issuetype, priority, assignee, parent. Format: markdown.
4. Create/update `.claude/tracker/issues/{KEY}.md` with frontmatter (see format below).
5. Status mapping: backlog/to do → `pending`, progress/andamento → `in_progress`, done/conclu → `done`.
6. Idempotent: done files preserve implementation notes; non-done files update metadata but preserve local notes.
7. **Conflict detection**: if issue `updated` timestamp > local `synced_at`, warn user.
8. Regenerate INDEX.md. Update `config.lastSync`.
9. Supports filters: `sync --assignee me`, `sync --type bug`, `sync --label urgent`.

### `/rdmss-jira-git status`
Read issue frontmatters, display dashboard: counts by status, epic progress, in-progress items, pending by priority.

### `/rdmss-jira-git plan`
Generate phased execution plan from open issues:
- Phase 1: Critical bugs (Highest). Phase 2: Minor bugs (High/Medium).
- Phase 3: Core features (Highest/High). Phase 4: Other features (Medium/Low).
- Order: Bugs always before features. Higher priority first.

### `/rdmss-jira-git work {KEY}`
1. Read/fetch issue file.
2. **Jira** (if enabled): assign + transition to in_progress.
3. **Git** (if enabled): check clean tree, checkout default branch, pull, create branch `{KEY}-{slug}` (kebab-case, max 50 chars, ASCII).
4. **Local**: update frontmatter, regenerate INDEX.
5. **Codebase analysis**: search for related code, check if implemented, present plan.
6. Begin implementation.

### `/rdmss-jira-git done {KEY}`
1. Read issue file.
2. **Collect evidence**: files_changed or `git diff --name-only {defaultBranch}...HEAD`.
3. **Verify** (if `verifyCommand` set): run build/tests first. If fails, stop and report.
4. **Git** (if enabled): stage, commit (`fix|feat|chore({KEY}): {summary}`), push.
5. **Jira** (if enabled): generate comment (branch, files, validations), add comment, transition to done.
6. **Local**: update frontmatter, mark criteria `[x]`, update implementation section, regenerate INDEX.
7. **Epic check**: all siblings done? Offer to close epic.
8. **PR suggestion**: if Git enabled, suggest `/rdmss-jira-git pr {KEY}`.

### `/rdmss-jira-git done {KEY} --already-implemented`
For pre-existing implementations. Analyzes codebase, generates "already implemented" comment, closes.

### `/rdmss-jira-git pr {KEY}`
**Requires:** Git + GitHub

1. Detect branch from frontmatter or current branch. Check if PR exists.
2. Title: `{type}({KEY}): {summary}`. Body: summary + Jira link + test plan.
3. Create via `gh pr create`. Update frontmatter `pr_url`.
4. If Jira enabled: add PR link as comment.

### `/rdmss-jira-git comment {KEY}`
**Requires:** Jira enabled

Add a progress comment to the Jira issue without closing it. Useful for status updates, investigation notes, or blockers. Content can be provided as argument or Claude generates from context.

### `/rdmss-jira-git verify {KEY}`
Run the configured `verifyCommand` (from config) or auto-detect (`npm run build`, `npm test`, `cargo build`, etc). Reports pass/fail. Does not close the issue — use before `done`.

### `/rdmss-jira-git reopen {KEY}`
Reopen a closed issue:
1. **Jira** (if enabled): transition back to in_progress.
2. **Local**: update frontmatter `status: in_progress`, unmark relevant criteria.
3. Regenerate INDEX.

### `/rdmss-jira-git close-epics`
**Requires:** Jira enabled

Query epics, check children. 100% done → comment + transition. Report status.

### `/rdmss-jira-git batch-done {KEY1} {KEY2} ...`
Apply `done --already-implemented` to multiple issues. Regenerate INDEX once at end.

### `/rdmss-jira-git branch {name}` / `/rdmss-jira-git commit`
Git-only commands. Create branch or smart-commit without Jira.

---

## 3. Issue File Format

```markdown
---
key: {KEY}
summary: "{title}"
type: {Bug|Feature|Story|Task}
priority: {Highest|High|Medium|Low}
epic: {parent_key|"none"}
status: {pending|in_progress|done}
jira_status: "{Jira status name}"
assignee: "{name|unassigned}"
synced_at: "{ISO datetime}"
branch: "{branch-name}"
pr_url: "{url}"
files_changed: []
---

# {KEY} — {summary}

## Description
{complete Jira description — never truncated}

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Implementation
_Pending_

## Notes
```

---

## 4. INDEX.md Auto-Generation

Always regenerated from frontmatter. Never edit manually. Sort: in_progress first, then pending (priority desc), then done. Includes: summary table, issues table, epics table.

---

## 5. Safety Rules

1. **Project lock**: config binds to ONE project + ONE repo.
2. **Config validation**: always validate before operations. Missing → setup wizard.
3. **Never hardcode IDs**: read from config.json.
4. **Branch safety**: never force-push, never delete branches without confirmation.
5. **Commit safety**: never commit `.env`, credentials, secrets.
6. **INDEX is disposable**: regenerated from frontmatter.
7. **Frontmatter is local truth**: Jira is remote truth. Sync reconciles.
8. **Idempotent**: sync/done twice won't break anything.
9. **Preserve local work**: sync never overwrites implementation notes on done issues.
10. **Full descriptions**: never truncate Jira descriptions.
11. **Memory**: save project reference for cross-conversation context.
12. **Offline-first**: local reads only. External APIs on explicit commands.
13. **Optional integrations**: gracefully skip disabled integrations.
14. **Verify before close**: if `verifyCommand` is set, run it before `done`.
