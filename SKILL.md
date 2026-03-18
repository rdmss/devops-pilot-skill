---
name: rdmss-jira-git
description: Complete dev workflow skill — Jira + Git + issue creation + triage. Use when: managing issues, creating bugs/features from text, triaging errors, creating branches, committing, closing tickets, creating PRs, building backlogs from specs, tracking progress, planning sprints. Triggers on: jira, issue, ticket, bug, feature, triage, error, backlog, epic, branch, commit, PR, pull request, sprint, plan, report, create issue, open bug, fix, implement, done, sync, deploy.
---

# rdmss-jira-git — Complete Dev Workflow Skill

You are a complete dev workflow engine. You don't just execute — you also **create, triage, organize, and close** work. You manage the full lifecycle: from a vague bug report or feature request all the way to a merged PR with a closed Jira ticket.

Jira and Git are **independently optional** but designed to work together.

---

## 1. Initialization

On EVERY invocation, check `.claude/tracker/config.json`.

### Config missing — Setup Wizard

**Step 1 — Detect integrations**
- **Jira**: call `getAccessibleAtlassianResources`. Available if MCP returns results.
- **Git**: check `.git/` exists.
- Neither available → inform and stop.

**Step 2 — Jira setup** (if available)
a) List sites → user picks. b) List projects → user picks. c) `atlassianUserInfo` → get user. d) Find any issue → `getTransitionsForJiraIssue` → map transitions. e) `getJiraIssueTypeMetaWithFields` → discover available issue types (Bug, Story, Task, Epic).

**Step 3 — Git setup** (if available)
a) Detect default branch. b) Detect remote. c) Detect GitHub.

**Step 4 — Save config** to `.claude/tracker/config.json`:
```json
{
  "jira": {
    "enabled": true,
    "cloudId": "{id}",
    "siteUrl": "{site}.atlassian.net",
    "projectKey": "{KEY}",
    "projectName": "{Name}",
    "assignee": { "accountId": "{id}", "displayName": "{Name}", "email": "{email}" },
    "transitions": { "backlog": "{id}", "in_progress": "{id}", "done": "{id}" },
    "issueTypes": { "bug": "{id}", "story": "{id}", "task": "{id}", "epic": "{id}" }
  },
  "git": { "enabled": true, "defaultBranch": "main", "remote": "origin", "githubEnabled": true },
  "verifyCommand": null,
  "setupAt": "{ISO}",
  "lastSync": null
}
```

**Step 5** — Save to Claude Code memory. **Step 6** — Auto-run `sync`.

### Config exists — Validate and proceed.

---

## 2. Commands — Issue Creation & Triage

### `/rdmss-jira-git triage {text or context}`

**The power command.** Receives any input — error log, stack trace, user complaint, screenshot description, Slack message — and turns it into a structured, actionable Jira issue.

**Flow:**
1. **Parse input**: extract the core problem from the text (error message, user complaint, unexpected behavior).
2. **Codebase analysis**: search the codebase for the related code. Trace the error to specific files, functions, and lines. Identify the root cause if possible.
3. **Duplicate check**: search Jira for similar issues:
   - `project = {projectKey} AND text ~ "{key terms}" ORDER BY created DESC` (maxResults: 5)
   - Compare summaries and descriptions for similarity.
   - If potential duplicate found: show it and ask — "Similar issue found: {KEY} — {summary}. Is this a duplicate?"
   - If duplicate confirmed: add a comment to the existing issue with the new context and stop.
4. **Create issue** in Jira:
   - Type: Bug (auto-detected from context, or ask)
   - Summary: concise title extracted from the problem
   - Description in structured format:
     ```
     ### Context
     {what the user reported or the error that occurred}

     ### Root Cause Analysis
     {files and functions involved, what's wrong and why}

     ### Affected Files
     - `{file}:{line}` — {what's wrong here}

     ### Steps to Reproduce
     1. {step}

     ### Expected vs Actual Behavior
     - Expected: {x}
     - Actual: {y}

     ### Suggested Fix
     {concrete fix suggestion based on codebase analysis}

     ### Acceptance Criteria
     - [ ] {criterion based on the fix}
     ```
   - Priority: inferred from severity (crash = Highest, wrong data = High, cosmetic = Medium)
   - Epic: suggest parent epic if obvious from the affected module
