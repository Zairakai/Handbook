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

Tags follow the format **`vMAJOR.MINOR.PATCH`** — always prefixed with `v`:

```bash
git tag v3.2.1
git push origin v3.2.1
```

Never tag directly on `main` without a passing CI pipeline.

---

## Release Process

### 1. Prepare

```bash
# Create a release branch if needed
git checkout -b chore/#TICKET-release-v3.2.1

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
git tag v3.2.1
git push origin main --tags
```

### 5. Publish

| Ecosystem | Trigger | Registry |
| :--- | :--- | :--- |
| PHP (Composer) | Git tag pushed → GitLab CI auto-notifies Packagist | [packagist.org] |
| NPM | Git tag pushed → GitLab CI runs `npm publish` | [npmjs.com] |

No manual publish — the CI pipeline handles it on tag.

---

## Changelog

There is no `CHANGELOG.md` file. The changelog is **generated automatically** by the CI pipeline during the release process and published as the body of the GitLab Release attached to the tag.

Each release body is built from the Conventional Commits between the previous tag and the new one — this is why commit message discipline matters.

---

## Constraints in composer.json / package.json

### PHP packages

```json
"require": {
    "php": "^8.3",
    "laravel/framework": "^11.0 || ^12.0"
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
