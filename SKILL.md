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

### Config does NOT exist → Setup Wizard

**Step 1 — Detect available integrations**

Check what's available:
- **Jira**: Try calling `getAccessibleAtlassianResources`. If the MCP tool exists and returns results → Jira is available
- **Git**: Check if `.git/` directory exists in the workspace root → Git is available
- If neither is available, inform user and stop

**Step 2 — Jira setup (if available)**

a) `getAccessibleAtlassianResources` → list sites. If 1: auto-select. If N: ask user.
b) `getVisibleJiraProjects(cloudId)` → display numbered list, ask user to pick
c) `atlassianUserInfo` → get current user's accountId, displayName, email
d) Search for any issue: `project = {projectKey} ORDER BY created DESC` (maxResults: 1)
e) `getTransitionsForJiraIssue(cloudId, issueKey)` → map transition names to IDs:
   - Names containing "backlog" or "to do" → `backlog`
   - Names containing "progress" or "andamento" → `in_progress`
   - Names containing "done" or "conclu" → `done`
   - If ambiguous, show all transitions and ask user to confirm mapping

**Step 3 — Git setup (if available)**

a) Detect default branch: `git symbolic-ref refs/remotes/origin/HEAD` or check for main/master
b) Detect remote: `git remote -v` → extract remote name and URL
c) Detect GitHub: if remote URL contains github.com, enable PR creation

**Step 4 — Save config**

Write `.claude/tracker/config.json`:

```json
{
  "jira": {
    "enabled": true,
    "cloudId": "{cloudId}",
    "siteUrl": "{site}.atlassian.net",
    "projectKey": "{PD}",
    "projectId": "{10100}",
    "projectName": "{Producao Digital}",
    "assignee": {
      "accountId": "{70121:xxx}",
      "displayName": "{Renan Miguel}",
      "email": "{renan@rdmss.com.br}"
    },
    "transitions": {
      "backlog": "{11}",
      "in_progress": "{31}",
      "done": "{41}"
    }
  },
  "git": {
    "enabled": true,
    "defaultBranch": "main",
    "remote": "origin",
    "githubEnabled": true
  },
  "language": "pt-BR",
  "setupAt": "{ISO datetime}",
  "lastSync": null
}
```

If Jira not available: `"jira": { "enabled": false }`.
If Git not available: `"git": { "enabled": false }`.

**Step 5 — Save to Claude Code memory**

Write a memory file (type: reference) recording:
- What project is configured (name, key, site)
- Config file location: `.claude/tracker/config.json`
- Which integrations are enabled (Jira, Git, both)

**Step 6 — First sync**

If Jira enabled, automatically run `sync`. Report results.

### Config EXISTS → Validate

- Read `.claude/tracker/config.json`
- Verify structure is valid
- If Jira enabled, verify projectKey is set
- If Git enabled, verify `.git/` still exists
- Proceed with requested command

---

## 2. Commands Reference

### `/rdmss-jira-git` (no args)

Config exists → show status dashboard.
Config missing → run Setup Wizard.

---

### `/rdmss-jira-git sync`

**Requires:** Jira enabled

1. Query Jira for all non-Epic issues:
   - Open: `project = {projectKey} AND issuetype != Epic AND status != Concluido ORDER BY priority DESC, created ASC`
   - Recently closed: `project = {projectKey} AND issuetype != Epic AND status = Concluido AND updated >= -30d`
   - Fields: `summary, description, status, issuetype, priority, assignee, parent`
   - Use `responseContentFormat: "markdown"`
2. Create `.claude/tracker/issues/` directory if needed
3. For each issue, create/update `{KEY}.md` (see Issue File Format below)
4. Status mapping:
   - Jira status contains "backlog" or "to do" → `status: pending`
   - Jira status contains "progress" or "andamento" or "selected" → `status: in_progress`
   - Jira status contains "done" or "conclu" or "finished" → `status: done`
5. Update rules (idempotent):
   - File exists AND `status: done` locally → preserve `## Implementation`, `files_changed`, acceptance checkmarks
   - File exists AND NOT done → update Jira metadata, preserve local notes
   - File doesn't exist → create new
6. Regenerate INDEX.md
7. Update `config.lastSync`
8. Report: `Sync: {new} new, {updated} updated, {total} total ({done}/{open})`

---

### `/rdmss-jira-git status`

Read all issue files from `.claude/tracker/issues/`, parse frontmatter, display dashboard in chat with counts by status, epics progress, in-progress items, and pending items sorted by priority.

---

### `/rdmss-jira-git plan`

Generate execution plan from open issues, grouping by phase:
- Phase 1: Critical bugs (Highest priority)
- Phase 2: Minor bugs (High/Medium)
- Phase 3: Core features (Highest/High priority)
- Phase 4: Complementary features (Medium/Low)

Order: Bugs always before features. Higher priority first within each group.

