# Gitignore

> **[Handbook][handbook]** › **[Coding Standards][standards]** › Gitignore

---

## Overview

The `.gitignore` of a repository lists what Git must never track. Keep it short, grouped and commented, so that a reader knows why each line is there.

---

## What to ignore

| Group | Examples | Why |
| :--- | :--- | :--- |
| Dependencies | `/node_modules`, `/vendor` | Restored by `npm ci` or `composer install` from the lock file. |
| Build and generated output | `/dist`, `/build`, `/coverage`, `/docs` (TypeDoc) | Rebuilt by a command. |
| Caches and logs | `.phpunit.cache`, `*.log`, `npm-debug.log*` | Local and disposable. |
| Environment and secrets | `.env`, `*.local` | Never committed. A `.env.example` is committed instead. |
| Editors, IDEs, AI assistants | the [standard block](#standard-block) | Personal files of one contributor. |

## What not to ignore

- The lock files (`package-lock.json`, `composer.lock`): they make the installs reproducible.
- `config/dev-tools/`: the published configuration is committed and versioned.
- The shared instructions for AI assistants (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`): they are documentation for the contributors.

---

## Writing the file

- **One titled block per group.** The title is a comment, followed by an underline of 16 dashes, with nothing above it. Blocks are separated by one empty line:

  ```text
  # Dependencies
  # ----------------
  /node_modules
  /vendor
  ```

- **Specific comments** (why a pattern exists) go on their own line above the pattern, never at the end of a line: Git does not accept a comment after a pattern.
- **Group order**: first the personal blocks, the same in every repository (JetBrains, Visual Studio Code, Other editors, AI assistants); then the blocks of the repository itself: Dependencies, Build output, Laravel runtime (when relevant), Tests and coverage, Caches, Logs, Environment and secrets, Temporary and backup files, Dev tools, Operating system, then the project-specific groups.
- **A trailing slash for a folder** (`.idea/`): it matches only folders.
- **A leading slash for the root** (`/vendor`): it matches only at the root of the repository, not in a sub-folder.
- **No duplicate line**: before adding a pattern, check that the file does not already hold it.
- **Never use the `.gitignore` to hide a secret that was already committed.** Ignoring a file does not remove it from the history; see the security policy.

---

## Standard block

Every repository starts its `.gitignore` with this block, then lists its own groups below. It has one titled block per editor family and one for the AI assistants. The [PHP package][php-package-structure] and [npm package][npm-package-structure] templates carry the same block.

```text
# JetBrains (PhpStorm, WebStorm)
# ----------------
.idea/
.idea_modules/
*.iml
*.ipr
*.iws

# Visual Studio Code and derivatives
# ----------------
.vscode/
.history/

# Other editors
# ----------------
.fleet/
.zed/
.vs/
*.sublime-project
*.sublime-workspace
.project
.classpath
.settings/
nbproject/
*.swp
*.swo
*~

# AI assistants
# ----------------
# Local state only, shared instructions (AGENTS.md, CLAUDE.md, GEMINI.md) stay committed
.claude/settings.local.json
CLAUDE.local.md
.aider*
.cursor/
.windsurf/
.continue/
.codex/
.gemini/
```

Notes:

- `.claude/settings.local.json` and `CLAUDE.local.md` are the **local** files of Claude Code. The shared project files of the same tool (`CLAUDE.md`, `.claude/settings.json`) stay committed.
- The AI folders of the other tools (`.cursor/`, `.windsurf/`, `.continue/`, `.codex/`, `.gemini/`) are ignored as a whole. If a project decides to share rules from one of them, it removes that line and says so in its README.
- The block is complete in every repository. A pattern the repository already had (for example `/.idea`) is moved into the block, never repeated.

### Your own global ignore file

A contributor who uses a tool that is not in the block can ignore its files for all repositories with a global file, without touching the repository:

```bash
git config --global core.excludesFile ~/.config/git/ignore
```

The repository block stays the reference: it protects every contributor, including those who have no global file.

---

## Official documentation

- [gitignore (Git reference)][git-gitignore]: syntax and precedence of the patterns.
- [github/gitignore][github-gitignore]: community templates by language and tool.

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[php-package-structure]: ../templates/php-package/structure.md
[npm-package-structure]: ../templates/npm-package/structure.md
[git-gitignore]: https://git-scm.com/docs/gitignore
[github-gitignore]: https://github.com/github/gitignore