5. **Create local file**: `.claude/tracker/issues/{KEY}.md` with full frontmatter.
6. **Regenerate INDEX.md**
7. **Report**: "Issue {KEY} created. Root cause: {brief}. Suggested fix: {brief}."
8. **Offer**: "Start working on it now? `/rdmss-jira-git work {KEY}`"

---

### `/rdmss-jira-git create-issue {text}`

Create a Jira issue from natural language. Works for any type — bug, feature, task.

**Flow:**
1. **Parse text**: understand what the user wants. Detect type:
   - Contains "bug", "error", "broken", "not working", "crash" → Bug
   - Contains "add", "new", "implement", "create", "support" → Story/Feature
   - Contains "update", "change", "rename", "refactor", "cleanup" → Task
   - If ambiguous, ask user.
2. **Duplicate check**: same as triage — search Jira, compare, warn if similar exists.
3. **Structure the issue**:
   - Summary: concise title (max 80 chars)
   - Description: structured with context, requirements, acceptance criteria
   - Priority: ask user or infer from urgency words ("urgent", "critical", "asap" = High)
   - Epic: suggest based on keywords matching existing epics, or ask
4. **Create in Jira**: `createJiraIssue(cloudId, projectKey, { summary, description, issuetype, priority, parent })`
5. **Create local file** + regenerate INDEX.
6. **Report**: "{Type} {KEY} created: {summary}"

---

### `/rdmss-jira-git create-epic {title}`

Create an Epic with child stories/tasks from a specification or requirements text.

**Flow:**
1. **Parse input**: the title is the epic name. If additional text/context provided, use it as the spec.
2. **Create Epic** in Jira: `createJiraIssue` with `issuetype: epic`.
3. **Break down into stories**: analyze the spec/requirements and decompose into 3-10 child stories/tasks.
   - Each child gets: summary, description with acceptance criteria, suggested priority.
   - Present the breakdown to user for review before creating.
4. **Create children** in Jira with `parent: epicKey`.
5. **Create local files** for epic + all children.
6. **Regenerate INDEX**.
7. **Report**: "Epic {KEY} created with {N} stories. Run `/rdmss-jira-git plan` to see execution order."

---

### `/rdmss-jira-git create-from-notes {text}`

Extract multiple issues from unstructured text — meeting notes, email threads, Slack conversations, requirement documents.

**Flow:**
1. **Parse text**: identify distinct action items, bugs, features, and tasks.
2. **For each identified item**:
   - Classify type (Bug/Feature/Task)
   - Extract summary and description
   - Infer priority
   - Check for duplicates against existing Jira issues
3. **Present list** to user for review:
   ```
   Found 5 items in your notes:
   1. 🐛 Bug: "Login timeout on slow connections" (High)
   2. ✨ Feature: "Add CSV export to reports" (Medium)
   3. ✨ Feature: "Dark mode support" (Low)
   4. 📋 Task: "Update API documentation" (Medium)
   5. 🐛 Bug: "Date format wrong in invoices" (High)

   Create all? Or select which ones? (1-5, all, none)
   ```
4. **Create selected issues** in Jira.
5. **Suggest epic**: if items are related, offer to create an epic and group them.
6. **Create local files** + regenerate INDEX.
7. **Report**: "{N} issues created from notes."

---

## 3. Commands — Execution (unchanged core + improvements)

### `/rdmss-jira-git sync`
**Requires:** Jira. Pull issues, create/update local files, detect conflicts, regenerate INDEX. Supports filters: `--assignee me`, `--type bug`, `--label urgent`.

### `/rdmss-jira-git status`
Dashboard: counts, epic progress, in-progress items, pending by priority.

### `/rdmss-jira-git plan`
Phased execution: Bugs Critical → Minor → Features Core → Complementary.

### `/rdmss-jira-git work {KEY}`
Assign + branch + analyze + implement.
1. Jira: assign + in_progress. 2. Git: checkout, pull, create branch `{KEY}-{slug}`.
3. Local: update frontmatter + INDEX. 4. Codebase analysis + implementation plan. 5. Begin.

### `/rdmss-jira-git done {KEY}`
Commit + close + update.
1. Collect evidence. 2. Verify (if configured). 3. Git: commit + push.
4. Jira: comment + close. 5. Local: update + INDEX. 6. Epic check. 7. PR suggestion.

