# Project Settings

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Project Settings

Default settings applied to all projects. Settings marked **override** must be adjusted per project type.

---

## General Settings

### Visibility, project features, permissions

#### Project visibility

- [x] Public
- [ ] Private

> **Override:** Applications (cardex, manabot, skillbridge) → Private.

#### Additional options

- [ ] Users can request access

#### Features

- [x] Work items
  - [x] Everyone With Access
  - [ ] Only Project members
- [x] Repository
  - [x] Everyone With Access
  - [ ] Only Project members
  - [x] Merge requests
    - [x] Everyone With Access
    - [ ] Only Project members
  - [ ] Forks
    Users can copy the repository to a new project.

    > Forks are disabled. Workflow: clone + branch + MR.
    > Forks fragment history and are unnecessary with controlled team access.

  - [ ] Git Large File Storage (LFS)
    Enable per project as needed.
- [ ] Container registry
  Enable on Docker image repos only.
- [x] CI/CD
  - [x] Everyone With Access
  - [ ] Only Project members
- [x] Analytics
  - [x] Everyone With Access
  - [ ] Only Project members
- [x] Security and compliance
  - [ ] Everyone With Access
  - [x] Only Project members
- [ ] Wiki
  Use the handbook instead.
- [ ] Snippets
- [ ] Package registry
  Enable on npm/Composer package repos only.
- [ ] Pages
- [ ] Monitor
- [ ] Environments
- [ ] Feature flags
- [ ] Infrastructure
- [x] Releases
  - [x] Everyone With Access
  - [ ] Only Project members

#### Email notifications

- [ ] Enable email notifications
  Inherited from group settings.
- [x] Show default emoji reactions
- [x] Warn about Potentially Unwanted Characters
  Highlights hidden unicode characters (bidi, homoglyphs) that can be used in exploits.
- [ ] Add additional webhook triggers for project access token expiration
- [ ] CI/CD Catalog project

---

## Service Desk

- [x] Activate Service Desk
- [x] Ticket visibility (restricted)
  New tickets are confidential by default.

### External participants

- [ ] Reopen issues when an external participant comments
- [ ] Add external participants from the Cc header

---

## Repository settings

### General

- [ ] Sign web-based commits

### Branch defaults

#### Default branch

`main`

- [x] Auto-close referenced issues on default branch

#### Branch name template

```bash
%{id}-%{title}
```

Branches created from issues follow `{issue-id}-{issue-title}`.

### Branch rules

#### All branches

Squash commits: Allow

#### main (default protected)

Allowed to merge: Maintainers
Allowed to push and merge: No one

##### Branch rule details

**Protect branch:**

Allowed to merge:

- [x] Maintainers
- [ ] Developers and Maintainers
- [ ] No one

Allowed to push and merge:
Changes require a merge request. The following users can push and merge directly.

- [ ] Maintainers
- [ ] Developers and Maintainers
- [x] No one

- [ ] Allow force push

### Protected tags

| Tag | Allowed to create |
| --- | ----------------- |
| `*` | No one |
| `v*` | Maintainers |

---

## Merge requests

### Merge method

- [ ] Merge commit
- [x] Merge commit with semi-linear history
  Merging is only allowed when the source branch is up-to-date with its target.
  When semi-linear merge is not possible, the user is given the option to rebase.
- [ ] Fast-forward merge

> Enforces rebase before merge. Developers who don't rebase are prompted to do so, without making it their sole responsibility.

### Merge options

- [ ] Automatically resolve merge request diff threads when they become outdated
- [x] Show link to create or view a merge request when pushing from the command line
- [x] Enable "Delete source branch" option by default

### Squash commits when merging

- [ ] Do not allow
- [x] Allow
  Checkbox is visible and unselected by default.
- [ ] Encourage
- [ ] Require

> Developers squash manually via `git rebase -i` before pushing. The option exists as a fallback.

### Merge checks

- [x] Pipelines must succeed
  - [ ] Skipped pipelines are considered successful
- [x] All threads must be resolved

### Merge suggestions

```bash
review: apply %{suggestions_count} suggestion(s) to %{files_count} file(s)

%{co_authored_by}
```

### Merge commit message template

```bash
Merge branch '%{source_branch}' into '%{target_branch}'

%{title}

%{issues}
MR %{local_reference}
%{approved_by}
%{merged_by}
```

### Squash commit message template

```bash
%{title}

%{issues}
```

> `%{approved_by}`, `%{merged_by}`, `%{issues}`, `%{co_authored_by}` are omitted automatically when empty.

---

## CI/CD Settings

### General pipelines

- [x] Project-based pipeline visibility
- [x] Auto-cancel redundant pipelines
- [x] Prevent outdated deployment jobs
  - [ ] Allow job retries for rollback deployments
- [ ] Use separate caches for protected branches

### CI/CD configuration file

`.gitlab-ci.yml`

### Git strategy

- [ ] git clone
- [x] git fetch
  Re-use the project workspace. Falls back to clone if workspace doesn't exist.

Git shallow clone: `20`

Timeout: `1h`

Automatic pipeline cleanup: empty (never)

### Artifacts

- [x] Keep artifacts from most recent successful jobs

### Variables

#### Minimum role to use pipeline variables

- [ ] No one allowed
- [ ] Owner
- [ ] Maintainer
- [x] Developer

#### Access protected resources in merge request pipelines

- [x] Allow merge request pipelines to access protected variables and runners
  Only when both source and target branches are protected.

#### Display manually-defined pipeline variables

- [ ] Display pipeline variables
  Security risk — never enable if variables contain secrets.

---

**[Back to GitLab Configuration][gitlab]**

[handbook]: ../README.md
[gitlab]: ./README.md
