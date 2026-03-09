# Global Contributing Guide

> **[Handbook][handbook]** › Contributing

We welcome contributions to any Zairakai project!  
This document defines the standard workflow and expectations for all contributors.

---

## 🚀 Standard Workflow

Every contribution follows these 7 steps to ensure consistency and quality.

| Step | Action | Description |
| :--- | :--- | :--- |
| **1. Discuss** | Open Issue | Always discuss your intent in an issue before starting work. |
| **2. Setup** | Local Env | Follow the local `README.md` to install dependencies and git hooks. |
| **3. Branch** | `feature/#TICKET` | Create a branch from `main` following the naming convention. |
| **4. Implement** | Code | Follow the project's specific style and architecture. |
| **5. Quality** | `make quality` | All projects use the **Unified Make System** for quality enforcement. |
| **6. Commit** | [Git Rules][git-rules] | Use Conventional Commits with a mandatory ticket number. |
| **7. MR** | Merge Request | Open a Merge Request to `main` once all checks pass. |

---

## 🔧 Types of Contributions

| Type | Expectation |
| :--- | :--- |
| **🐛 Bug Reports** | Use templates. Provide reproduction steps and environment details. |
| **✨ Features** | Must solve a clear problem. Architecture must remain modular. |
| **📜 Documentation** | Markdown must be linted via `make markdownlint`. |
| **🛠️ Tooling** | Any change to `Makefile` or `scripts/` must be ShellCheck compliant. |

---

## 🏛️ Quality First

At Zairakai, we don't compromise on quality:

- **No breaking changes** without major version bump.
- **No merge** without green quality gate (`make quality`).
- **No commit** without ticket reference.

---

**[Back to Handbook][handbook]**

[handbook]: ./README.md
[git-rules]: ./policies/git-rules.md
