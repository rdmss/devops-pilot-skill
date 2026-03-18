<p align="center">
  <img src="https://img.shields.io/badge/Claude_Code-Skill-7C3AED?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0xMiAyTDIgN2wxMCA1IDEwLTV6TTIgMTdsMTAgNSAxMC01TTIgMTJsMTAgNSAxMC01Ii8+PC9zdmc+&logoColor=white" alt="Claude Code Skill" />
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

1. Open Jira → find the issue → read the description → copy the key
2. Switch to terminal → create a branch → remember the naming convention
3. Open IDE → find the right files → understand the code
4. Fix the bug → test it → go back to terminal
5. Stage → commit (write message, remember the key) → push
6. Open GitHub → create PR → write description → link to Jira
7. Go back to Jira → add a comment → move to Done

**That's 7 context switches for one bug fix.**

## The Solution

```
/rdmss-jira-git work PD-65
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

## How It Works

```
                          YOUR TERMINAL
                    ┌──────────────────────┐
                    │                      │
     /sync          │   .claude/tracker/   │         /done PD-65
  ┌────────────────►│   ├── config.json    │────────────────────┐
  │                 │   ├── INDEX.md       │                    │
  │                 │   └── issues/        │                    │
  │                 │       ├── PD-50.md   │                    │
  │                 │       ├── PD-65.md   │                    │
  │                 │       └── PD-71.md   │                    │
  │                 │                      │                    │
  │                 └──────────────────────┘                    │
  │                          │                                  │
  │                    /work PD-65                               │
  │                          │                                  │
  │                 ┌────────┴────────┐                         │
  │                 │  1. Jira: assign│                 ┌───────┴───────┐
  │                 │  2. Git: branch │                 │  1. Git:      │
┌─┴──┐             │  3. Analyze code│                 │     commit    │
│Jira│             │  4. Plan & impl │                 │     push      │
│Cloud│             └─────────────────┘                 │  2. Jira:     │
└─┬──┘                                                 │     comment   │
  │                                                    │     close     │
  │                          /pr PD-65                  │  3. Epic:     │
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

 ✓ Jira: rdmss.atlassian.net (2 projects found)
   1. PD — Producao Digital
   2. DEV — Development Platform

 Which project? > 1

 ✓ User: Renan Miguel (renan@rdmss.com.br)
 ✓ Transitions: Backlog(11) → In Progress(31) → Done(41)
 ✓ Git: main branch, origin remote, GitHub detected
 ✓ Config saved to .claude/tracker/config.json

 Syncing issues...
 ✓ 28 issues synced (11 bugs, 17 features)

 Ready! Run /rdmss-jira-git status to see your dashboard.
```

**No manual IDs. No YAML editing. No documentation hunting.**

---

## Commands

### Overview

| Command | What it does |
|:--------|:-------------|
| `/rdmss-jira-git` | First run: setup wizard. After: status dashboard |
| `/rdmss-jira-git sync` | Pull issues from Jira → local markdown files |
| `/rdmss-jira-git status` | Dashboard with progress by epic and priority |
| `/rdmss-jira-git plan` | Execution plan by phases (bugs first, then features) |
| `/rdmss-jira-git work {KEY}` | Assign + branch + analyze + implement |
| `/rdmss-jira-git done {KEY}` | Commit + push + Jira comment + close |
| `/rdmss-jira-git pr {KEY}` | Create GitHub PR with Jira link |
| `/rdmss-jira-git batch-done {K1 K2 ...}` | Close multiple already-implemented issues |
| `/rdmss-jira-git close-epics` | Auto-close epics with 100% children done |
| `/rdmss-jira-git branch {name}` | Create branch (Git-only) |
| `/rdmss-jira-git commit` | Smart commit with conventional messages |

---

### `/rdmss-jira-git sync`

Downloads all issues from Jira and creates local markdown files with YAML frontmatter.

```
Sync: 5 novas, 3 atualizadas, 28 total (17 concluidas, 11 abertas)
```

Each issue becomes a `.md` file:

```yaml
---
key: PD-65
summary: "Stock exit not working"
type: Bug
priority: Highest
epic: PD-45
status: pending
jira_status: "Backlog"
assignee: "unassigned"
synced_at: "2026-03-18T06:30:00Z"
branch: ""
pr_url: ""
files_changed: []
---

# PD-65 — Stock exit not working

