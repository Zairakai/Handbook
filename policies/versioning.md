# Versioning & Release Process

> **[Handbook][handbook]** › **[Policies][policies]** › Versioning

---

## Semantic Versioning

All Zairakai packages follow **[SemVer][semver]** strictly: `MAJOR.MINOR.PATCH`

| Segment | Increment when… | Example |
| :--- | :--- | :--- |
| **MAJOR** | A breaking change is introduced | `2.0.0` → `3.0.0` |
| **MINOR** | A new feature is added, backward-compatible | `3.1.0` → `3.2.0` |
| **PATCH** | A bug fix, backward-compatible | `3.2.0` → `3.2.1` |

### What counts as a breaking change

- Removing or renaming a public method, class, or function
- Changing a method signature (added required parameter, changed type)
- Changing the behavior of an existing feature in a way consumers depend on
- Removing a config key or changing its default value
- Dropping support for a PHP / Node version

### What does NOT count as breaking

- Adding new optional parameters with defaults
- Adding new public methods or classes
- Internal refactoring with no public API change
- Fixing a bug (even if someone depended on the incorrect behavior)

---

## Git Tags

Tags follow the format **`MAJOR.MINOR.PATCH`** — never prefixed with `v`:

```bash
git tag 3.2.1
git push origin 3.2.1
```

Never tag directly on `main` without a passing CI pipeline.

### Format check

The format is defined once, in the CI/CD variable **`VERSION_TAG_REGEX`** of the `zairakai` GitLab group (and as a repository variable on each GitHub repository):

```text
/^([0-9]+\.){2}[0-9]+(-[0-9A-Za-z.]+)?$/
```

It accepts `3.2.1` and a pre-release such as `3.2.1-rc.1`. A pipeline is created for every tag: the job `tag-format` fails when the tag does not match, and the jobs that publish only run for a tag that matches.

The protected tag rule (`*.*.*`, maintainers only) is a glob and cannot check the format: the pipeline does. Pre-releases are published on npm with the tag `next`, and are refused by the Docker image pipelines, which move `latest`.

> Tags created before this rule (`v2.2.0`, `v1.4.3`, ...) keep their `v`: they are never renamed, because projects pin them in their `.gitlab-ci.yml`. A project can therefore have both forms in its history. Composer and npm read `v2.2.0` and `2.2.0` as the same version.

---

## Release Process

### 1. Prepare

```bash
# Create a release branch if needed
git checkout -b chore/#TICKET-release-3.2.1

# Bump version in the relevant manifest
# PHP: composer.json (no "version" field — Packagist reads the tag)
# NPM: package.json
npm version patch   # or minor / major
```

### 2. README & Badges Check

Before tagging, verify `README.md` reflects the new version:

- PHP version badge matches the `require.php` constraint in `composer.json`
- Laravel version badge matches the `require.illuminate/*` or `require.laravel/framework` constraint
- Any other version references in the README (stack table, description) are accurate

This applies to **every package** in the release chain — if `laravel-dev-tools` is bumped, all downstream packages that bump in turn must also be checked.

### 3. Quality Gate

```bash
make quality && make test-all
```

The pipeline must be **100% green** before tagging.

### 4. Tag and Push

```bash
git tag -a 3.2.1 -m "3.2.1"
git push origin 3.2.1
```

### 5. Publish

| Ecosystem | Trigger | Registry |
| :--- | :--- | :--- |
| PHP (Composer) | Git tag pushed → GitLab CI auto-notifies Packagist | [packagist.org] |
| NPM | Git tag pushed → GitLab CI runs `npm publish` | [npmjs.com] |

No manual publish — the CI pipeline handles it on tag.

---

## Which changes need a tag

A tag publishes the package, so it is made when what is **published** changes, not when the repository changes.

| Change | Tag |
| :--- | :--- |
| Public API change, new feature | MINOR (MAJOR if breaking) |
| Bug fix | PATCH |
| A range of `require` (Composer) or of `dependencies` / `peerDependencies` (NPM) changes | PATCH at least. Raising the minimum version of a dependency, or dropping a supported version, is breaking: MAJOR |
| Only `require-dev` / `devDependencies` change (tools, tests, documentation site) | **No tag**: the published archive is the same |
| Any dependency update of `zairakai/laravel-dev-tools` or `@zairakai/js-dev-tools` | MINOR: their dependencies (Rector, PHPStan, ESLint, Stylelint...) are their product |
| Lock files (`composer.lock`, `package-lock.json`) | Never: a published library does not ship them |

