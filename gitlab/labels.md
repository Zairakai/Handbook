# Labels

> **[Handbook][handbook]** › **[GitLab Configuration][gitlab]** › Labels

Labels applied to issues and merge requests across the Zairakai organization.
Scoped labels (`::`) are mutually exclusive within their scope.

---

## Main group `zairakai`

> Available in all subgroups and projects.

### Kind::

Aligned with conventional commits. Mutually exclusive.

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Kind::Feature` | New feature to implement | `#0052CC` |
| `Kind::Bug` | Bug or regression to fix | `#CC0000` |
| `Kind::Chore` | Technical task with no functional impact (deps, config, build) | `#767676` |
| `Kind::Docs` | Documentation creation or update | `#E6A817` |
| `Kind::Refactor` | Code refactoring without behavior change | `#8250DF` |
| `Kind::Performance` | Performance improvement | `#2DA44E` |
| `Kind::Security` | Security fix or improvement | `#CF222E` |
| `Kind::Test` | Test addition or fix | `#0597A7` |
| `Kind::CI` | CI/CD pipeline changes | `#E36209` |

### Priority::

Mutually exclusive. The 3 `Priority::` labels are set as prioritized labels.

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Priority::Critical` | Blocking, requires immediate action | `#B91C1C` |
| `Priority::High` | Must be addressed soon, next sprint | `#C2410C` |
| `Priority::Low` | Can wait, backlog | `#6B7280` |

### Status::

Mutually exclusive. Todo and Done are handled by issue open/close state.

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Status::Discussion` | Under discussion or specification | `#0891B2` |
| `Status::Ready` | Refined and ready to be picked up | `#059669` |
| `Status::In Progress` | Currently being worked on | `#2563EB` |
| `Status::In Review` | MR open, awaiting review | `#7C3AED` |
| `Status::Blocked` | Blocked by an external dependency | `#DC2626` |
| `Status::On Hold` | Voluntarily paused, awaiting a decision | `#6B7280` |

### Area::

Technical domain. Not mutually exclusive — multiple areas can apply.

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Area::Frontend` | User interface, Vue components | `#F59E0B` |
| `Area::Backend` | Server-side logic | `#059669` |
| `Area::API` | API endpoints, third-party integrations | `#2563EB` |
| `Area::Database` | Database, Redis, migrations | `#7C3AED` |
| `Area::CI/CD` | Pipeline, GitLab CI, automation | `#E36209` |
| `Area::UI/UX` | Design, ergonomics, user experience | `#EC4899` |
| `Area::Security` | Cross-cutting security concerns | `#CF222E` |
| `Area::Testing` | Test coverage, fixtures, test helpers | `#0891B2` |
| `Area::Config` | Configuration, environment variables | `#6B7280` |
| `Area::Documentation` | Docs, README, guides | `#D97706` |
| `Area::Infrastructure` | Servers, VPS, network, hosting | `#0F766E` |
| `Area::Monitoring` | Logs, metrics, observability, alerts | `#6D28D9` |
| `Area::Makefile` | Makefile tasks and targets | `#427819` |
| `Area::Shell` | Shell scripts | `#4EAA25` |

### Standalone

Cross-cutting flags. Can be combined freely with any other label.

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Breaking-change` | Implies a major version bump | `#DC2626` |
| `Duplicate` | Duplicate of an existing issue | `#6B7280` |
| `Good-first-issue` | Good entry point for a new contributor | `#059669` |
| `Help-wanted` | External contribution welcome | `#7C3AED` |
| `Invalid` | Invalid or out-of-scope issue | `#9CA3AF` |
| `Needs-investigation` | Requires in-depth analysis before action | `#D97706` |
| `Regression` | Previously working behavior that broke | `#C2410C` |
| `Technical-debt` | Technical debt to address | `#92400E` |
| `Upstream` | Issue caused by an external dependency | `#4B5563` |
| `Wontfix` | Will not be addressed (out of scope or deliberate decision) | `#6B7280` |

---

## Subgroup `php-packages`

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Area::PHPStan` | Static analysis issues | `#4A90D9` |
| `Area::Insights` | PHP Insights quality issues | `#F4645F` |
| `Area::Rector` | Automated refactoring issues | `#E74C3C` |
| `Area::Pint` | Code style issues | `#F05340` |

---

## Subgroup `npm-packages`

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Area::Vue` | Vue.js components or plugins | `#42B883` |
| `Area::TypeScript` | Types, interfaces, tsconfig | `#3178C6` |
| `Area::SCSS` | Styles, variables, mixins | `#CC6699` |
| `Area::ESLint` | Linting rules and configuration | `#4B32C3` |
| `Area::Stylelint` | Style linting rules and configuration | `#D7B8F3` |
| `Area::Vitest` | Unit test runner issues | `#6E9F18` |
| `Area::Knip` | Dead code and unused exports detection | `#F97316` |

---

## Subgroup `applications`

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Area::Auth` | Authentication, authorization | `#F59E0B` |
| `Area::Cache` | Caching, Redis | `#0891B2` |
| `Area::Queue` | Jobs, workers, queues | `#7C3AED` |
| `Area::Storage` | Files, S3, disks | `#059669` |
| `Area::Notifications` | Emails, webhooks, alerts | `#EC4899` |
| `Area::PHPStan` | Static analysis issues | `#4A90D9` |
| `Area::Insights` | PHP Insights quality issues | `#F4645F` |
| `Area::Rector` | Automated refactoring issues | `#E74C3C` |
| `Area::Pint` | Code style issues | `#F05340` |
| `Area::ESLint` | Linting rules and configuration | `#4B32C3` |
| `Area::Stylelint` | Style linting rules and configuration | `#D7B8F3` |
| `Area::Vitest` | Unit test runner issues | `#6E9F18` |
| `Area::Knip` | Dead code and unused exports detection | `#F97316` |

---

## Subgroup `dockers`

Inherits from the main group — no specific labels.

---

## Subgroup `templates`

Inherits from the main group — no specific labels.

---

## Individual projects

### `daemon`

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Area::Twitch` | Twitch API, webhooks, EventSub | `#9146FF` |
| `Area::Discord` | Discord bot, commands, webhooks | `#5865F2` |
| `Area::Overlay` | Stream overlays, OBS browser sources | `#1A1A2E` |
| `Area::WebSocket` | Real-time events, alerts, stream interactions | `#010101` |
| `Area::Automation` | Triggers, automated actions | `#E36209` |

### `nexus`

| Label | Description | Color |
| ----- | ----------- | ----- |
| `Area::PDF` | PDF generation, LaTeX rendering | `#DC2626` |
| `Area::Export` | File export, format conversion | `#059669` |
| `Area::Import` | File import, parsing, validation (VCF, JSON...) | `#2563EB` |

---

**[Back to GitLab Configuration][gitlab]**

[handbook]: ../README.md
[gitlab]: ./README.md
