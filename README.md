<p align="center">
  <img src="https://img.shields.io/badge/Claude_Code-Skill-7C3AED?style=for-the-badge&logoColor=white" alt="Claude Code Skill" />
  <img src="https://img.shields.io/badge/Jira-Integration-0052CC?style=for-the-badge&logo=jira&logoColor=white" alt="Jira" />
  <img src="https://img.shields.io/badge/Git-Workflow-F05032?style=for-the-badge&logo=git&logoColor=white" alt="Git" />
  <img src="https://img.shields.io/badge/GitHub-PR_Automation-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub" />
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="MIT License" />
</p>

<h1 align="center">devops-pilot</h1>

<p align="center">
  <strong>Dev Workflow Skill for Claude Code</strong><br/>
  Sync Jira issues, create branches, implement, commit, close tickets, and open PRs — all from your terminal.
</p>

<p align="center">
  <em>Jira and Git work independently or together. Use what you have.</em>
</p>

---

## The Problem

Every context switch kills productivity. A developer working on a bug goes through this:

1. Open Jira &rarr; find the issue &rarr; read the description &rarr; copy the key
2. Switch to terminal &rarr; create a branch &rarr; remember the naming convention
3. Open IDE &rarr; find the right files &rarr; understand the code
4. Fix the bug &rarr; test it &rarr; go back to terminal
5. Stage &rarr; commit (write message, remember the key) &rarr; push
6. Open GitHub &rarr; create PR &rarr; write description &rarr; link to Jira
7. Go back to Jira &rarr; add a comment &rarr; move to Done

**That's 7 context switches for one bug fix.**

## The Solution

```
/devops-pilot work ERP-42
```

One command. Zero context switches. The skill handles everything:
- Assigns the issue in Jira
- Creates a Git branch with proper naming
- Analyzes the codebase to find related code
- Implements the fix
- Commits with conventional message
- Closes the Jira issue with a structured comment
- Suggests creating a PR

---

## Before & After

| | Before (manual) | After (with skill) |
|:--|:----------------|:-------------------|
| **Start work** | Open Jira, find issue, copy key, create branch manually | `/devops-pilot work ERP-42` |
| **Track progress** | Switch between Jira board and IDE constantly | `/devops-pilot status` |
| **Close issue** | Write commit, push, open Jira, write comment, move card | `/devops-pilot done ERP-42` |
| **Create PR** | Open GitHub, write title, copy description, link Jira | `/devops-pilot pr ERP-42` |
| **Close epics** | Check each child manually, update epic status | `/devops-pilot close-epics` |
| **Batch close** | Open each issue, comment, close, repeat N times | `/devops-pilot batch-done ERP-10 ERP-11 ERP-12` |
| **Plan sprint** | Read backlog, prioritize mentally, write plan somewhere | `/devops-pilot plan` |
| **Context switches** | 7 per issue | 0 |

---

## How It Works

```
                          YOUR TERMINAL
                    ┌──────────────────────┐
                    │                      │
     /sync          │   .claude/tracker/   │         /done ERP-42
  ┌────────────────►│   ├── config.json    │────────────────────┐
  │                 │   ├── INDEX.md       │                    │
  │                 │   └── issues/        │                    │
  │                 │       ├── ERP-10.md  │                    │
  │                 │       ├── ERP-42.md  │                    │
  │                 │       └── ERP-7.md   │                    │
  │                 │                      │                    │
  │                 └──────────────────────┘                    │
  │                          │                                  │
  │                    /work ERP-42                              │
  │                          │                                  │
  │                 ┌────────┴────────┐                         │
  │                 │  1. Jira: assign│                 ┌───────┴───────┐
  │                 │  2. Git: branch │                 │  1. Git:      │
  │                 │  3. Analyze code│                 │     commit    │
┌─┴──┐             │  4. Plan & impl │                 │     push      │
│Jira│             └─────────────────┘                 │  2. Jira:     │
│Cloud│                                                │     comment   │
└─┬──┘                                                 │     close     │
  │                          /pr ERP-42                 │  3. Epic:     │
  │                               │                    │     check     │
  │                      ┌────────┴────────┐           └───────────────┘
  │                      │  GitHub PR with │
  │◄─────────────────────│  Jira link      │
  │   link in comment    └─────────────────┘
  │
```

---

