# Editor Integration

> **[Handbook][handbook]** › **[Coding Standards][standards]** › Editor Integration

---

## Overview

The commands of the repository are the contract. Your editor is a convenience on top of them.

- Run `make quality` before you commit: it checks the code and changes nothing.
- Run `make quality-fix` to apply the automatic corrections.
- The CI runs the same checks and blocks the merge on a failure, so a missing editor setup never lets a violation through.

Nothing in this page is required. Use the editor you want, with or without extensions.

---

## The commands

| Situation | Check | Fix |
| :--- | :--- | :--- |
| The repository has a `Makefile` (default) | `make quality` | `make quality-fix` |
| No `Makefile` | the scripts of `package.json` (`npm run`, `yarn`, `pnpm`) or `composer.json` (`composer run`) | the same scripts, with their `:fix` or `-fix` variant |

`make help` lists the targets of a repository. Script names without a `Makefile` vary by project: read the `scripts` section of `package.json` or `composer.json`.

The package manager is your choice. The commands above do not depend on it.

---

## Files published at the root

These files are committed in the repository. Editors and extensions that find them apply the rules without any other setup.

| File | Used by | Role |
| :--- | :--- | :--- |
| `.editorconfig` | VS Code (extension), JetBrains IDEs, Vim, and most editors | Indentation, line endings, charset. See [EditorConfig][editorconfig-standard]. |
| `.gitattributes` | Git | Line endings, Markdown diffs, binary files. |
| `eslint.config.mjs`, `prettier.config.mjs`, `stylelint.config.mjs` | ESLint, Prettier and Stylelint extensions | Thin files: they re-export `config/dev-tools/<tool>.config.js`. |
| `vitest.config.mjs` | Vitest extension | Thin file, same role. Not created when the project has a `vite.config.*`, which Vitest reads. |
| `phpstan.neon` | PHPStan extensions | Thin file: it includes `config/dev-tools/phpstan.neon`. |
| `.markdownlint.json` | markdownlint extension | Thin file: it extends `config/dev-tools/.markdownlint.json`. |
| `pint.json`, `phpunit.xml` | Pint and PHPUnit extensions | Copies of the files of `config/dev-tools/` (see below). |
| `.prettierignore`, `.stylelintignore`, `.markdownlintignore` | The same extensions | Copies of the files of `config/dev-tools/`. |

`rector.php`, `phpinsights.php`, `knip.config.js` and `tsup.config.js` have no file at the root: no extension of an editor reads them, the commands do (see the rules below).

**Why root files.** The shared configuration is published in `config/dev-tools/` and the `make` targets read it from there. Editor extensions only look for the standard file names at the root of the repository, in the folder of the file you edit and its parents. They never look into `config/dev-tools/`. A thin file holds no rule of its own: it re-exports or extends the published one.

```js
// eslint.config.mjs
export { default } from './config/dev-tools/eslint.config.js'
```

```json
{
  "extends": "./config/dev-tools/.markdownlint.json"
}
```

An ignore file cannot extend another file, and npm does not publish symbolic links: the root gets a copy. The `js-dev-tools` package creates these files on install (`npm run setup`, or the `postinstall`) when the project has no config of that tool yet, and refresh them with `--force`. A project that edits a file of `config/dev-tools/` keeps the thin file as it is: it follows.

### The rules of the root files

1. **The configuration of a project is the file of `config/dev-tools/`**, one per tool, committed. It extends the one of the `dev-tools` package of the group, which extends the base rules. Everything that is specific to the project is written there, and nowhere else. The commands (`make`, `composer`, `npm`) read these files.
2. **A file exists at the root only when an editor or an extension reads it.** They look for the standard names at the root and never in `config/`. A tool that no extension reads has no file at the root.
3. **A root file that can point at another file is thin, and points at the one of `config/dev-tools/`, and at nothing else**: a re-export, an `extends` or an `includes`. It never points at `vendor/` or `node_modules/`.
4. **A file that cannot point at another one is a copy: it exists at the root, for the editor, and in `config/dev-tools/`, which the commands read.** `pint.json` (Pint cannot extend a file), `phpunit.xml` (same) and the ignore files are in this case. The two must say the same, and a check makes sure of it.

**Copies in PHP projects.** `make config-check` compares each copy with its file of `config/dev-tools/` and tells a thin file that does not point at `config/dev-tools/`; it is part of `make quality`, and `SYNC=true make config-check` writes the copies again. PHPUnit reads the paths of its file from the folder of that file, so the `phpunit.xml` of `config/dev-tools/` goes up two folders (`../../src`) and the copy of the root does not (`src`): they are equivalent when only that prefix differs. A forced publication (`--force`) keeps a copy of every file that it writes over in `.dev-tools-backup/<UTC date and time>/`.

---

## Set up your editor (optional)

Each contributor sets up the editor in their own environment, repository by repository. The repository does not publish editor settings (see the next section), and the `dev-tools` packages do not impose any.

### VS Code

1. Install the extension of the tool you want (for example `davidanson.vscode-markdownlint`, `dbaeumer.vscode-eslint`, `esbenp.prettier-vscode`, `stylelint.vscode-stylelint`, `editorconfig.editorconfig`). The extension uses the version installed in the project (`node_modules` or `vendor`) and the file found at the root.
1. To format and fix on save, add the settings below to your user settings or to your profile. The corrections run on a manual save only (`explicit`), not on the autosave, and Prettier formats afterwards. Markdown keeps its trailing double spaces, which are line breaks.

```jsonc
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.fixAll.stylelint": "explicit",
    "source.fixAll.markdownlint": "explicit"
  },
  "[javascript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[typescript]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[json]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[css]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[scss]": { "editor.defaultFormatter": "esbenp.prettier-vscode" },
  "[markdown]": {
    "editor.formatOnSave": false,
    "files.trimTrailingWhitespace": false
  }
}
```

The `.editorconfig` already carries the indentation, the line endings and the final newline: the `editorconfig.editorconfig` extension applies them, so do not repeat them in your settings. Official documentation: [VS Code settings][vscode-settings].

1. To run the commands from the editor, add a task to your own settings (user `tasks.json`, or your workspace file). The example runs `make quality` at the root of the Git repository of the file you have open:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "make quality",
      "type": "shell",
      "command": "make -C \"$(git -C '${fileDirname}' rev-parse --show-toplevel)\" quality",
      "problemMatcher": []
    }
  ]
}
```

Official documentation: [VS Code tasks][vscode-tasks].

### JetBrains IDEs (PhpStorm, WebStorm)

Add `make quality` as an external tool, with the project folder as working directory. Official documentation: [External tools][jetbrains-external-tools].

### Any other editor

Run the commands in a terminal. Check the documentation of your editor for tasks, build commands or external tools.

---

## What a repository does not publish

Personal editor and AI assistant files are ignored in every repository: they hold paths and preferences of one person (`.vscode/`, `.idea/`, and the others). The complete list, with the block to copy into the `.gitignore`, is in the [Gitignore][gitignore] standard.

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[editorconfig-standard]: ./editorconfig.md
[gitignore]: ./gitignore.md
[vscode-tasks]: https://code.visualstudio.com/docs/debugtest/tasks
[vscode-settings]: https://code.visualstudio.com/docs/configure/settings
[jetbrains-external-tools]: https://www.jetbrains.com/help/phpstorm/configuring-third-party-tools.html
