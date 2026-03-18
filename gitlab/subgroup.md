# Subgroup Settings

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Subgroup Settings

Default settings for subgroups (example: **Dockers**). Subgroups inherit root group settings unless overridden here.

---

## General settings

### Visibility level

- [ ] Private
- [ ] Internal
- [x] Public

---

### Permissions and group features

#### Permissions

- [x] Projects in this group cannot be shared with other groups
  Applied to all subgroups unless overridden by a group owner.

- [x] Group mentions are disabled
  Group members are not notified if the group is mentioned.

#### Email notifications

- [x] Enable email notifications
  - [x] Include diff previews

#### Expiry notification emails (locked)

- [x] All direct and inherited members of the group or project
- [ ] Only direct members of the group or project

#### Large File Storage

- [x] Projects in this group can use Git LFS

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

#### Two-factor authentication

- [x] All users in this group must set up two-factor authentication

**Delay 2FA enforcement (hours):** `48`

#### Membership

- [x] Users can request access
- [ ] Users cannot be added to projects in this group

#### Pages public access

- [ ] Remove public access

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

## CI/CD Settings

### Variables

No subgroup-level variables. All CI/CD variables are defined at the root group level and inherited.

### Runners

- [ ] Enable instance runners for this group
- [x] Allow projects and subgroups to override the group setting

### Auto DevOps

- [ ] Default to Auto DevOps pipeline for all projects within this group

---

## Packages and registries settings

### Duplicate packages

Inherits root group policy — all duplicates disabled.

### Package forwarding

Inherits root group policy — all forwarding disabled.

### Dependency Proxy

Cache container images from Docker Hub to speed up builds and reduce external bandwidth usage.

- [ ] Enable Dependency Proxy
- [x] Automatic cache cleanup  
  Automatically remove cached images older than 90 days.

---

**[Back to GitLab Configuration][gitlab]**

[handbook]: ../README.md
[gitlab]: ./README.md