## Quick Start

### 1. Install

```bash
# Global (all projects)
mkdir -p ~/.claude/skills
cp SKILL.md ~/.claude/skills/devops-pilot.md

# Or per-project
cp SKILL.md your-project/.claude/skills/devops-pilot.md
```

### 2. Run

```
/devops-pilot
```

The setup wizard verifies authentication and auto-discovers everything:

```
 Checking authentication...

 ✓ Jira: authenticated (acme.atlassian.net)
 ✓ GitHub: authenticated (ana-silva)
 ✓ Git: repository detected (origin → github.com/acme/erp-system)

 Available projects on acme.atlassian.net:
   1. ERP — Acme ERP System
   2. WEB — Company Website
   3. MOB — Mobile App

 Which project will you work on? > 1

 ✓ Working as: Ana Silva (ana@acme.dev)
 ✓ Transitions: Backlog(11) → In Progress(31) → Done(41)
 ✓ Issue types: Bug, Story, Task, Epic
 ✓ GitHub repo: acme/erp-system
 ✓ Config saved to .claude/tracker/config.json

 Syncing issues...
 ✓ 23 issues synced (8 bugs, 15 features)

 Ready! Run /devops-pilot status to see your dashboard.
```

If something is missing, the wizard tells you exactly what to do:

```
 Checking authentication...

 ✓ Jira: authenticated (acme.atlassian.net)
 ✗ GitHub: not authenticated
   → Run: gh auth login
 ✓ Git: repository detected

 Continuing without GitHub PR features...
```

**No manual IDs. No YAML editing. No documentation hunting.**

---

## Commands

### Overview

**Create & Triage** (turn text into structured Jira issues)

| Command | What it does |
|:--------|:-------------|
| `/devops-pilot triage {text}` | Analyze error/bug report, find root cause in code, create Jira issue |
| `/devops-pilot create-issue {text}` | Create bug/feature/task from natural language |
| `/devops-pilot create-epic {title}` | Create epic + break down into child stories |
| `/devops-pilot create-from-notes {text}` | Extract multiple issues from meeting notes or emails |

**Execute** (work on issues end-to-end)

| Command | What it does |
|:--------|:-------------|
| `/devops-pilot work {KEY}` | Assign + branch + analyze codebase + implement |
| `/devops-pilot done {KEY}` | Commit + push + Jira comment + close issue |
| `/devops-pilot pr {KEY}` | Create GitHub PR with Jira link |
| `/devops-pilot merge {KEY}` | Merge PR, delete branch, cleanup |
| `/devops-pilot review {KEY}` | Review a PR with code analysis |
| `/devops-pilot verify {KEY}` | Run build/tests before marking done |
| `/devops-pilot comment {KEY}` | Add a Jira comment without closing |
| `/devops-pilot reopen {KEY}` | Reopen a closed issue |
| `/devops-pilot batch-done {K1 K2 ...}` | Close multiple already-implemented issues |

**Edit & Organize** (modify issues and manage backlog)

| Command | What it does |
|:--------|:-------------|
| `/devops-pilot edit {KEY} [--field val]` | Edit summary, priority, description, labels, epic |
| `/devops-pilot assign {KEY} {user}` | Assign issue to a different team member |
| `/devops-pilot move {KEY} {status}` | Move issue to any workflow status |
| `/devops-pilot link {KEY1} {rel} {KEY2}` | Link issues (blocks, relates-to, duplicates) |
| `/devops-pilot label {KEY} {labels}` | Add or remove labels |
| `/devops-pilot delete {KEY}` | Delete an issue (with confirmation) |

**Manage** (track, plan, and organize)

| Command | What it does |
|:--------|:-------------|
| `/devops-pilot` | Setup wizard (first time) or status dashboard |
| `/devops-pilot sync` | Pull issues from Jira to local markdown files |
| `/devops-pilot status` | Dashboard with progress by epic and priority |
| `/devops-pilot plan` | Execution plan by phases (bugs first, features later) |
| `/devops-pilot sprint` | View current sprint, progress, and issues |
| `/devops-pilot backlog` | View and sort the full backlog |
| `/devops-pilot close-epics` | Auto-close epics with 100% children done |
| `/devops-pilot branch {name}` | Create branch (Git-only) |
| `/devops-pilot commit` | Smart commit with conventional messages |

