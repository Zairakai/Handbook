# Work Items

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Work Items

Standard for using GitLab Work Items across the Zairakai organization.

---

## Plan Constraint

> The Zairakai group is on the **GitLab Free plan**.  
> **Epic** (Work Item type) requires **Premium or higher** — it is not available on Free.  
> The hierarchy on Free is: **Issue → Task** only.  
> Until plan upgrade, multi-issue grouping is handled via a dedicated tracking Issue (see [Tracking Issues](#tracking-issues) below).

---

## Overview

Work Items are the unified model underlying all GitLab tracking entities. Types available on the Free plan:

| Type | Available | Purpose | Scope |
| ---- | --------- | ------- | ----- |
| **Issue** | Free | Unit of work with a defined outcome | Single feature, bug fix, task deliverable |
| **Task** | Free | Atomic sub-step of an Issue | Checklist item with an owner, state, and due date |
| **Epic** | Premium+ | Group a set of related Issues around a single goal | Multi-sprint chantier, major feature, broad refactor |

Other types (Objective, Key Result, Ticket, Incident) are not used.

When the group upgrades to Premium, Epics replace tracking Issues with no migration cost — Issues referenced in the tracking Issue description become the Epic children.

---

## Tracking Issues

On the Free plan, use a **tracking Issue** as an Epic placeholder when multiple Issues share a single goal.

### Rules

- Title prefix: `Epic: <goal>`
- Description must include a table listing all child Issues with their `#id` and status
- Labels: same as a standard Issue — `Kind::Feature`, `Priority::`, `Status::`, relevant `Area::` labels
- Close the tracking Issue only when **all** child Issues are closed
- Do not create Tasks directly on a tracking Issue — Tasks belong on child Issues only

### Example

```markdown
## Formats in scope

| Format | Issue | Priority |
|--------|-------|----------|
| Responsive web view | #2 | High |
| PDF export          | #3 | High |
| Print view          | #4 | Low  |
| JSON / vCard export | #5 | Low  |

## Definition of done
- All child Issues closed
- CI green on `develop`
```

---

## Hierarchy

**Free plan (current)**

```
Tracking Issue  (Epic placeholder — free text, no native hierarchy)
 └── Issue  (referenced via #id in tracking Issue description)
       └── Task
             └── Task  (nested, up to 2 levels max)
```

**Premium (when upgraded)**

```
Epic
 └── Issue
       └── Task
             └── Task  (nested, up to 2 levels max)
```

### Rules

- A **tracking Issue** must reference at least two child Issues — do not create a solo-issue tracking Issue.
- An **Issue** can exist without a tracking Issue (standalone work is valid).
- A **Task** must always have a parent Issue — never create a floating Task.
- Task nesting beyond 2 levels is forbidden — decompose the Issue further instead.

---

## When to Use Each Type

### Epic

Use an Epic when:

- The work spans more than one sprint.
- Multiple Issues contribute to a single coherent goal (e.g., "OAuth integration", "PDF export refactor").
- You need a rollup view of progress across Issues.

Do not create an Epic for:

- A single Issue regardless of its complexity.
- Vague themes with no concrete deliverables ("improve performance").

### Issue

The default unit of work. Use an Issue when:

- The work has a clear, singular outcome.
- It can be completed within one sprint.
- It warrants a branch, a MR, and a CI pipeline run.

An Issue maps 1-to-1 with a branch and a MR.

### Task

Use a Task when:

- An Issue has distinct sub-steps that benefit from individual ownership or state tracking.
- A step has its own due date or assignee separate from the parent Issue.
- Markdown checklists are insufficient (no assignee, no state, no tracking).

Do not convert a Task into an Issue unless it grows into MR-worthy work.

---

## Labels

The existing label taxonomy applies to all Work Item types without modification.

### Per type guidance

| Label scope | Epic | Issue | Task |
| ----------- | ---- | ----- | ---- |
| `Kind::` | Optional — set if the Epic is purely one kind | Required | Inherit from parent (do not repeat) |
| `Priority::` | Required | Required | Optional — override only if different from parent |
| `Status::` | Required | Required | Required |
| `Area::` | Optional — set if Epic is area-specific | Required | Inherit from parent (do not repeat) |
| Standalone | As needed | As needed | Rarely needed |

`Status::In Review` applies to Issues only — it signals an open MR.  
Epics and Tasks do not have MRs; skip `Status::In Review` for them.

---

## Issue Boards

Work Item types integrate with boards as follows:

- **Issues and Epics** appear in standard Issue Boards filtered by `Status::` labels.
- **Tasks** do not appear in Issue Boards — they are visible in the parent Issue's detail view and in the Work Items list view.

No new board type is required. Existing boards continue to work.

### Board standard per project type

**Applications** (daemon, nexus, aurora) — board name: **Sprint Board** — 6 columns:

| Column | Label |
| ------ | ----- |
| Discussion | `Status::Discussion` |
| Ready | `Status::Ready` |
| In Progress | `Status::In Progress` |
| In Review | `Status::In Review` |
| Blocked | `Status::Blocked` |
| On Hold | `Status::On Hold` |

**Packages** (PHP, NPM) — board name: **Workflow** — 4 columns:

| Column | Label |
| ------ | ----- |
| Ready | `Status::Ready` |
| In Progress | `Status::In Progress` |
| In Review | `Status::In Review` |
| Blocked | `Status::Blocked` |

Open = to do. Closed = done. No extra columns needed.

### Sprint tracking

On the Free plan, **milestones** serve as sprints. No sprint labels are used.

Workflow:

1. Create a milestone `Sprint N — YYYY-MM-DD` on the project.
2. Assign issues to the milestone — these are the sprint candidates.
3. Filter the Sprint Board by milestone for a sprint-scoped Kanban view.
4. Close the milestone when all assigned issues are closed.

`Priority::High` / `Priority::Critical` express urgency within the backlog.  
`Status::Ready` marks issues qualified for sprint assignment.

---

## Saved Views

Standard saved views created on all application projects (daemon, nexus, aurora):

| View | Filter | Sort |
| ---- | ------ | ---- |
| Blocked | `Status::Blocked` | Priority desc |
| In Progress | `Status::In Progress` | Updated desc |
| To Review | `Status::In Review` | Updated desc |
| Backend | `Area::Backend` | Priority desc |
| Frontend | `Area::Frontend` | Priority desc |

Additional views per project:

| Project | View | Filter | Sort |
| ------- | ---- | ------ | ---- |
| daemon | Ready | `Status::Ready` | Priority desc |
| daemon | Twitch | `Area::Twitch` | Priority desc |
| daemon | Discord | `Area::Discord` | Priority desc |
| daemon | Overlay | `Area::Overlay` | Priority desc |

Package projects do not have saved views — the Workflow board covers their needs.

---

## Naming Conventions

### Epics

Use the goal as the title, not the solution:

```
# Good
OAuth2 integration — Twitch + Discord providers

# Bad
Add OAuthController and refactor AuthService
```

### Issues

Use imperative mood, describe the outcome:

```
# Good
Add PDF export for CV profiles

# Bad
PDF stuff / export feature
```

### Tasks

Imperative, specific, fits on one line:

```
# Good
Extract PdfExportService from ResumeController
Write unit tests for PdfExportService::generate()

# Bad
Tests
```

---

## Branch and MR Creation

- Branches are created from **Issues only** — never from Tasks or Epics.
- The branch name template `%{id}-%{title}` (configured in project settings) applies to Issues.
- One Issue → one branch → one MR. Do not merge multiple Issues into one branch.
- Closing an Issue via MR (`Closes #id`) also rolls up completion to the parent Epic.

---

## Activation per Project

Work Items are enabled by default (see [Project Settings][project]).  
No per-project action is required — the feature is already active on all projects.

To verify: **Settings → General → Visibility, project features, permissions → Work items → Everyone With Access**.

---

**[Back to GitLab Configuration][gitlab]**

[handbook]: ../README.md
[gitlab]: ./README.md
[project]: ./project.md
