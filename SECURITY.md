# Global Security Policy

> **[Handbook][handbook]** › Security Policy

Zairakai is committed to the security of its users and the integrity of its projects.

---

## 🔒 Reporting Vulnerabilities

If you discover a security vulnerability, please **do not disclose it publicly**. Use one of the following channels:

| Channel | Contact / Link |
| :--- | :--- |
| **Service Desk** | Refer to the project's local `SECURITY.md`. |
| **Direct Email** | `security@the-white-rabbits.fr` |

**Please include:**

1. Vulnerability description and potential impact.
2. Step-by-step reproduction instructions.
3. Affected versions.

---

## 🛡️ Standard Protections

Every project in the Zairakai ecosystem integrates:

- **Static Analysis**: Mandatory linting and type checking (Level Max for PHP).
- **Secret Detection**: Automated scanning in CI pipelines.
- **Dependency Audit**: Regular checks for known vulnerabilities.
- **Controlled Execution**: Shell scripts validated via ShellCheck.

---

## ⏱️ Response Timeline

| Severity | Acknowledgment | Fix Target |
| :--- | :--- | :--- |
| **CRITICAL** | 24h | 24-48h |
| **HIGH** | 48h | 7 days |
| **MEDIUM** | 7 days | 30 days |
| **LOW** | 14 days | 90 days |

---

**[Back to Handbook][handbook]**

[handbook]: ./README.md
