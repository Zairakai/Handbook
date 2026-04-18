# GitLab Configuration

> **[Handbook][handbook]** › GitLab Configuration

Reference settings for GitLab groups and projects in the Zairakai organization. These settings define the baseline — individual projects may override where noted.

---

## Sections

| Page | Contents |
| :--- | :--- |
| **[Group Settings][group]** | Permissions, 2FA, branch protection defaults, package registry |
| **[Subgroup Settings][subgroup]** | Inherited defaults, visibility, runners, package registry |
| **[Project Settings][project]** | Visibility, branch rules, merge method, CI/CD, commit templates |
| **[Labels][labels]** | Label taxonomy — Kind, Priority, Status, Area, Standalone |
| **[Work Items][work-items]** | Epic / Issue / Task hierarchy, usage rules, board setup |

---

## Principles

- **Security by default** — 2FA enforced, no force push, no direct push to `main`
- **MR-only workflow** — all changes go through merge requests with semi-linear history
- **No package forwarding** — builds fail explicitly if a package is missing, no silent public registry fallback
- **Semver immutability** — duplicate packages disabled across all registries
- **No forks** — clone + branch + MR; forks fragment history and are unnecessary with controlled team access
- **Per-project overrides** — package registry, container registry, and visibility are disabled by default and enabled per project as needed

---

**[Back to Handbook][handbook]**

[handbook]: ../README.md
[group]: ./group.md
[subgroup]: ./subgroup.md
[project]: ./project.md
[labels]: ./labels.md
[work-items]: ./work-items.md