## Description
{full Jira description}

## Acceptance Criteria
- [ ] Stock exit creates movement with positionId
- [ ] Balance updates correctly after exit
```

---

### `/rdmss-jira-git status`

```
┌─────────────────────────────────────────────────┐
│  Producao Digital (PD)                          │
├──────────────────┬──────────────────────────────┤
│  ✅ Done          │  22                          │
│  🔧 In Progress   │   2                          │
│  ⬜ Pending       │   4                          │
│  Total           │  28                          │
├──────────────────┴──────────────────────────────┤
│  Progress: 22/28 (78%)                          │
├─────────────────────────────────────────────────┤
│  Epics                                          │
│  PD-43 Fluxo de Producao     8/8 ✅             │
│  PD-44 Cadastro              8/8 ✅             │
│  PD-45 Gestao de Estoque     6/6 ✅             │
│  PD-46 Manutencao            2/4 🔧             │
│  PD-47 Priorizacao           2/2 ✅             │
├─────────────────────────────────────────────────┤
│  In Progress                                    │
│  🔧 PD-71: Machine deactivation (High)          │
│     branch: PD-71-machine-deactivation          │
├─────────────────────────────────────────────────┤
│  Pending (by priority)                          │
│  ⬜ PD-72: Labor cost calculation (Medium)       │
│  ⬜ PD-73: Maintenance supplies (High)           │
└─────────────────────────────────────────────────┘
```

---

### `/rdmss-jira-git plan`

Generates a phased execution plan. Bugs always come first — nothing new works if the base is broken.

```
## Execution Plan — Producao Digital

### Phase 1: Critical Bugs (unblock the workflow)
 1. 🐛 PD-65 — Stock exit not working (Highest)
 2. 🐛 PD-48 — Transport box flow incorrect (Highest)
 3. 🐛 PD-62 — Product edit loses fields (Highest)
 4. 🐛 PD-64 — Position edit reverts data (Highest)

### Phase 2: Minor Bugs (user experience)
 5. 🐛 PD-49 — Refresh on delete (High)
 6. 🐛 PD-59 — Kit not updating (High)
 7. 🐛 PD-74 — Label scroll (High)
 8. 🐛 PD-60 — Label editor crash (High)

### Phase 3: Core Features
 9. ✨ PD-50 — Production module (Highest)
10. ✨ PD-56 — Barcode-only input (High)
11. ✨ PD-53 — Batch box creation (High)

### Phase 4: Complementary Features
12. ✨ PD-66 — Position suggestion (Medium)
13. ✨ PD-69 — Excel import (Medium)

Order: Bugs Highest → High → Features Highest → High → Medium
```

---

### `/rdmss-jira-git work PD-65`

The main command. One invocation does everything:

```
 ✓ Jira: PD-65 assigned to Renan Miguel
 ✓ Jira: transitioned to "In Progress"
 ✓ Git: branch PD-65-stock-exit-fix created from main
 ✓ Local: status updated to in_progress

 Codebase Analysis:
 Found: quickStockExit mutation in stockRouter.ts:4316
 Issue: StockMovement created without positionId
 Fix: Add positionId: bal.positionId to the create call

 Implementation plan:
 1. Edit src/trpc/stockRouter.ts line 4354
 2. Add positionId field to StockMovement create
 3. Verify with npm run build

 Starting implementation...
```

---

### `/rdmss-jira-git done PD-65`

Closes the loop:

```
 Collecting evidence...
 Files changed: src/trpc/stockRouter.ts
 Criteria: 2/2 validated

 ✓ Git: committed "fix(PD-65): add positionId to quickStockExit movement"
 ✓ Git: pushed to origin/PD-65-stock-exit-fix
 ✓ Jira: comment added with implementation details
 ✓ Jira: PD-65 transitioned to "Done"
 ✓ Local: INDEX.md regenerated

 Epic PD-45 (Gestao de Estoque): 6/6 children done.
 Close epic? (y/n)

 Create PR? Use: /rdmss-jira-git pr PD-65
```

The Jira comment is auto-generated:

```markdown
**Bug corrigido — PD-65**

**Branch:** `PD-65-stock-exit-fix`

**Arquivos alterados:**
- `src/trpc/stockRouter.ts`: added positionId to quickStockExit movement creation

