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
| `.markdownlint.json` | markdownlint extension | Extends `config/dev-tools/.markdownlint.json`. |
| `.prettierignore`, `.stylelintignore`, `.markdownlintignore` | The same extensions | Copies of the files of `config/dev-tools/`. |

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
