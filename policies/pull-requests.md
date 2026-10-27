# Pull Requests & Merging

> **[Handbook][handbook]** › **[Policies][policies]** › Pull Requests

How to title and describe a pull request (GitHub) or merge request (GitLab), and in which order branches are merged. Commit format, branch names and tags are defined in [Git Rules][git-rules]; this page does not repeat them.

---

## 📝 Request Title

The title follows the commit convention. For a single-commit request, it is the commit subject.

```text
feat(auth): #102 add OAuth2 provider support
fix(api): #87 handle null response from payment gateway
Draft: feat(export): #51 add CSV export
```

- Unfinished work is opened as `Draft:` (see [Git Rules][git-rules]).
- Remove `Draft:` only when the quality gate passes.

---

## 📄 Description

Every request carries three parts, in this order:

1. **Summary**: what changes and why, in a few lines.
2. **Not validated**: what was not run or not checked, stated plainly. Write `Nothing` only when everything was verified.
3. **Ticket link** at the bottom: `Related to #N`, or `Closes #N` when the request completes the ticket.

### Format

```markdown
## Summary

<what changes and why>

## Not validated

- <what was not run or not checked>

Related to #<ticket>
```

### Full Example

```markdown
## Summary

Adds token-bucket rate limiting on unauthenticated API routes to prevent
abuse. The limit is configurable per environment with
`RATE_LIMIT_PER_MINUTE` (default: 60).

## Not validated

- Load test under production-like traffic (not run).
- The new 429 response body against the mobile client (not checked).

Closes #310
```

- Add the labels that already exist on the project, never create new ones.
- Assign the author. Assign the linked ticket too.
- A long description lives in a file (`--body-file` with `gh pr create` or `glab mr create`), not in a one-line command.

---

## 🔀 Merging

| Rule | Detail |
| :--- | :--- |
| **Merge commits only** | Never squash: the history of each branch stays readable. |
| **Feature and fix branches** | Merged into `develop`, one at a time, each with a green pipeline. |
| **Release** | One request `develop` → `main` once the whole batch is done. |
| **Back-merge** | After a release, `main` is merged back into `develop`. |
| **Who merges** | The maintainer. A contributor, human or tool, does not merge without an explicit agreement for that request. |

### Several Branches at Once

When many branches are in flight, merge only the chosen ones into `develop`, then open a single release request. Never push to `main` after each merged branch.

```text
feature/#42-user-authentication ─┐
fix/#87-null-payment-response   ─┼─▶ develop   (one request each, green pipeline)
feature/#51-export-csv          ─┘
                                     │
                                     └─▶ main   (one release request, batch complete)
                                           │
                                           └─▶ develop   (back-merge after the release)
```

### Closing Tickets

`Closes #N` is only read on the default branch: a request into `develop` does not close the ticket. Close it when the release reaches `main`, or close it by hand.

---

## ✅ Checks

Wait for the pipeline to be green before merging.

```bash
gh pr checks 310 --watch
```

A check that is non-blocking, validated beforehand or explicitly waived by the maintainer does not hold the work.

Tags and releases are reserved to maintainers: see [Versioning][versioning].

---

**[Back to Policies][policies]**

[handbook]: ../README.md
[policies]: ./README.md
[git-rules]: ./git-rules.md
[versioning]: ./versioning.md