---

### `/devops-pilot triage`

The power command. Give it any bug report — error log, user complaint, stack trace — and it finds the root cause in your code and creates a structured Jira issue:

```
> /devops-pilot triage "Users report that refunds are not showing up
  in their account after processing. Error in logs: PaymentRecord
  created without orderId reference."

 Analyzing codebase...
 Found: processRefund() in src/services/orderService.ts:218
 Root cause: PaymentRecord.create() missing orderId field (line 234)

 Checking for duplicates...
 No similar issues found in Jira.

 Creating issue...
 ✓ ERP-42 created: "Refund not processing — missing orderId"
   Type: Bug | Priority: Highest | Epic: ERP-3 (Payments)

   Root cause: orderService.ts:234 — orderId not passed to PaymentRecord
   Suggested fix: Add orderId: order.id to the create call

 Start working on it? /devops-pilot work ERP-42
```

---

### `/devops-pilot create-issue`

Turn natural language into a structured Jira issue:

```
> /devops-pilot create-issue "We need to add CSV export to the
  reports page. Users should be able to export any report as CSV
  with one click. High priority."

 Detected type: Feature (Story)
 Checking for duplicates... none found.

 ✓ ERP-25 created: "Add CSV export to reports page"
   Type: Story | Priority: High | Epic: ERP-3 (Reports)

   Acceptance Criteria:
   - [ ] Export button visible on all report pages
   - [ ] CSV contains all visible columns
   - [ ] Large reports export without timeout
```

---

### `/devops-pilot create-epic`

Build a complete backlog from a specification:

```
> /devops-pilot create-epic "Payment System Overhaul" "We need to
  support multiple payment gateways, add refund automation, and
  implement subscription billing."

 ✓ Epic ERP-50 created: "Payment System Overhaul"

 Breaking down into stories...
 1. ✨ ERP-51: Multi-gateway payment abstraction (High)
 2. ✨ ERP-52: Stripe integration (High)
 3. ✨ ERP-53: PayPal integration (Medium)
 4. ✨ ERP-54: Automated refund processing (High)
 5. ✨ ERP-55: Subscription billing engine (High)
 6. ✨ ERP-56: Billing dashboard and reports (Medium)

 Create all 6 stories? (y/n)
```

---

### `/devops-pilot create-from-notes`

Extract issues from meeting notes, emails, or Slack threads:

```
> /devops-pilot create-from-notes "From today's standup:
  - The login page is broken on Safari
  - We need dark mode before the demo next week
  - API docs are outdated, someone needs to update them
  - The export is timing out on large datasets"

 Found 4 items:
 1. 🐛 Bug: "Login broken on Safari" (High)
 2. ✨ Feature: "Dark mode support" (High) — deadline mentioned
 3. 📋 Task: "Update API documentation" (Medium)
 4. 🐛 Bug: "Export timeout on large datasets" (High)

 Checking for duplicates...
 ⚠️  Item 4 may be a duplicate of ERP-38 "Export performance issues"
     → Add context to ERP-38 instead? (y/n)

 Create items 1, 2, 3? (y/n)
```

---

### `/devops-pilot sync`

Downloads all issues from Jira and creates local markdown files with YAML frontmatter.

```
Sync: 3 new, 2 updated, 23 total (15 done, 8 open)
```

Each issue becomes a `.md` file:

```yaml
---
key: ERP-42
summary: "Refund not processing correctly"
type: Bug
priority: Highest
epic: ERP-3
status: pending
jira_status: "Backlog"
assignee: "unassigned"
synced_at: "2026-03-18T06:30:00Z"
branch: ""
pr_url: ""
files_changed: []
---

# ERP-42 — Refund not processing correctly

## Description
{full Jira description}

## Acceptance Criteria
- [ ] Refund creates payment record with orderId
- [ ] Balance updates correctly after refund
```

---

### `/devops-pilot status`

