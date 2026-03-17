# Group Settings

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Group Settings

Baseline settings applied to the root Zairakai group. Subgroups inherit unless explicitly overridden.

---

## General settings

### Permissions and group features

#### Permissions

- [ ] Projects in this group cannot be shared with other groups
- [ ] Group mentions are disabled

#### Email notifications

- [x] Enable email notifications
  - [x] Include diff previews

#### Expiry notification emails (locked)

- [x] All direct and inherited members of the group or project
- [ ] Only direct members of the group or project

#### Large File Storage

- [x] Projects in this group can use Git LFS
  Possible to override in each project.

#### Minimum role required to create projects

- [ ] No One
- [ ] Administrator
- [ ] Owner
- [x] Maintainers
- [ ] Developers

#### Roles allowed to create subgroups

- [x] Owners
- [ ] Maintainers

#### Two-factor authentication

- [x] All users in this group must set up two-factor authentication

#### Delay 2FA enforcement (hours)

`48`

#### Membership

- [ ] Users can request access
- [ ] Users cannot be added to projects in this group

#### Customer relations

- [x] Customer relations is enabled

---

## Repository settings

### Default branch

#### Initial default branch name

`main`

#### Initial default branch protection

- [x] Protected
  - Allowed to push
    - [ ] Developers + Maintainers
    - [x] Maintainers
    - [ ] No one
  - Allowed to merge
    - [ ] Developers + Maintainers
    - [x] Maintainers
    - [ ] No one
  - [ ] Allowed to force push
  - [ ] Require approval from code owners
  - [ ] Allow developers to push to the initial commit

---

## Packages and registries settings

### Duplicate packages

Semver immutability — a published version is never overwritten. To update, publish a new version.

- Maven
  - [ ] Allow duplicates
- Generic
  - [ ] Allow duplicates
- NuGet
  - [ ] Allow duplicates
- Terraform module
  - [ ] Allow duplicates

### Package forwarding

Disabled — supply chain security. If a package is not in the GitLab registry, the build must fail explicitly. No silent fallback to public registries (dependency confusion risk).

#### npm

- [ ] Forward npm package requests
- [ ] Enforce npm setting for all subgroups

#### PyPI

- [ ] Forward PyPI package requests
- [ ] Enforce PyPI setting for all subgroups

---

**[Back to GitLab Configuration][gitlab]**

[handbook]: ../README.md
[gitlab]: ./README.md
