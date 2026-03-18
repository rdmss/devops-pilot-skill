<p align="center">
  <img src="https://img.shields.io/badge/Claude_Code-Skill-7C3AED?style=for-the-badge&logoColor=white" alt="Claude Code Skill" />
  <img src="https://img.shields.io/badge/Jira-Integration-0052CC?style=for-the-badge&logo=jira&logoColor=white" alt="Jira" />
  <img src="https://img.shields.io/badge/Git-Workflow-F05032?style=for-the-badge&logo=git&logoColor=white" alt="Git" />
  <img src="https://img.shields.io/badge/GitHub-PR_Automation-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub" />
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="MIT License" />
</p>

<h1 align="center">rdmss-jira-git</h1>

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
/rdmss-jira-git work ERP-42
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
| **Start work** | Open Jira, find issue, copy key, create branch manually | `/rdmss-jira-git work ERP-42` |
| **Track progress** | Switch between Jira board and IDE constantly | `/rdmss-jira-git status` |
| **Close issue** | Write commit, push, open Jira, write comment, move card | `/rdmss-jira-git done ERP-42` |
| **Create PR** | Open GitHub, write title, copy description, link Jira | `/rdmss-jira-git pr ERP-42` |
| **Close epics** | Check each child manually, update epic status | `/rdmss-jira-git close-epics` |
| **Batch close** | Open each issue, comment, close, repeat N times | `/rdmss-jira-git batch-done ERP-10 ERP-11 ERP-12` |
| **Plan sprint** | Read backlog, prioritize mentally, write plan somewhere | `/rdmss-jira-git plan` |
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
cp SKILL.md ~/.claude/skills/rdmss-jira-git.md

# Or per-project
cp SKILL.md your-project/.claude/skills/rdmss-jira-git.md
```

### 2. Run

```
/rdmss-jira-git
```

The setup wizard auto-discovers everything:

```
 Detecting integrations...

 ✓ Jira: acme.atlassian.net (2 projects found)
   1. ERP — Acme ERP
   2. WEB — Acme Website

 Which project? > 1

 ✓ User: Ana Silva (ana@acme.dev)
 ✓ Transitions: Backlog(11) → In Progress(31) → Done(41)
 ✓ Git: main branch, origin remote, GitHub detected
 ✓ Config saved to .claude/tracker/config.json

 Syncing issues...
 ✓ 23 issues synced (8 bugs, 15 features)

 Ready! Run /rdmss-jira-git status to see your dashboard.
```

**No manual IDs. No YAML editing. No documentation hunting.**

---

## Commands

### Overview

| Command | What it does |
|:--------|:-------------|
| `/rdmss-jira-git` | First run: setup wizard. After: status dashboard |
| `/rdmss-jira-git sync` | Pull issues from Jira to local markdown files |
| `/rdmss-jira-git status` | Dashboard with progress by epic and priority |
| `/rdmss-jira-git plan` | Execution plan by phases (bugs first, features later) |
| `/rdmss-jira-git work {KEY}` | Assign + branch + analyze codebase + implement |
| `/rdmss-jira-git done {KEY}` | Commit + push + Jira comment + close issue |
| `/rdmss-jira-git pr {KEY}` | Create GitHub PR with Jira link |
| `/rdmss-jira-git comment {KEY}` | Add a Jira comment without closing |
| `/rdmss-jira-git verify {KEY}` | Run build/tests before marking done |
| `/rdmss-jira-git reopen {KEY}` | Reopen a closed issue |
| `/rdmss-jira-git batch-done {K1 K2 ...}` | Close multiple already-implemented issues |
| `/rdmss-jira-git close-epics` | Auto-close epics with 100% children done |
| `/rdmss-jira-git branch {name}` | Create branch (Git-only) |
| `/rdmss-jira-git commit` | Smart commit with conventional messages |

---

### `/rdmss-jira-git sync`

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

### `/rdmss-jira-git status`

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

### `/rdmss-jira-git plan`

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

### `/rdmss-jira-git work ERP-42`

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

### `/rdmss-jira-git done ERP-42`

Closes the loop:

```
 Collecting evidence...
 Files changed: src/services/orderService.ts
 Criteria: 2/2 validated

 ✓ Git: committed "fix(ERP-42): add orderId to refund payment record"
 ✓ Git: pushed to origin/ERP-42-refund-processing
 ✓ Jira: comment added with implementation details
 ✓ Jira: ERP-42 transitioned to "Done"
 ✓ Local: INDEX.md regenerated

 Epic ERP-2 (Payments): 7/8 children done.
 1 remaining: ERP-38

 Create PR? Use: /rdmss-jira-git pr ERP-42
```

The Jira comment is auto-generated:

```markdown
**Bug fixed — ERP-42**

**Branch:** `ERP-42-refund-processing`

**Files changed:**
- `src/services/orderService.ts`: added orderId to refund payment record

**Validations:**
- [x] Refund creates payment record with orderId
- [x] Balance updates correctly after refund
```

---

### `/rdmss-jira-git pr ERP-42`

Creates a GitHub PR with Jira integration:

```
 ✓ PR created: fix(ERP-42): add orderId to refund payment record
   https://github.com/acme/erp-system/pull/42

 ✓ Jira: PR link added as comment on ERP-42
```

---

### `/rdmss-jira-git verify ERP-42`

Runs your configured build/test command before marking done:

```
 Running: npm run build
 ✓ Build passed (0 errors, 3 warnings)

 Running: npm test
 ✓ 142 tests passed

 ERP-42 verified. Ready to close with /rdmss-jira-git done ERP-42
```

---

### `/rdmss-jira-git comment ERP-42`

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
/rdmss-jira-git              # Auto-discovers everything, syncs issues
/rdmss-jira-git plan         # See phased execution plan
```

### Daily Work

```bash
/rdmss-jira-git status              # What's pending?
/rdmss-jira-git work ERP-42         # Start the highest priority item
# ... implementation happens ...
/rdmss-jira-git verify ERP-42       # Run build & tests
/rdmss-jira-git done ERP-42         # Close it everywhere
/rdmss-jira-git pr ERP-42           # Open a PR
```

### Weekly

```bash
/rdmss-jira-git sync                # Pull latest from Jira
/rdmss-jira-git close-epics         # Close completed epics
/rdmss-jira-git status              # Progress report
```

### Bulk Operations

```bash
# Close multiple already-implemented features
/rdmss-jira-git batch-done ERP-10 ERP-11 ERP-12 ERP-14
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
