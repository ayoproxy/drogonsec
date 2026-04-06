# Modules & Engines

Drogonsec is composed of specialized scanning engines, each targeting a different attack surface. All engines run in parallel by default and can be individually enabled or disabled.

---

## Engine Overview

| Engine | Flag | Description |
|--------|------|-------------|
| **SAST** | `--no-sast` to disable | Static code analysis for 20+ languages |
| **SCA** | `--no-sca` to disable | Dependency vulnerability scanning |
| **Leaks** | `--no-leaks` to disable | Secret and credential detection |
| **IaC** | built-in | Infrastructure as Code misconfiguration detection |
| **AI Remediation** | `--enable-ai` | Intelligent fix suggestions *(Coming soon)* |

---

## SAST Engine — Static Application Security Testing

The SAST engine analyzes source code for security vulnerabilities without executing it. It supports over **20 programming languages** and maps findings to OWASP, CWE, and CVSS standards.

### Supported Languages

`Python` `Java` `JavaScript` `TypeScript` `Go` `Kotlin` `C#` `PHP` `Ruby` `Swift` `Dart` `Elixir` `Erlang` `Shell` `C/C++` `HTML` `Terraform` `Kubernetes` `Nginx`

### What it detects

- SQL Injection, Command Injection, XSS
- Hardcoded credentials in source code
- Insecure cryptographic algorithms
- Broken authentication patterns
- Insecure deserialization
- Path traversal vulnerabilities
- SSRF and XXE patterns

### Example finding

```
#1 [HIGH] SQL Injection via string formatting
File     : src/users.py:42
Rule     : PY-001
OWASP    : A05:2025 - Injection
CWE      : CWE-89  CVSS: 9.8
Fix      : Use parameterized queries instead of string formatting
```

---

## SCA Engine — Software Composition Analysis

The SCA engine scans your project's dependency files for known CVEs, outdated packages, and supply chain risks.

### Supported manifest files

- `package.json` / `package-lock.json` (Node.js)
- `requirements.txt` / `Pipfile` (Python)
- `go.mod` / `go.sum` (Go)
- `pom.xml` / `build.gradle` (Java)
- `Gemfile.lock` (Ruby)
- `composer.json` (PHP)
- `Cargo.toml` (Rust)

### OWASP coverage

Maps directly to **A03:2025 — Software Supply Chain Failures** (new in OWASP Top 10:2025).

---

## Leaks Engine — Secret Detection

Detects hardcoded secrets, API keys, tokens, and credentials in source code and git history.

### Detected patterns (50+)

| Category | Patterns |
|----------|----------|
| **Cloud** | AWS Access Keys, GCP API Keys, Azure Storage Keys |
| **SCM** | GitHub tokens (classic, fine-grained, OAuth, App) |
| **Payment** | Stripe Secret/Restricted Keys |
| **Communication** | Slack Bot/App tokens, Webhook URLs |
| **Email** | SendGrid API Keys |
| **Crypto** | RSA/EC/SSH/PGP private keys, JWT tokens |
| **Database** | Connection strings (PostgreSQL, MySQL, MongoDB, Redis) |
| **Generic** | Hardcoded passwords, API keys, secrets |

### Scan git history

```bash
drogonsec scan . --git-history
```

This scans all commits in the repository history, not just the current state of the code.

---

## IaC Engine — Infrastructure as Code

Detects security misconfigurations in infrastructure definition files.

### Supported formats

- **Terraform** — AWS, GCP, Azure resources
- **Kubernetes** — Pod security, RBAC, network policies
- **Docker** — Dockerfile best practices

### Common detections

- Public S3 buckets
- Overly permissive IAM roles
- Missing encryption at rest
- Containers running as root
- Exposed ports and services
- Missing resource limits

---

## AI Remediation Engine *(Coming soon)*

The AI engine provides intelligent, context-aware fix suggestions for detected vulnerabilities.

```bash
# Set your AI provider API key
export AI_API_KEY="..."

# Enable AI remediation
drogonsec scan . --enable-ai
```

### Planned features

- Context-aware code fix suggestions
- Support for multiple AI providers (OpenAI, Anthropic, custom endpoints)
- High-severity-only mode to reduce noise
- Corrected code snippets inline with findings

```bash
# Coming soon: use your own AI provider
drogonsec scan . --enable-ai \
  --ai-provider openai \
  --ai-model gpt-4o \
  --ai-endpoint https://your-endpoint/v1/messages
```

### Example AI output

```
🤖 AI Remediation:
The SQL injection in line 42 allows attackers to manipulate your query...

Corrected code:
  cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
```

---

## OWASP Top 10:2025 Coverage

| # | Category | Engine | Rules |
|---|----------|--------|-------|
| A01 | Broken Access Control | SAST | ✅ 23 rules |
| A02 | Security Misconfiguration | SAST + IaC | ✅ 31 rules |
| A03 | Software Supply Chain Failures 🆕 | SCA | ✅ Full coverage |
| A04 | Cryptographic Failures | SAST | ✅ 18 rules |
| A05 | Injection | SAST | ✅ 45 rules |
| A06 | Insecure Design | SAST | ✅ 15 rules |
| A07 | Authentication Failures | SAST + Leaks | ✅ 20 rules |
| A08 | Software or Data Integrity Failures | SCA + SAST | ✅ 9 rules |
| A09 | Security Logging & Alerting Failures | SAST | ✅ 11 rules |
| A10 | Mishandling of Exceptional Conditions 🆕 | SAST | ✅ 8 rules |

---

## Extending with Custom Rules

Drogonsec rules are defined as YAML files in the `rules/` directory, making them easy to add and share with the community.

```yaml
# rules/custom/my-rule.yaml
id: CUSTOM-001
name: My Custom Rule
severity: HIGH
language: python
pattern: "eval(.*request.*)"
owasp: A05:2025
cwe: CWE-95
message: "Avoid using eval() with user-supplied input"
fix: "Use ast.literal_eval() for safe evaluation"
```