The two dev-tools packages are installed by the other packages in `require-dev` / `devDependencies` only. A project that installs `@zairakai/js-utils` never receives `@zairakai/js-dev-tools`, so updating it does not force a tag in the projects that use it: they take the new version at their next update, in their own merge request. To know whether a project needs a tag, compare the sections `require`, `dependencies` and `peerDependencies` between the last tag and `HEAD`.

---

## Dropping a supported version

Support only the versions that still receive **security fixes** (PHP, Laravel, Node...). Removing a version from a constraint (`^11.0 || ^12.0 || ^13.0` becomes `^12.0 || ^13.0`) makes the package impossible to install there: it is a breaking change, so a MAJOR release, with a `BREAKING CHANGE:` footer in the commit (see [Git Rules][git-rules]).

Remove it everywhere in the same merge request: `composer.json` (the constraint and the `conflict` entry), the CI matrix, the install scripts, the README and the documentation. Laravel 11 was dropped this way: its security support ended in March 2026.

---

## Creating a tag

```bash
git switch main && git pull
git tag -a 3.2.1 -m "3.2.1"
git push origin 3.2.1
git ls-remote --tags origin 3.2.1   # it must be listed
```

- A tag is **annotated**, with a message. When `tag.gpgSign` is set, `git tag 3.2.1` alone fails with `no tag message?`.
- The pipeline of `main` must be green before tagging, and the pipeline of the tag runs the publication: check it.
- A published tag is never moved or reused. A release found wrong is replaced by a new version, and the wrong one is removed.

---

## Automatic versioning of datasets

The projects that publish data (`French-postal-code`, the builder, and `French-Postal-Code-Package`) choose the version from **what changed**, so that it can be automated:

| Segment | Builder | Package |
| :--- | :--- | :--- |
| **Z** | Only the points of the BAN changed (`latitude`, `longitude`, `address_count`) | The builder went up by a Z, or a fix of the code |
| **Y** | A file of INSEE (COG) or La Poste is newer than the last release. Z goes back to 0 | The builder went up by a Y |
| **X** | The schema of the files changed (a column) | The layout of the files changed, or a supported version is dropped |

- **Builder**: the workflow `Watch sources` checks the sources every day, and every Monday it starts a control build for the points of the BAN. The workflow `Build dataset` restores the SQL of the latest release, updates it, checks the identifiers and the schemas, and creates a **draft** release only if its export differs from the baseline release in any column. It chooses Y or Z by asking whether a source is newer. The maintainer publishes the draft: this is the single manual step.
- **Package**: the publication starts the workflow `Update data`. The manifest of `data/` holds `builder_release`. The workflow `Release data` compares it with the one of the last tag: a new Y gives a minor release, a new Z a patch release, and a new X stops the workflow and says why (a major release is tagged by hand). These tags are signed with a dedicated key (secret `GPG_PRIVATE_KEY`, identity in the variables `RELEASE_SIGNER_NAME` and `RELEASE_SIGNER_EMAIL`).
- The versions of the package and of the builder are not the same number: a fix of the code is a patch of the package only.
- The Table Schemas read by data.gouv.fr are the files of the branch `schemas` of the builder. The build checks the CSV files against them, and the publication writes the version of the release in their field `version`.

---

## Changelog

There is no `CHANGELOG.md` file. The changelog is **generated automatically** by the CI pipeline during the release process and published as the body of the GitLab Release attached to the tag.

Each release body is built from the Conventional Commits between the previous tag and the new one — this is why commit message discipline matters.

---

## Constraints in composer.json / package.json

### PHP packages

```json
"require": {
    "php": "^8.4",
    "laravel/framework": "^12.0 || ^13.0"
}
```

### NPM packages

```json
"peerDependencies": {
    "typescript": "^5.0"
}
```

- Use **caret `^`** for minor-compatible ranges
- Pin **exact versions** only for security-critical dependencies
- Declare peer dependencies as `peerDependencies`, not `dependencies`

---

**[Back to Policies][policies]**

[handbook]: ../README.md
[policies]: ./README.md
[semver]: https://semver.org/
[packagist.org]: https://packagist.org
[npmjs.com]: https://www.npmjs.com
