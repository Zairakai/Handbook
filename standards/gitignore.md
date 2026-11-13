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
# Personal instruction files: not published (the KB repositories force them with exceptions)
AGENTS.md
CLAUDE.md
GEMINI.md
*.local.md
.claude/settings.local.json
.aider*
.cursor/
.windsurf/
.continue/
.codex/
.gemini/
```

Notes:

- **The instruction files of the AI assistants are personal.** `AGENTS.md`, `CLAUDE.md` and `GEMINI.md` are ignored, so that a contributor does not publish them by mistake. A personal file that a tool reads next to the shared one goes in a `*.local.md` file (`CLAUDE.local.md` is read by Claude Code); the pattern ignores it.
- `.claude/settings.local.json` is the local settings file of Claude Code. The AI folders of the other tools (`.cursor/`, `.windsurf/`, `.continue/`, `.codex/`, `.gemini/`) are ignored as a whole.
- **Exception: the knowledge-base repositories.** They version these files on purpose, because the files are useful there. Such a repository adds `!` exceptions after the standard block (`!AGENTS.md`, `!CLAUDE.md`, `!GEMINI.md`) and says why in a comment.
- The block is complete in every repository. A pattern the repository already had (for example `/.idea`) is moved into the block, never repeated.

### Your own global ignore file

A contributor can ignore these files for all repositories with a global file, without touching the repository. The AI instruction files and `*.local.md` belong in it too:

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
