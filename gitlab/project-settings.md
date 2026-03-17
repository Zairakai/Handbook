# Project Settings

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Project Settings

Default settings applied to all projects. Some settings are intentionally disabled by default and must be enabled per project as needed.

---

## Visibility

| Setting | Default | Override |
| :--- | :--- | :--- |
| Project visibility | **Public** | Applications → Private |
| Users can request access | ❌ | — |
| Forks | ❌ | — |

> Forks are disabled.  
> The workflow is: clone + branch + MR. Forks fragment history and are unnecessary with controlled team access.

---

## Features

| Feature | Enabled | Access |
| :--- | :--- | :--- |
| Issues | ✅ | Everyone With Access |
| Repository | ✅ | Everyone With Access |
| Merge requests | ✅ | Everyone With Access |
| CI/CD | ✅ | Everyone With Access |
| Analytics | ✅ | Everyone With Access |
| Releases | ✅ | Everyone With Access |
| Security & compliance | ✅ | **Project members only** |
| Package registry | ❌ | Enable on package repos |
| Container registry | ❌ | Enable on Docker repos |
| Wiki | ❌ | Use handbook instead |
| Snippets | ❌ | — |
| LFS | ❌ | Enable as needed |
| Pages | ❌ | Enable as needed |
| Monitor / Environments / Feature flags | ❌ | Enable as needed |

---

## General

| Setting | Value |
| :--- | :--- |
| Warn about Potentially Unwanted Characters | ✅ |
| Sign web-based commits | ❌ |
| Email notifications (project level) | ❌ (inherited from group) |
| Show default emoji reactions | ✅ |

---

## Branch Rules

### `main` (default, protected)

| Rule | Value |
| :--- | :--- |
| Allowed to merge | **Maintainers** |
| Allowed to push directly | **No one** — MR required |
| Force push | ❌ |

### All branches

| Rule | Value |
| :--- | :--- |
| Squash commits | Allow (unselected by default) |

### Protected tags

| Pattern | Allowed to create |
| :--- | :--- |
| `*` (all tags) | **No one** |
| `v*` (version tags) | **Maintainers** |

### Branch name template

```bash
%{id}-%{title}
```

Branches created from issues follow `{issue-id}-{issue-title}`.

---

## Merge Requests

### Merge method

**Merge commit with semi-linear history** — enforces rebase before merge, preserves a merge commit for traceability. Developers who don't rebase are prompted to do so.

### Merge options

| Setting | Value |
| :--- | :--- |
| Auto-resolve outdated diff threads | ❌ |
| Show MR link when pushing from CLI | ✅ |
| Delete source branch by default | ✅ |

### Squash

**Allow** — visible, unselected by default. Developers squash manually via `git rebase -i` before pushing. The option exists as a fallback.

### Merge checks

| Check | Value |
| :--- | :--- |
| Pipelines must succeed | ✅ |
| Skipped pipelines count as success | ❌ |
| All threads must be resolved | ✅ |

### Commit message templates

**Merge commit:**

```bash
Merge branch '%{source_branch}' into '%{target_branch}'

%{title}

%{issues}
MR %{local_reference}
%{approved_by}
%{merged_by}
```

**Squash commit:**

```bash
%{title}

%{issues}
```

**Applied suggestions:**

```bash
review: apply %{suggestions_count} suggestion(s) to %{files_count} file(s)

%{co_authored_by}
```

> `review:` prefix identifies suggestion commits in `git log`. `%{approved_by}`, `%{merged_by}`, `%{issues}`, `%{co_authored_by}` are omitted automatically when empty.

---

## CI/CD

### Pipelines

| Setting | Value |
| :--- | :--- |
| Pipeline visibility | Project-based (follows project visibility) |
| Auto-cancel redundant pipelines | ✅ |
| Prevent outdated deployment jobs | ✅ |
| Allow retries for rollback deployments | ❌ |
| Separate caches for protected branches | ❌ |
| CI/CD configuration file | `.gitlab-ci.yml` |

### Git strategy

| Setting | Value |
| :--- | :--- |
| Strategy | **git fetch** (reuse workspace, faster than clone) |
| Shallow clone depth | **20** |
| Job timeout | **1 hour** |
| Automatic pipeline cleanup | Disabled |

### Artifacts

| Setting | Value |
| :--- | :--- |
| Keep artifacts from most recent successful jobs | ✅ |

### Variables

| Setting | Value |
| :--- | :--- |
| Minimum role to use pipeline variables | **Developer** |
| MR pipelines access protected variables | ✅ (source + target both protected) |
| Display manually-defined variables | ❌ (security risk) |

---

**[Back to GitLab Configuration][gitlab]**

[handbook]: ../README.md
[gitlab]: ./README.md
