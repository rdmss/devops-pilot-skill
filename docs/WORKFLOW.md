# Workflow Reference

## Issue State Machine

```
                /work                         /done
  ⬜ pending ──────────► 🔧 in_progress ──────────► ✅ done
       ▲                      │                  │      │
       │                      │                  │      │
       │              Jira: In Progress   Jira: Done    │
       │              Git: branch         Git: commit   │
       │              Analyze codebase    Git: push     │
       │              Plan & implement    Jira: comment │
       │                                  PR: suggest   │
       │                                                │
       └───────────────── /reopen ──────────────────────┘
```

## Full Lifecycle

```
  ┌──────────────────────────────────────────────────┐
  │ 1. SYNC        Pull issues from Jira             │
  │                Create local .md files             │
  │                Detect conflicts                   │
  │                Generate INDEX dashboard           │
  ├──────────────────────────────────────────────────┤
  │ 2. PLAN        Group by phase:                    │
  │                Bugs Critical → Bugs Minor          │
  │                → Features Core → Features Other    │
  ├──────────────────────────────────────────────────┤
  │ 3. WORK        Assign in Jira                     │
  │                Create Git branch                   │
  │                Analyze codebase                    │
  │                Implement fix/feature               │
  ├──────────────────────────────────────────────────┤
  │ 4. VERIFY      Run build/tests (optional)         │
  │                Report pass/fail                    │
  ├──────────────────────────────────────────────────┤
  │ 5. DONE        Commit with conventional message   │
  │                Push to remote                      │
  │                Comment on Jira with details        │
  │                Close issue in Jira                 │
  │                Check if epic can be closed         │
  ├──────────────────────────────────────────────────┤
  │ 6. PR          Create GitHub PR                   │
  │                Link to Jira issue                  │
  │                Add test plan from criteria         │
  ├──────────────────────────────────────────────────┤
  │ 7. CLOSE-EPICS Find epics with 100% done          │
  │                Close them with summary comment     │
  └──────────────────────────────────────────────────┘
```

## Integration Matrix

```
                    Jira Enabled         Jira Disabled
              ┌─────────────────────┬─────────────────────┐
  Git         │     FULL MODE       │    GIT-ONLY MODE    │
  Enabled     │  All features work  │  branch, commit, PR │
              │                     │  (no Jira tracking) │
              ├─────────────────────┼─────────────────────┤
  Git         │   JIRA-ONLY MODE    │                     │
  Disabled    │  sync, assign, close│  Setup wizard stops │
              │  track, epic close  │  (nothing available)│
              └─────────────────────┴─────────────────────┘
```

## Execution Phases

```
  Phase 1: Critical Bugs     ████████░░  Highest priority bugs
  Phase 2: Minor Bugs        ██████░░░░  High/Medium bugs
  Phase 3: Core Features     ████░░░░░░  Highest/High features
  Phase 4: Other Features    ██░░░░░░░░  Medium/Low features

  Rationale: Nothing new works if the base is broken.
```