---

### `/rdmss-jira-git work {KEY}`

Start working on an issue.

1. Read issue from `.claude/tracker/issues/{KEY}.md`. If not found and Jira enabled, fetch and create it.
2. **Jira** (if enabled):
   - Assign: `editJiraIssue(cloudId, KEY, { assignee: { accountId } })`
   - Transition: `transitionJiraIssue(cloudId, KEY, { id: transitions.in_progress })`
3. **Git** (if enabled):
   - Check working tree is clean. If dirty, warn user.
   - `git checkout {defaultBranch} && git pull`
   - Create branch: `git checkout -b {KEY}-{slug}`
   - Slug = summary in kebab-case, max 50 chars, ASCII only
4. **Local**: Update frontmatter `status: in_progress`, `branch: {name}`. Regenerate INDEX.
5. **Codebase analysis**: Search for related code, check if already implemented, present plan.
6. Begin implementation.

---

### `/rdmss-jira-git done {KEY}`

Close an issue after implementation.

1. Read issue file
2. **Collect evidence**: files_changed from frontmatter or `git diff --name-only {defaultBranch}...HEAD`
3. **Git** (if enabled):
   - Stage, commit with conventional message: `fix({KEY}):` / `feat({KEY}):` / `chore({KEY}):`
   - Push to remote
4. **Jira** (if enabled):
   - Generate structured comment (branch, files changed, validations)
   - Add comment and transition to done
5. **Local**: Update frontmatter, mark criteria `[x]`, update implementation section, regenerate INDEX
6. **Epic check**: If all siblings done, offer to close the epic
7. **PR suggestion**: If Git enabled, suggest creating PR

---

### `/rdmss-jira-git done {KEY} --already-implemented`

For issues already implemented before tracking. Analyzes codebase, generates "already implemented" comment, closes in Jira.

---

### `/rdmss-jira-git pr {KEY}`

**Requires:** Git enabled + GitHub detected

1. Detect branch, check if PR exists
2. Generate title with conventional format
3. Generate body with summary, Jira link, test plan
4. Create PR via `gh pr create`
5. Update frontmatter with PR URL
6. If Jira enabled, add PR link as comment

---

### `/rdmss-jira-git close-epics`

**Requires:** Jira enabled

Query epics, check if all children are done, close those that are 100% complete with a comment listing all children.

---

### `/rdmss-jira-git batch-done {KEY1} {KEY2} ...`

Apply `done --already-implemented` to multiple issues. Brief analysis + close + update for each. Regenerate INDEX once at end.

---

### `/rdmss-jira-git branch {name}` / `/rdmss-jira-git commit`

Git-only commands. Create branch or smart-commit without Jira integration.

---

## 3. Issue File Format

```markdown
---
key: PD-71
summary: "Machine deactivation admin only"
type: Feature
priority: High
epic: PD-46
status: done
jira_status: "Done"
assignee: "Renan Miguel"
synced_at: "2026-03-18T06:30:00Z"
branch: "PD-71-machine-deactivation-admin"
pr_url: "https://github.com/org/repo/pull/42"
files_changed:
  - src/trpc/maintenanceRouter.ts
---

# PD-71 — Machine deactivation admin only

## Description
{complete Jira description}

## Acceptance Criteria
- [x] Backend restricts to ADMIN only
- [x] Frontend hides button for non-ADMIN

## Implementation
Removed isSupervisor from deactivateMachine validation.

## Notes
```

---

## 4. INDEX.md Auto-Generation

INDEX is ALWAYS regenerated from frontmatter. Never edit manually.
Sort: in_progress first, then pending (priority desc), then done.
Includes: summary table, issues table, epics progress table.

---

## 5. Emoji Reference

| Concept | Emoji |
|---------|-------|
| Bug | 🐛 |
| Feature/Story | ✨ |
| Task | 📋 |
| Pending | ⬜ |
| In Progress | 🔧 |
| Done | ✅ |

---

## 6. Safety Rules

1. **Project lock**: config binds to ONE Jira project + ONE Git repo.
2. **Config validation**: always validate before operations. Missing → setup wizard.
3. **Never hardcode IDs**: always read from config.json.
4. **Branch safety**: never force-push, never delete branches without confirmation.
5. **Commit safety**: never commit `.env`, credentials, secrets.
6. **INDEX is disposable**: regenerated from frontmatter.
7. **Frontmatter is local truth**: Jira is remote truth. Sync reconciles.
8. **Idempotent**: running sync/done twice won't break anything.
9. **Preserve local work**: sync never overwrites implementation notes on done issues.
10. **Full descriptions**: never truncate Jira descriptions.
11. **Memory**: save project reference to Claude Code memory.
12. **Offline-first**: reads from local files. External APIs only on explicit commands.
13. **Optional integrations**: gracefully skip disabled integrations.