```
┌──────────────────────────────────────────────┐
│  Acme ERP (ERP)                              │
├──────────────────┬───────────────────────────┤
│  ✅ Done          │  15                       │
│  🔧 In Progress   │   2                       │
│  ⬜ Pending       │   6                       │
│  Total           │  23                       │
├──────────────────┴───────────────────────────┤
│  Progress: 15/23 (65%)                       │
├──────────────────────────────────────────────┤
│  Epics                                       │
│  ERP-1 User Management     5/5 ✅            │
│  ERP-2 Payments            6/8 🔧            │
│  ERP-3 Reports             2/5 🔧            │
│  ERP-4 Integrations        2/2 ✅            │
│  ERP-5 Performance         0/3 ⬜            │
├──────────────────────────────────────────────┤
│  In Progress                                 │
│  🔧 ERP-42: Refund processing (Highest)      │
│     branch: ERP-42-refund-processing         │
├──────────────────────────────────────────────┤
│  Pending (by priority)                       │
│  ⬜ ERP-38: Export timeout (High) 🐛         │
│  ⬜ ERP-25: Dashboard charts (Medium) ✨      │
└──────────────────────────────────────────────┘
```

---

### `/devops-pilot plan`

Generates a phased execution plan. Bugs always come first.

```
## Execution Plan — Acme ERP

### Phase 1: Critical Bugs (unblock the workflow)
 1. 🐛 ERP-42 — Refund not processing (Highest)
 2. 🐛 ERP-38 — Export timeout on large datasets (High)
 3. 🐛 ERP-31 — Login session expires too fast (High)

### Phase 2: Minor Bugs
 4. 🐛 ERP-19 — UI not refreshing after delete (Medium)
 5. 🐛 ERP-16 — Date format inconsistency (Low)

### Phase 3: Core Features
 6. ✨ ERP-25 — Dashboard analytics charts (High)
 7. ✨ ERP-22 — Bulk import from CSV (High)

### Phase 4: Complementary Features
 8. ✨ ERP-14 — Dark mode support (Medium)

Order: Bugs Highest → High → Features Highest → High → Medium
```

---

### `/devops-pilot work ERP-42`

The main command. One invocation does everything:

```
 ✓ Jira: ERP-42 assigned to Ana Silva
 ✓ Jira: transitioned to "In Progress"
 ✓ Git: branch ERP-42-refund-processing created from main
 ✓ Local: status updated to in_progress

 Codebase Analysis:
 Found: processRefund function in orderService.ts:218
 Issue: PaymentRecord created without orderId reference
 Fix: Add orderId field to the create call

 Implementation plan:
 1. Edit src/services/orderService.ts line 234
 2. Add orderId to PaymentRecord creation
 3. Verify with npm run build

 Starting implementation...
```

---

### `/devops-pilot done ERP-42`

Closes the loop:

```
 Collecting evidence...
 Analyzing diff: 1 file changed, 3 insertions
 Root cause identified: orderId missing from PaymentRecord.create()
 Criteria: 2/2 validated

 ✓ Verify: npm run build passed (0 errors)
 ✓ Git: committed "fix(ERP-42): add orderId to refund payment record"
       commit: a3f8c21
 ✓ Git: pushed to origin/ERP-42-refund-processing
 ✓ Jira: rich comment added (root cause + files + criteria)
 ✓ Jira: ERP-42 transitioned to "Done"
 ✓ GitHub: issue #42 closed with commit reference
 ✓ Local: INDEX.md regenerated

 Epic ERP-2 (Payments): 7/8 children done.
 1 remaining: ERP-38

 Create PR? Use: /devops-pilot pr ERP-42
```

The Jira comment is **rich and individualized** — never generic:

```markdown
### Bug fixed — ERP-42

**Root cause:**
PaymentRecord.create() in processRefund() was missing the orderId
field, causing refunds to be created without a reference to the
original order. This made them invisible in the account history.

**Solution:**
Added orderId: order.id to the PaymentRecord.create() call in
processRefund(), ensuring every refund is linked to its order.

**Branch:** `ERP-42-refund-processing`
**Commit:** `a3f8c21` — `fix(ERP-42): add orderId to refund payment record`

**Files changed:**
| File | Change |
|------|--------|
| `src/services/orderService.ts` | Added `orderId: order.id` to PaymentRecord.create() at line 234 |

**Acceptance criteria:**
- [x] Refund creates payment record with orderId
- [x] Balance updates correctly after refund

**Build:** Verified — `npm run build` passed with 0 errors
```

The GitHub issue also gets a rich close comment:

