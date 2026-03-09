# Bash Scripting Standards

> **[Handbook][handbook]** › **[Coding Standards][standards]** › Bash

---

## Overview

Bash scripts automate environment setup, dependency management, quality checks, and CI tasks. These guidelines ensure all scripts are readable, portable, and maintainable across projects.

**Official reference**: [GNU Bash Manual][gnu-bash-manual]

---

## Writing Bash Scripts

### Mandatory Header

Every script must start with:

```bash
#!/usr/bin/env bash
set -euo pipefail
```

- `#!/usr/bin/env bash` — portable shebang (not `/bin/bash`)
- `set -e` — exit on error
- `set -u` — exit on undefined variable
- `set -o pipefail` — catch errors in pipes

### Configuration Import

When a project provides a shared `config.sh` (logging, color helpers, shared variables), import it at the top:

```bash
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
# shellcheck source=config.sh
source "${SCRIPT_DIR}/config.sh"
```

### Variables

- **UPPERCASE** for environment variables and constants: `PROJECT_ROOT`, `VENDOR_PATH`
- **lowercase** for local script variables: `pkg_name`, `target_file`
- Always quote variables: `"$VAR"` not `$VAR`

### Functions

- **lowercase_with_underscores**: `install_hooks`, `publish_file`, `log_success`
- Keep functions focused — one responsibility per function
- Declare local variables with `local`

### Comments and Indentation

- Use **4 spaces** for indentation (2 is acceptable for short scripts)
- Comment complex logic, not obvious commands

### ShellCheck

All scripts must pass **ShellCheck** with no warnings:

```bash
shellcheck scripts/*.sh
# or via Make:
make shellcheck
```

---

## Naming and File Organization

- **File names**: lowercase, hyphenated — `install-hooks.sh`, `setup-package.sh`
- **Organization**: place scripts in a `scripts/` directory at the project root
- **Specific tasks**: use subdirectories if needed — `scripts/gitlab-ci/`, `scripts/hooks/`

---

## Control Structures

### `if / else / fi`

```bash
if [[ -f "composer.json" ]]; then
    echo "composer.json exists."
else
    echo "composer.json is missing."
fi
```

### `if / elif / else / fi`

```bash
if [[ -f "composer.json" ]]; then
    echo "composer.json found."
elif [[ -f "composer.lock" ]]; then
    echo "composer.lock found."
else
    echo "No composer files found."
fi
```

### `for` loop

```bash
for file in *.sh; do
    shellcheck "$file"
done
```

### `while` loop

```bash
counter=1
while [[ $counter -le 5 ]]; do
    echo "Step $counter"
    ((counter++))
done
```

---

## Testing Variables

### Non-empty variable

```bash
if [[ -n "$VAR" ]]; then
    echo "VAR is set: $VAR"
fi
```

### Empty or unset variable

```bash
if [[ -z "$VAR" ]]; then
    echo "VAR is empty or unset."
fi
```

### Boolean value

```bash
if [[ "$FORCE" == "true" ]]; then
    echo "Force mode enabled."
fi
```

### Integer check

```bash
if [[ "$VAR" =~ ^[0-9]+$ ]]; then
    echo "VAR is a positive integer."
fi
```

---

## Testing Files and Directories

```bash
# File exists
if [[ -f "composer.json" ]]; then
    echo "File exists."
fi

# Directory exists
if [[ -d "vendor" ]]; then
    echo "Directory exists."
fi

# File is executable
if [[ -x "./scripts/setup.sh" ]]; then
    echo "Script is executable."
fi
```

---

## Utility Functions Pattern

Projects that expose logging helpers follow this pattern:

```bash
log_header()  { echo -e "\n=== $* ==="; }
log_step()    { echo -e "  → $*"; }
log_success() { echo -e "  ✓ $*"; }
log_warning() { echo -e "  ⚠ $*" >&2; }
log_error()   { echo -e "  ✗ $*" >&2; }
```

Use these consistently instead of raw `echo` calls — it makes silent mode (`--silent` flag) and CI mode easier to implement.

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[gnu-bash-manual]: https://www.gnu.org/software/bash/manual/
