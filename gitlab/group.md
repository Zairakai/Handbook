# Group Settings

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Group Settings

Settings applied to the root **Zaïrakai** group. All subgroups inherit these unless explicitly overridden.

---

## General settings

### Naming, description, visibility

#### Visibility level

- [ ] Private
- [ ] Internal
- [x] Public
  The group, any public projects, and any of their members, issues, and merge requests can be viewed without authentication.

---

### Permissions and group features

#### Permissions

- [x] Members cannot invite groups outside of Zaïrakai and its subgroups
  Available only on the top-level group. Applies to all subgroups. Groups already shared with a group outside Zaïrakai are still shared unless removed manually.

- [x] Projects in Zaïrakai cannot be shared with other groups
  Applied to all subgroups unless overridden by a group owner. Groups already added to the project lose access.

- [x] Group mentions are disabled
  Group members are not notified if the group is mentioned.

#### Email notifications

- [x] Enable email notifications
  Enable sending email notifications for this group and all its subgroups and projects
  - [x] Include diff previews
    Emails are not encrypted.

#### Expiry notification emails

- [x] All direct and inherited members of the group or project
- [ ] Only direct members of the group or project
- [x] Enforce for all subgroups

#### Large File Storage

- [x] Projects in this group can use Git LFS
  Possible to override in each project.

#### Enabled git access protocols

Both SSH and HTTP(S)

#### Minimum role required to create projects

- [ ] No One
- [ ] Administrator
- [ ] Owner
- [x] Maintainers
- [ ] Developers

#### Roles allowed to create subgroups

- [x] Owners
- [ ] Maintainers

#### Prevent project forking outside current group (locked)

- [ ] Prevent forking outside of the group

#### Personal access tokens

- [x] Require expiration dates for service accounts

#### Two-factor authentication

- [x] All users in this group must set up two-factor authentication

**Delay 2FA enforcement (hours):** `48`

- [x] Allow more restrictive 2FA enforcement for subgroups

#### Membership

- [x] Users can request access (if visibility is public or internal)
- [ ] Users cannot be added to projects in this group

**Seat control:** Open access — invitations do not need to be approved by a group owner.

- [ ] Remove dormant members after a period of inactivity

#### Pages public access

- [ ] Remove public access

#### Customer relations

- [x] Customer relations is enabled

---

## Repository settings

### General

- [ ] Sign web-based commits

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

## CI/CD Settings

### General pipelines

- [x] Enable JWT format for CI/CD job tokens
  Scheduled to be mandatory in GitLab 19.0.

### Variables

**Default role to use pipeline variables:**

- [ ] No one allowed
- [ ] Owner
- [ ] Maintainer
- [x] Developer

### Runners

- [ ] Enable instance runners for this group
- [x] Allow projects and subgroups to override the group setting
- [ ] Allow members to create runners with runner registration tokens

### Auto DevOps

- [ ] Default to Auto DevOps pipeline for all projects within this group

---

## Packages and registries settings

### Duplicate packages

Semver immutability — a published version is never overwritten. To update, publish a new version.

- [ ] Maven
- [ ] Generic
- [ ] NuGet
- [ ] Terraform module

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
