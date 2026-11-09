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
- A third-party tool image (Alpine, ShellCheck, Hadolint, the release CLI, Kaniko, Trivy) is pinned to a version tag and declared once, as a variable of the GitLab group of the projects that use it (`zairakai/dockers` for the Docker images), never in a pipeline file. The variables are not protected, so that merge request pipelines read them.
- A tool is never installed with `curl | sh` from a moving branch: use its official image at a pinned tag, or a pinned release.
- A version that a Dockerfile also holds (the base image of a Docker image) lives in the Dockerfile; a job checks that the group variable matches it.
- Docker images are built with **Kaniko**. Docker-in-Docker is never used.

---

## Versions

The version comes from the git tag only. No manual bump in a config file: the pipeline reads the tag at release time (see [Versioning][versioning]). For an npm package, `package.json` stays at `0.0.0` in the repository.

---

## Pipeline Time

- A scheduled pipeline runs only its own jobs, not the validation and test jobs.
- In a merge request, a job whose result depends on some files (lint of the Dockerfile, of the Markdown, of the shell scripts) runs only when they changed (`rules:changes`). Jobs that depend on it list it with `optional: true`.
- A new commit cancels the running checks of the branch (`workflow:auto_cancel`, `interruptible`). The jobs that build, scan, promote or release are never interruptible.
- What a job downloads is cached or avoided: npm packages, the vulnerability database of the scanner, a tool taken from its image instead of an install.
- A Docker image is built without push in the merge request that changes what it contains, so a broken build shows before the tag.

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