**Validacoes:**
- [x] Stock exit creates movement with positionId
- [x] Balance updates correctly after exit
```

---

### `/rdmss-jira-git pr PD-65`

Creates a GitHub PR with Jira integration:

```
 ✓ PR created: fix(PD-65): add positionId to quickStockExit movement
   https://github.com/rdmss/producao-digital/pull/42

 ✓ Jira: PR link added as comment on PD-65
```

PR body includes:
- Summary of changes
- Link to Jira issue
- Acceptance criteria as test plan checklist

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
| Bug | `PD-65-stock-exit-fix` | `fix(PD-65): add positionId to stock exit` |
| Feature | `PD-61-raw-material-crud` | `feat(PD-61): add raw material CRUD` |
| Task | `PD-51-rename-box-label` | `chore(PD-51): rename Caixa Preta` |

Branch naming: `{KEY}-{summary-in-kebab-case}` (max 50 chars, ASCII only)

Commit format: [Conventional Commits](https://www.conventionalcommits.org/) with issue key as scope

---

## File Structure

```
your-project/
└── .claude/
    └── tracker/
        ├── config.json              # Auto-generated config (DO NOT EDIT)
        ├── INDEX.md                 # Auto-generated dashboard (DO NOT EDIT)
        └── issues/
            ├── PD-48.md             # One file per issue
            ├── PD-49.md             # YAML frontmatter + description
            ├── PD-50.md             # + acceptance criteria
            └── ...                  # + implementation notes
```

### Issue file anatomy

```yaml
---
key: PD-71                                    # Jira issue key
summary: "Machine deactivation admin only"    # Issue title
type: Feature                                 # Bug | Feature | Task
priority: High                                # Highest | High | Medium | Low
epic: PD-46                                   # Parent epic key
status: done                                  # pending | in_progress | done
jira_status: "Done"                           # Jira status name
assignee: "Renan Miguel"                      # Assigned developer
synced_at: "2026-03-18T06:30:00Z"            # Last sync timestamp
branch: "PD-71-machine-deactivation-admin"    # Git branch name
pr_url: "https://github.com/org/repo/pull/42" # Pull request URL
files_changed:                                # Files modified
  - src/trpc/maintenanceRouter.ts
---

# PD-71 — Machine deactivation admin only

## Description
{Complete Jira description — never truncated}

## Acceptance Criteria
- [x] Backend restricts to ADMIN only
- [x] Frontend hides button for non-ADMIN

## Implementation
Removed isSupervisor from deactivateMachine validation.
Now only isAdmin(user.role) is checked.

## Notes
Frontend already had the correct check at line 96.
```

---

## Typical Workflow

### Day 1: Setup

```bash
/rdmss-jira-git          # Auto-discovers everything, syncs issues
/rdmss-jira-git plan     # See execution plan by phases
```

### Daily Work

```bash
/rdmss-jira-git status          # What's pending?
/rdmss-jira-git work PD-65      # Start the highest priority item
# ... Claude implements the fix ...
/rdmss-jira-git done PD-65      # Close it everywhere
/rdmss-jira-git pr PD-65        # Open a PR
```

### Weekly

```bash
/rdmss-jira-git sync            # Pull latest from Jira
/rdmss-jira-git close-epics     # Close completed epics
/rdmss-jira-git status          # Progress report
```

### Bulk Operations

```bash
# Close multiple already-implemented features at once
/rdmss-jira-git batch-done PD-50 PD-58 PD-53 PD-54 PD-55
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

- **Project-locked**: Config binds to one Jira project + one Git repo. Cannot accidentally cross projects.
- **Never force-pushes**: All Git operations are safe. No `--force`, no branch deletion without confirmation.
- **Never commits secrets**: `.env`, credentials, `node_modules` are never staged.
- **Idempotent**: Running `sync` or `done` twice won't duplicate data or break state.
- **Offline-first**: All reads from local files. Jira/GitHub only contacted on explicit commands.
- **Preserves work**: Syncing never overwrites implementation notes or acceptance checkmarks on completed issues.

---

## Origin

This skill was born from real-world necessity — managing 28 Jira issues (11 bugs + 17 features) across a production industrial management system. Every command, every convention, and every safety rule exists because we needed it.

The phased execution approach (bugs first, features later) comes from a simple truth: **nothing new works if the base is broken**.

---

## License

[MIT](LICENSE)

---

<p align="center">
  Built by <a href="https://rdmss.com.br">RDMSS</a>
</p>
