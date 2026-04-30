# Workflow Overlay

This file defines the current project execution plan. Cursor uses this as the
source for phased work, dry-runs, validation, status reporting, and resume.

Keep tasks concrete. Each task should have clear inputs, outputs, acceptance
criteria, dependencies, and verification expectations.

## Metadata

| Field | Value |
|-------|-------|
| Workflow version | `0.1` |
| Status | `[draft / ready / running / paused / completed]` |
| Created | `[ISO_DATE]` |
| Based on | `[PROJECT.md / issue / spec / design doc]` |
| Owner | `[person/team]` |

## Execution Policy

| Policy | Value |
|--------|-------|
| Commit mode | `[per-phase / final-only / manual / none]` |
| Review gate | `[required / optional]` |
| Test gate | `[required / risk-based / optional]` |
| Max parallel tasks | `[number]` |
| Runtime state path | `.cursor/runtime/` |
| Event journal | `.cursor/project/EVENTS.jsonl` |

## Agent Roles

| Role | Responsibility | Notes |
|------|----------------|-------|
| Implementation | Code/docs/config changes for assigned tasks | Follow `STRUCTURE.md`, `RULES.md`, and `CODING_CONVENTIONS.md`. |
| Testing | Test creation and verification | Follow `TOOLING.md`. |
| Review | PASS/FAIL quality gate | Use `.cursor/rules/030-review-quality.mdc`. |
| Commit | Local commit grouping if requested | Use project commit policy. |
| Pack-specific | Technology-specific setup | Use enabled packs under `.cursor/packs/`. |

## Phase Template

Copy this block for each phase.

### Phase N: `[PHASE_NAME]`

**Goal:** `[phase goal]`

**Entry Criteria:**
- `[criterion]`

**Exit Criteria:**
- `[criterion]`

**Verification:**
- `[command from TOOLING.md or manual validation]`

**Parallel Capacity:** `[n]`

#### PN.T1: `[TASK_TITLE]`

- **Type:** `[implementation / test / review / docs / tooling / pack-specific]`
- **Agent:** `[Cursor Agent / manual / pack-specific]`
- **Complexity:** `[S / M / L / XL]`
- **Inputs:**
  - `[path]`
- **Outputs:**
  - `[path]`
- **Description:**
  - `[specific implementation instructions]`
- **Acceptance Criteria:**
  - `[ ] [criterion]`
  - `[ ] [criterion]`
- **Verification:**
  - `[command or check]`
- **Parallel Group:** `[PN-A]`
- **Dependencies:** `[none or task ids]`
- **Risk:** `[low / medium / high]`
- **Notes:** `[optional]`

## Current Phases

### Phase 1: `[PHASE_NAME]`

**Goal:** `[phase goal]`

**Entry Criteria:**
- `[criterion]`

**Exit Criteria:**
- `[criterion]`

**Verification:**
- `[command/check]`

**Parallel Capacity:** `[n]`

#### P1.T1: `[TASK_TITLE]`

- **Type:** `[implementation / test / review / docs / tooling / pack-specific]`
- **Agent:** `[Cursor Agent / manual / pack-specific]`
- **Complexity:** `[S / M / L / XL]`
- **Inputs:**
  - `[path]`
- **Outputs:**
  - `[path]`
- **Description:**
  - `[specific instructions]`
- **Acceptance Criteria:**
  - `[ ] [criterion]`
- **Verification:**
  - `[command/check]`
- **Parallel Group:** `[P1-A]`
- **Dependencies:** `[none]`
- **Risk:** `[low / medium / high]`

## Dependency Graph

Use plain text or Mermaid.

```text
P1.T1 -> P1.T2 -> P2.T1
```

## Parallel Groups

| Group | Tasks | Can Run Together? | Conflict Notes |
|-------|-------|-------------------|----------------|
| `[P1-A]` | `[P1.T1, P1.T2]` | `[yes/no]` | `[notes]` |

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| `[risk]` | `[low/medium/high]` | `[low/medium/high]` | `[plan]` |

## Validation Checklist

Before marking a phase complete:

- [ ] All expected output files exist.
- [ ] Acceptance criteria are met.
- [ ] Review gate passed.
- [ ] Required tests or checks passed.
- [ ] `PROGRESS.md` is updated.
- [ ] `EVENTS.jsonl` is updated if workflow execution has started.
- [ ] Commit policy was followed.