```markdown
**Resolved in [`a3f8c21`](https://github.com/acme/erp-system/commit/a3f8c21)**

**Bug:** Refund not processing — missing orderId in PaymentRecord

**Changes:**
- `src/services/orderService.ts`: Added orderId to PaymentRecord.create()

**Root cause:** processRefund() created records without order reference

**Jira:** [ERP-42](https://acme.atlassian.net/browse/ERP-42)
```

---

### `/devops-pilot pr ERP-42`

Creates a GitHub PR with Jira integration:

```
 ✓ PR created: fix(ERP-42): add orderId to refund payment record
   https://github.com/acme/erp-system/pull/42

 ✓ Jira: PR link added as comment on ERP-42
```

---

### `/devops-pilot verify ERP-42`

Runs your configured build/test command before marking done:

```
 Running: npm run build
 ✓ Build passed (0 errors, 3 warnings)

 Running: npm test
 ✓ 142 tests passed

 ERP-42 verified. Ready to close with /devops-pilot done ERP-42
```

---

### `/devops-pilot comment ERP-42`

Add a progress note to Jira without closing:

```
 Added comment to ERP-42:
 "Investigation complete. Root cause: orderId missing in payment record creation.
  Fix ready, pending code review."
```

---

## Integration Modes

The skill adapts to what's available:

```
┌─────────────────────────────────────────────────────┐
│                   Jira Available                    │
│              YES              │         NO          │
├──────────────┬────────────────┼─────────────────────┤
│              │   FULL MODE    │    GIT-ONLY MODE    │
│  Git    YES  │   Everything   │    branch, commit,  │
│  Available   │   works        │    PR (no Jira)     │
│              │                │                     │
├──────────────┼────────────────┼─────────────────────┤
│              │  JIRA-ONLY     │                     │
│  Git    NO   │  sync, assign, │    Setup wizard     │
│  Available   │  close, track  │    stops (nothing   │
│              │  (no branches) │    to integrate)    │
└──────────────┴────────────────┴─────────────────────┘
```

---

## Git Conventions

The skill enforces consistent naming:

| Type | Branch Name | Commit Message |
|:-----|:------------|:---------------|
| Bug | `ERP-42-refund-processing` | `fix(ERP-42): add orderId to refund record` |
| Feature | `ERP-25-dashboard-charts` | `feat(ERP-25): add analytics dashboard charts` |
| Task | `ERP-16-date-format` | `chore(ERP-16): standardize date format` |

Branch: `{KEY}-{summary-kebab-case}` (max 50 chars, ASCII)

