# Git & Commit Rules

> **[Handbook][handbook]** › **[Policies][policies]** › Git Rules

To maintain a clean, searchable, and automatable history, all Zairakai projects follow a strict commit and branching policy based on [Conventional Commits] and mandatory ticket references.

---

## 📝 Commit Message Convention

### Format

```bash
<type>(<scope>): #TICKET <subject>

<body>

<footer>
```

- **First line**: max **72 characters** — this is the commit summary visible in logs
- **Second line**: always **blank** — separates subject from body
- **Body and footer**: lines wrapped at **80 characters**
- **type** and **scope**: always lowercase

### Subject Line

```bash
feat(auth): #102 add OAuth2 provider support
fix(api): #87 handle null response from payment gateway
docs(readme): #31 update installation instructions
```

### Types

| Type | Use Case |
| :--- | :--- |
| **feat** | A new feature or significant change. |
| **fix** | A bug fix. |
| **docs** | Documentation changes only. |
| **style** | Formatting, missing semi-colons — no logic change. |
| **refactor** | Code change that neither fixes a bug nor adds a feature. |
| **perf** | A code change that improves performance. |
| **test** | Adding or correcting tests — no production code change. |
| **chore** | Build tasks, package configs — no production code change. |
| **ci** | Changes to CI configuration files and scripts. |
| **build** | Changes to the build system (Vite, Webpack, Makefile…). |

### Scope (optional)

The scope is the area of the codebase affected. Keep it short and consistent:

```bash
feat(auth):       # authentication module
fix(payments):    # payment integration
refactor(models): # Eloquent models
ci(gitlab):       # GitLab CI pipeline
build(vite):      # Vite configuration
```

Omit scope when the change is global or hard to assign to a single area.

---

## 📖 Commit Body

The body is **optional** for simple changes, **mandatory** for complex ones.

- Write in the **imperative, present tense**: "add" not "added", "fix" not "fixed"
- Explain the **motivation** for the change — what problem does it solve?
- Contrast with previous behavior when relevant

```bash
refactor(cache): #201 replace custom cache layer with Laravel Cache facade

The custom cache implementation was causing inconsistencies in TTL
handling between Redis and file drivers. Laravel's Cache facade provides
a unified interface that handles both drivers transparently.

Previously, callers had to manage driver-specific serialization manually.
```

---

## 🔖 Commit Footer

### Closing issues

Reference resolved issues in the footer — GitLab will automatically close them on merge:

```bash
Closes #234
```

Multiple issues:

```bash
Closes #123, #245, #992
```

### Breaking Changes

All breaking changes **must** be documented in the footer:

```bash
BREAKING CHANGE: The `--port-runner` option has been renamed to `--runner-port`
to align with the configuration file syntax.

Migration: replace all `--port-runner` usages with `--runner-port` in your
scripts and CI configuration.
```

### Full Example

```bash
feat(api): #310 add rate limiting to public endpoints

Implements token-bucket rate limiting on all unauthenticated API routes
to prevent abuse. The limit is configurable per environment via
RATE_LIMIT_PER_MINUTE (default: 60).

Closes #310

BREAKING CHANGE: The response format for 429 errors now follows RFC 7807
(Problem Details). Clients checking for plain-text error messages must
be updated to parse the JSON body.
```

---

## 🌿 Branching Strategy

| Direct push | Branch | Purpose |
| :--: | :--- | :--- |
| ❌ | **`main`** | Production-ready code. Merged via MR only. |
| ❌ | **`develop`** | Integration branch. Merged into `main` via MR only. |
| ✅ | **`feature/#TICKET-name`** | New features or improvements — branched from `develop`. |
| ✅ | **`fix/#TICKET-name`** | Bug fixes — branched from `develop`. |
| ✅ | **`hotfix/#TICKET-name`** | Urgent production fixes — branched from `main`. |

### Branch Naming

```bash
feature/#42-user-authentication
fix/#87-null-payment-response
hotfix/#99-critical-xss-vulnerability
```

- Always reference the ticket ID with `#`
- Use **kebab-case** for the description
- Keep it short and descriptive

---

## 🚧 Work In Progress (WIP)

When a branch is not ready for review, prefix the Merge Request title with `Draft:` in GitLab. This prevents accidental merges and signals that the work is still in progress.

```text
Draft: feat(auth): #102 add OAuth2 provider support
```

- **Do not** open a non-draft MR for unfinished work
- Remove the `Draft:` prefix only when the branch is ready for review and all quality gates pass
- A WIP commit **must** still follow the Conventional Commits format — no `wip: stuff` commits

---

## 🏷️ Tags & Releases

Version tags (`1.2.3`) are **reserved for maintainers** — contributors must not push tags. Any tag pushed by a non-maintainer will be deleted.

The full tagging and release process is defined in [Versioning][versioning].

---

## 🛠️ Automated Enforcement

Most projects include a `commit-msg` hook (deployed by `dev-tools`) that validates the commit format before the commit is created. If your commit is rejected:

1. Check the format against the rules above
2. Re-run: `git commit -m "type(scope): #TICKET subject"`

The `pre-push` hook runs the quality gate before pushing — no broken code reaches the remote.

---

**[Back to Policies][policies]**

[handbook]: ../README.md
[policies]: ./README.md
[versioning]: ./versioning.md
[Conventional Commits]: https://www.conventionalcommits.org/
