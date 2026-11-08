# CI Pipelines

> **[Handbook][handbook]** › **[Policies][policies]** › CI Pipelines

General rules for the GitLab CI pipelines of Zairakai projects. They set the principles, not the exact stages or job names: each ecosystem (PHP, JS, Docker images) keeps its own pipeline.

---

## Quality Gate

- The pipeline is green before any merge (see [Pull Requests][pull-requests]).
- A quality job never uses `allow_failure: true`: a check that cannot block is removed or fixed.
- The outdated dependency check runs in a **scheduled** pipeline only, never on push (see [Dependency Management][dependency-management]).

---

## Segmentation

A pipeline is split so that each part can be read, changed and reused on its own.

- Applications and packages do not copy a pipeline: they `include:` the shared one of their ecosystem (`laravel-dev-tools`, `js-dev-tools`) with a `ref:` pinned to a tag.
- One job per role (lint, test, build, scan), grouped in stages.
- A large pipeline is split in included files (for example one file for the scheduled jobs) rather than grown in a single one.

---

## Images

- Jobs of a PHP or Node project run on the Zairakai images (`zairakai/php`, `zairakai/node`).
- A third-party tool image (Alpine, ShellCheck, Hadolint, the release CLI, Kaniko) is pinned to a version tag and declared once in a `variables:` block.
- Docker images are built with **Kaniko**. Docker-in-Docker is never used.

---

## Versions

The version comes from the git tag only. No manual bump in a config file: the pipeline reads the tag at release time (see [Versioning][versioning]). For an npm package, `package.json` stays at `0.0.0` in the repository.

---

## Secrets

No secret in a pipeline file: tokens and keys come from CI/CD variables (or `CI_JOB_TOKEN` for the project's own registry).

---

**[Back to Policies][policies]**

[handbook]: ../README.md
[policies]: ./README.md
[pull-requests]: ./pull-requests.md
[dependency-management]: ./dependency-management.md
[versioning]: ./versioning.md