Commits: [Conventional Commits](https://www.conventionalcommits.org/) with issue key as scope

---

## File Structure

```
your-project/
└── .claude/
    └── tracker/
        ├── config.json              # Auto-generated config (DO NOT EDIT)
        ├── INDEX.md                 # Auto-generated dashboard (DO NOT EDIT)
        └── issues/
            ├── ERP-42.md            # One file per issue
            ├── ERP-25.md            # YAML frontmatter + full description
            ├── ERP-38.md            # + acceptance criteria + impl notes
            └── ...
```

### Issue file anatomy

```yaml
---
key: ERP-7                                       # Jira issue key
summary: "User deletion restricted to admin"     # Issue title
type: Feature                                    # Bug | Feature | Task
priority: High                                   # Highest | High | Medium | Low
epic: ERP-1                                      # Parent epic key
status: done                                     # pending | in_progress | done
jira_status: "Done"                              # Jira status name
assignee: "Ana Silva"                            # Assigned developer
synced_at: "2026-03-18T06:30:00Z"               # Last sync
branch: "ERP-7-user-deletion-admin"              # Git branch
pr_url: "https://github.com/acme/erp/pull/7"    # PR URL
files_changed:                                   # Modified files
  - src/services/userService.ts
---

# ERP-7 — User deletion restricted to admin

## Description
{Complete Jira description}

## Acceptance Criteria
- [x] Backend restricts to ADMIN role
- [x] Frontend hides button for non-ADMIN

## Implementation
Removed supervisor check from deleteUser validation.

## Notes
Frontend already had the correct role check.
```

---

## Typical Workflow

### Day 1: Setup

```bash
/devops-pilot              # Auto-discovers everything, syncs issues
/devops-pilot plan         # See phased execution plan
```

### Daily Work

```bash
/devops-pilot status              # What's pending?
/devops-pilot work ERP-42         # Start the highest priority item
# ... implementation happens ...
/devops-pilot verify ERP-42       # Run build & tests
/devops-pilot done ERP-42         # Close it everywhere
/devops-pilot pr ERP-42           # Open a PR
```

### Weekly

```bash
/devops-pilot sync                # Pull latest from Jira
/devops-pilot close-epics         # Close completed epics
/devops-pilot status              # Progress report
```

### Bulk Operations

```bash
# Close multiple already-implemented features
/devops-pilot batch-done ERP-10 ERP-11 ERP-12 ERP-14
```

---

## Prerequisites

| Requirement | Purpose | Required? |
|:------------|:--------|:----------|
| [Claude Code](https://claude.ai/claude-code) | Runtime | Yes |
| [Atlassian MCP Plugin](https://github.com/anthropics/claude-code) | Jira integration | For Jira features |
| Git | Branch/commit/push | For Git features |
| [GitHub CLI (`gh`)](https://cli.github.com/) | PR creation | For PR features |

---

## Safety

- **Project-locked** &mdash; Config binds to one Jira project + one Git repo. Cannot accidentally cross projects.
- **Never force-pushes** &mdash; All Git operations are safe. No `--force`, no branch deletion without confirmation.
- **Never commits secrets** &mdash; `.env`, credentials, `node_modules` are never staged.
- **Idempotent** &mdash; Running `sync` or `done` twice won't duplicate data or break state.
- **Offline-first** &mdash; All reads from local files. Jira/GitHub only contacted on explicit commands.
- **Preserves work** &mdash; Syncing never overwrites implementation notes or acceptance checkmarks on completed issues.
- **Conflict detection** &mdash; Warns when a Jira issue changed since your last sync.

---

## FAQ

<details>
<summary><strong>Does it work without Jira?</strong></summary>
Yes. If no Atlassian MCP is configured, the skill runs in Git-only mode: branches, commits, and PRs work normally. Jira-specific commands (sync, assign, close) are simply skipped.
</details>

<details>
<summary><strong>Does it work without Git?</strong></summary>
Yes. If no <code>.git/</code> directory exists, the skill runs in Jira-only mode: sync, status, plan, assign, and close all work. Git-specific commands (branch, commit, PR) are skipped.
</details>

<details>
<summary><strong>Can I use it with multiple Jira projects?</strong></summary>
One project per workspace. The config locks to a single project for safety. If you need another project, set up a different workspace or re-run the setup wizard.
</details>

<details>
<summary><strong>What happens if I run <code>done</code> twice?</strong></summary>
Nothing breaks. The skill is idempotent. The local file stays as <code>done</code>, and Jira ignores transitions to the same status.
</details>

<details>
<summary><strong>Does it support Jira Server / Data Center?</strong></summary>
Currently designed for Jira Cloud via the Atlassian MCP plugin. Server/DC support depends on MCP plugin compatibility.
</details>

<details>
<summary><strong>Can I customize the commit message format?</strong></summary>
The skill uses Conventional Commits by default (<code>fix(KEY): message</code>). The issue key is always included as the scope for traceability.
</details>

<details>
<summary><strong>What if someone else changed the Jira issue?</strong></summary>
The <code>sync</code> command detects conflicts: if an issue was modified in Jira after your last <code>synced_at</code>, it warns you before overwriting local data.
</details>

---

## Contributing

Contributions are welcome! Here's how:

1. Fork the repository
2. Create a branch: `git checkout -b feature/my-improvement`
3. Make your changes to `SKILL.md` (the core skill definition)
4. Test with Claude Code in a real project
5. Submit a PR with a clear description of what changed and why

### Guidelines

- Keep `SKILL.md` under 400 lines &mdash; it loads into Claude's context
- All examples must be generic (no real project data)
- New commands need documentation in both `SKILL.md` and `README.md`
- Safety rules are non-negotiable &mdash; never weaken them

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.

---

## Origin

This skill was born from real-world necessity &mdash; managing a full Jira backlog (bugs + features) across a production system entirely from the terminal. Every command, every convention, and every safety rule exists because it was needed.

The phased execution approach (bugs first, features later) comes from a simple truth: **nothing new works if the base is broken**.

---

## License

[MIT](LICENSE)

---

<p align="center">
  Built by <a href="https://rdmss.com.br">RDMSS</a>
</p>
