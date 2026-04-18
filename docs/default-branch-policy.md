# Default Branch Policy

## Rule

| Project type | Default branch |
|---|---|
| PHP packages | `main` |
| NPM packages | `main` |
| Templates | `main` |
| Handbook | `main` |
| Applications (daemon, nexus) | `develop` |

## Rationale

Packages and templates are always in a releasable state — `main` is the stable branch, the latest published version.
CI publishes to Packagist/NPM automatically on tag, which is always cut from `main`.

Applications use `develop` as the integration branch where features and fixes land first.
`main` on applications is reserved for production-ready releases only.

## Enforcement

Configured per-project in GitLab under **Settings > Repository > Default branch**.

Branch protection rules (applied on all projects):

| Branch | Merge | Push | Force push |
|---|---|---|---|
| `main` | Maintainers only | No one | No |
| `develop` (applications only) | Maintainers only | No one | No |

Feature/fix branches (`feature/*`, `fix/*`, `hotfix/*`, `wip/*`) are unprotected and auto-deleted after merge.
