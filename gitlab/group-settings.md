# Group Settings

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Group Settings

Baseline settings applied to the root Zairakai group. Subgroups inherit these unless explicitly overridden.

---

## Permissions

| Setting | Value | Reason |
| :--- | :--- | :--- |
| Projects can be shared with other groups | ❌ | Keeps projects contained within the organization |
| Group mentions | ✅ Enabled | Members notified on group mention |
| Minimum role to create projects | **Maintainers** | Prevents ad-hoc project proliferation |
| Roles allowed to create subgroups | **Owners only** | Organizational structure is intentional |
| Users can request access | ❌ | Closed group — access is granted, not requested |

---

## Two-Factor Authentication

| Setting | Value |
| :--- | :--- |
| 2FA required | ✅ All members |
| Grace period | 48 hours |

---

## Email Notifications

| Setting | Value |
| :--- | :--- |
| Email notifications | ✅ Enabled (group + subgroups + projects) |
| Include diff previews | ✅ |
| Token expiry notifications | All direct and inherited members |

---

## Repository

| Setting | Value |
| :--- | :--- |
| Default branch name | `main` |
| Branch protection | ✅ Protected on creation |
| Allowed to push to `main` | **Maintainers** |
| Allowed to merge to `main` | **Maintainers** |
| Force push | ❌ |
| Code owners approval | ❌ |
| Git LFS | ✅ Enabled (overridable per project) |

---

## Package Registry

### Duplicate packages

All disabled — semver immutability: a published version is never overwritten.

| Registry | Allow duplicates |
| :--- | :--- |
| Maven | ❌ |
| Generic | ❌ |
| NuGet | ❌ |
| Terraform module | ❌ |

### Package forwarding

All disabled — supply chain security. If a package is not in the GitLab registry, the build fails explicitly. No silent fallback to public registries.

| Registry | Forward requests |
| :--- | :--- |
| npm | ❌ |
| PyPI | ❌ |

---

**[Back to GitLab Configuration][gitlab]**

[handbook]: ../README.md
[gitlab]: ./README.md
