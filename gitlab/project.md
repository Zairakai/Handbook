# Project Settings

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Project Settings

Default settings applied to all projects. Settings marked **override** must be adjusted per project type.

---

## General Settings

### Visibility, project features, permissions

#### Project visibility

- [x] Public
- [ ] Private

#### Additional options

- [x] Users can request access

#### Features

- **Work items**
  - [x] Everyone With Access
  - [ ] Enable CVE ID requests in the issue sidebar
- **Repository**
  - [x] Everyone With Access
  - **Merge requests**
    - [x] Everyone With Access
  - **Forks**
    - [ ] disabled
  - **Git Large File Storage (LFS)**
    - [ ] disabled
  - **CI/CD**
    - [x] Everyone With Access
- **Container registry**
  - [ ] disabled
    > **Override:** enable for cache purpose on Docker image repos only.
- **Analytics**
  - [x] Everyone With Access
- **Security and compliance**
  - [x] Only Project members
- **Wiki**
  - [ ] disabled
    > Use the handbook instead.
- **Snippets**
  - [ ] disabled
- **Package registry**
  - [ ] disabled
    > **Override:** enable for cache purpose on npm/Composer package repos only.
- **Model experiments**
  - [ ] disabled
- **Model registry**
  - [ ] disabled
- **Pages**
  - [ ] disabled
- **Monitor**
  - [ ] disabled
- **Environments**
  - [ ] disabled
- **Feature flags**
  - [ ] disabled
- **Infrastructure**
  - [ ] disabled
- **Releases**
  - [x] Everyone With Access

#### Email notifications

- [x] Enable email notifications
  > Inherited from group settings.
  - [x] Include diff previews
    > Inherited from group settings.
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

> **Override per subgroup:**
>
> | Subgroup | Default branch | Reason |
> | :------- | :------------- | :----- |
> | `php-packages` | `main` | Published version on Packagist |
> | `npm-packages` | `main` | Published version on npm |
> | `dockers` | `main` | Published version on Docker Hub |
> | `applications` | `develop` | No public release — dense MR workflow |
> | `templates` | `main` | — |
>
> For `php-packages`, `npm-packages`, and `dockers`, `main` reflects the published, stable version visible to external users. Setting `develop` as default would expose unreleased code as the project landing page.
> For `applications`, `develop` is the active integration branch and the natural target for feature MRs.

#### Branch name template

```bash
%{id}-%{title}
```

Branches created from issues follow `{issue-id}-{issue-title}`.

### Branch rules

| Branch | Allowed to merge | Allowed to push and merge | Force push |
| ------ | ---------------- | ------------------------- | ---------- |
| `main` | Maintainers | No one | ✗ |
| `develop` | Maintainers | No one | ✗ |
| `release/*` | Maintainers | Maintainers | ✗ |

> Force push allowed on working branches to facilitate rebase before merge.
> Avoid abusing it.

### Protected tags

| Tag | Allowed to create |
| --- | ----------------- |
| `*` | No one |
| `v*` | Maintainers |

---

## Merge requests

### Merge method

- [x] Merge commit
- [ ] Merge commit with semi-linear history
- [ ] Fast-forward merge

> Standard three-way merge commit. No rebase required before merging.
>
> Semi-linear history is incompatible with Gitflow's `develop`→`main` pattern:
> each merge commit on `main` is never back-propagated to `develop`, so every
> subsequent `develop`→`main` MR triggers a mandatory rebase. This creates
> duplicate-SHA commits (rebased copies with same content, different hash) and
> produces a misleading graph. Regular merge commit avoids all of this while
> keeping a clean, readable Gitflow graph.

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

```bash
.gitlab-ci.yml
```

### Git strategy

- [ ] git clone
- [x] git fetch  
  Re-use the project workspace. Falls back to clone if workspace doesn't exist.

**Git shallow clone:** `20`

**Timeout:** `1h`

**Automatic pipeline cleanup:** `90 days`

### Auto DevOps

- [ ] Default to Auto DevOps pipeline

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

### Job token permissions

- [ ] Allow Git push requests to the repository

---

## Package Registry

> **Override:** enable on npm/Composer package repos only. Used as a CI cache registry — not for production packages (published to npm and Packagist).

### Duplicate assets

**Number of duplicate assets to keep:** `10`

---

## Container Registry

> **Override:** enable on Docker image repos only. Used as a CI cache registry — not for production images (published to Docker Hub).

### Cleanup policy

- [x] Enable cleanup policy

**Run cleanup:** Every day

**Keep the most recent:** `5` tags per image name

**Keep tags matching:** empty

**Remove tags older than:** `90` days

**Remove tags matching:** `.*`

---

## Monitor Settings

### Error tracking

- [ ] Enable error tracking

### Incidents

- [ ] PagerDuty integration

---

**[Back to GitLab Configuration][gitlab]**

[handbook]: ../README.md
[gitlab]: ./README.md
