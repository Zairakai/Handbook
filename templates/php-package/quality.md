# Quality & CI — php-package

> **[Handbook][handbook]** › **[Templates][templates]** › **[php-package][pkg]** › Quality & CI

---

## laravel-dev-tools integration

All tooling is delegated to `zairakai/laravel-dev-tools`. It runs automatically via Composer hooks:

```json
"post-install-cmd": ["@setup-dev-tools"],
"post-update-cmd":  ["@setup-dev-tools"]
```

`@setup-dev-tools` calls `setup-package.sh --silent`, which creates the Makefile stub and ensures configs are in place. Hash-protected — user modifications in `config/dev-tools/` are never overwritten on update.

---

## Make targets

```bash
make quality        # markdownlint + shellcheck + rector + pint + phpstan + insights
make quality-fast   # pint + phpstan + markdownlint (fast CI check)
make quality-fix    # markdownlint-fix + rector-fix + pint-fix
make test           # PHPUnit (Unit + Feature)
make test-unit      # tests/Unit/ only
make test-feature   # tests/Feature/ only
make test-coverage  # PHPUnit with coverage → build/coverage/ + build/logs/
make bats           # BATS shell tests
make ci             # quality + test + bats
make phpstan        # PHPStan only
make phpstan-baseline  # generate / update config/dev-tools/baseline.neon
make pint           # Pint check
make pint-fix       # Pint auto-fix
make rector         # Rector dry-run
make rector-fix     # Rector apply
make insights       # PHPInsights analysis
make doctor         # environment diagnostics
```

See [laravel-dev-tools make targets][php-make] for the full reference.

---

## PHPStan

`phpstan.neon` at project root:

```neon
includes:
    - vendor/zairakai/laravel-dev-tools/config/library.neon
    - config/dev-tools/baseline.neon
```

Inherits from `library.neon`: level max, `src/` path, 4 parallel processes.

### Baseline

`config/dev-tools/baseline.neon` starts empty:

```neon
parameters:
    ignoreErrors: []
```

To generate after initial setup (e.g. when integrating an existing codebase):

```bash
make phpstan-baseline
```

Never commit a non-empty baseline for new code. An empty baseline is the correct state for a new package.

---

## phpunit.xml

```xml
<testsuites>
    <testsuite name="Unit"><directory>tests/Unit</directory></testsuite>
    <testsuite name="Feature"><directory>tests/Feature</directory></testsuite>
</testsuites>

<source>
    <include><directory>src</directory></include>
</source>

<coverage>
    <report>
        <clover    outputFile="build/logs/clover.xml"/>
        <cobertura outputFile="build/logs/cobertura.xml"/>
        <html      outputDirectory="build/coverage"/>
        <text      outputFile="php://stdout" showUncoveredFiles="false"/>
    </report>
</coverage>

<logging>
    <junit outputFile="build/logs/junit.xml"/>
</logging>
```

### Environment variables (test isolation)

```xml
<env name="APP_ENV"               value="testing"/>
<env name="BCRYPT_ROUNDS"         value="4"/>
<env name="CACHE_STORE"           value="array"/>
<env name="QUEUE_CONNECTION"      value="sync"/>
<env name="SESSION_DRIVER"        value="array"/>
<env name="MAIL_MAILER"           value="array"/>
<env name="PULSE_ENABLED"         value="false"/>
<env name="TELESCOPE_ENABLED"     value="false"/>
```

---

## Git hooks

Installed to `.githooks/` via `setup-package.sh --with-hooks`:

| Hook | Trigger | Action |
| :--- | :--- | :--- |
| `commit-msg` | Every commit | Validates Conventional Commits format + ticket ID |
| `pre-commit` | Before commit | `make quality-fast` |
| `pre-push` | Before push | `make quality` |

```bash
git commit --no-verify   # bypass pre-commit (justified cases only)
git push --no-verify     # bypass pre-push (justified cases only)
```

---

## GitLab CI

```yaml
include:
  - project: 'zairakai/php-packages/laravel-dev-tools'
    ref: 3.0.0
    file: '.gitlab/ci/pipeline-php-package.yml'

variables:
  CACHE_KEY: "{{PACKAGE_SLUG}}"
  PACKAGIST_PACKAGE: "zairakai/{{PACKAGE_SLUG}}"
```

### Pipeline stages

| Stage | What runs |
| :--- | :--- |
| `lint` | `make quality-fast` (Pint + PHPStan + Markdownlint) |
| `test` | `make test` + coverage upload |
| `quality` | `make insights` + `make rector` |
| `publish` | Packagist webhook — triggered on git tag only |

### Updating the pipeline ref

When `laravel-dev-tools` releases a new version, the `DevToolsPlugin` updates the `ref:` in `.gitlab-ci.yml` automatically on `composer update`.

---

**[Back to php-package][pkg]**

[handbook]: ../../README.md
[templates]: ../README.md
[pkg]: ./README.md
[php-make]: ../../packages/laravel-dev-tools/make.md