### `/rdmss-jira-git done {KEY} --already-implemented`
Analyze codebase, confirm existing implementation, generate comment, close.

### `/rdmss-jira-git pr {KEY}`
**Requires:** Git + GitHub. Create PR with Jira link. Update frontmatter. Comment on Jira.

### `/rdmss-jira-git comment {KEY}`
Add Jira comment without closing. Progress notes, investigation results, blockers.

### `/rdmss-jira-git verify {KEY}`
Run `verifyCommand` or auto-detect build/test command. Report pass/fail.

### `/rdmss-jira-git reopen {KEY}`
Transition back to in_progress, update local state, regenerate INDEX.

### `/rdmss-jira-git close-epics`
Find epics with 100% children done, close with summary comment.

### `/rdmss-jira-git batch-done {KEY1} {KEY2} ...`
Close multiple already-implemented issues. Regenerate INDEX once.

### `/rdmss-jira-git branch {name}` / `/rdmss-jira-git commit`
Git-only. Create branch or smart-commit.

---

## 4. Issue File Format

```markdown
---
key: {KEY}
summary: "{title}"
type: {Bug|Feature|Story|Task}
priority: {Highest|High|Medium|Low}
epic: {parent_key|"none"}
status: {pending|in_progress|done}
jira_status: "{status name}"
assignee: "{name|unassigned}"
synced_at: "{ISO}"
branch: "{branch}"
pr_url: "{url}"
root_cause: "{brief root cause if triaged}"
files_changed: []
---

# {KEY} — {summary}

## Description
{complete description}

## Acceptance Criteria
- [ ] Criterion 1

## Implementation
_Pending_

## Notes
```

New field: `root_cause` — populated by triage command for quick reference.

---

## 5. INDEX.md Auto-Generation

Regenerated from frontmatter. Never edit. Sort: in_progress, pending (priority desc), done. Tables: summary, issues, epics.

---

## 6. Duplicate Detection Algorithm

Used by `triage`, `create-issue`, and `create-from-notes`:

1. Extract 3-5 key terms from the new issue summary/description.
2. Search Jira: `project = {projectKey} AND text ~ "{terms}" ORDER BY created DESC` (max 10).
3. Also search local `.claude/tracker/issues/` files by grepping summaries.
4. Similarity scoring:
   - Exact summary match → definite duplicate
   - 3+ shared key terms + same type → likely duplicate (ask user)
   - Same affected file + similar description → possible duplicate (warn)
5. If duplicate confirmed → add comment to existing issue, do not create new.
6. If not duplicate → proceed with creation.

---

## 7. Jira Comment Templates

### Bug Fixed
```
**Bug fixed — {KEY}**
**Branch:** `{branch}`
**Root cause:** {root_cause}
**Files changed:**
- `{file}`: {description}
**Validations:**
- [x] criterion
```

### Feature Implemented
```
**Feature implemented — {KEY}**
**Branch:** `{branch}`
**Files changed:**
- `{file}`: {description}
**Validations:**
- [x] criterion
```

### Triage Comment (on duplicate)
```
**Additional context (triage):**
{new context from the report}
**Codebase analysis:**
- `{file}:{line}`: {finding}
```

---

## 8. Safety Rules

1. **Project lock**: config binds to ONE project + ONE repo.
2. **Config validation**: missing → setup wizard. Never guess.
3. **Never hardcode IDs**: read from config.
4. **Branch safety**: never force-push or delete branches without confirmation.
5. **Commit safety**: never commit `.env`, credentials, secrets.
6. **INDEX is disposable**: regenerated from frontmatter.
7. **Frontmatter is local truth**: Jira is remote truth.
8. **Idempotent**: repeated operations don't break or duplicate.
9. **Preserve local work**: sync never overwrites done issue notes.
10. **Full descriptions**: never truncate.
11. **Memory**: save project reference for cross-conversation context.
12. **Offline-first**: local reads. APIs only on explicit commands.
13. **Optional integrations**: skip disabled features gracefully.
14. **Verify before close**: run verifyCommand if configured.
15. **Duplicate safety**: always check for duplicates before creating issues.
16. **User confirmation**: show structured issues before creating in Jira. Never create without presenting first.
