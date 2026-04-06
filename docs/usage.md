# Usage Examples

---

## Basic Commands

```bash
# Scan current directory
drogonsec scan .

# Show help
drogonsec --help

# Show version
drogonsec --version
```

---

## Scan with Output Formats

```bash
# Default text output
drogonsec scan .

# JSON report
drogonsec scan ./myproject --format json --output report.json

# HTML report (open in browser)
drogonsec scan . --format html --output report.html

# SARIF (GitHub/Azure DevOps integration)
drogonsec scan . --format sarif --output results.sarif
```

---

## Controlling Severity

```bash
# Only report HIGH and CRITICAL findings
drogonsec scan . --severity HIGH

# Include all severity levels
drogonsec scan . --severity LOW
```

---

## Enabling / Disabling Engines

```bash
# Disable SCA (dependency scanning)
drogonsec scan . --no-sca

# Disable secret detection
drogonsec scan . --no-leaks

# Disable SAST (code analysis)
drogonsec scan . --no-sast
```

---

## Git History Scanning

```bash
# Scan the entire git commit history for secrets
drogonsec scan . --git-history
```

This is especially useful for catching secrets that were committed and later deleted — they still exist in git history.

---

## AI-Powered Remediation *(Coming soon)*

```bash
# Set your AI provider key
export AI_API_KEY="..."

# Enable AI remediation suggestions
drogonsec scan . --enable-ai

# Use a custom AI provider
drogonsec scan . --enable-ai \
  --ai-provider openai \
  --ai-model gpt-4o \
  --ai-endpoint https://your-endpoint/v1/messages
```

---

## GitHub Actions Integration

```yaml
# .github/workflows/security.yml
name: Security Scan

on: [push, pull_request]

jobs:
  drogonsec:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: DragonSec Scan
        run: drogonsec scan . --format sarif --output results.sarif

      - name: Upload to GitHub Security
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: results.sarif
```

---

## Output Examples

### Text Output (default)

```
🛡 DragonSec Security Scanner
═══════════════════════════════════════════
  Target : /path/to/project
  SAST   : enabled
  SCA    : enabled
  Leaks  : enabled
═══════════════════════════════════════════

═══ SAST FINDINGS ══════════════════════
  #1 [HIGH] SQL Injection via string formatting
  File     : src/users.py:42
  Rule     : PY-001
  OWASP    : A05:2025 - Injection
  CWE      : CWE-89  CVSS: 9.8
  Fix      : Use parameterized queries...
```

### JSON Output

```json
{
  "version": "0.1.0",
  "stats": {
    "total_findings": 5,
    "critical": 1,
    "high": 3,
    "medium": 1,
    "low": 0
  },
  "sast_findings": [],
  "sca_findings": [],
  "leak_findings": []
}
```

---

## Configuration File

Create `.drogonsec.yaml` in your project root to define persistent scan settings:

```yaml
scan:
  min_severity: LOW
  workers: 4
  git_history: false
  ignore_paths:
    - node_modules
    - vendor
    - dist

engines:
  sast:
    enabled: true
  sca:
    enabled: true
  leaks:
    enabled: true
    min_entropy: 3.5

ai:
  enabled: false
  high_severity_only: true

fail_on:
  critical: true
  high: true
```

---

## Tips for Security Professionals

- Always run Drogonsec inside an **isolated VM** when analyzing potentially malicious code
- Use `--format json` when integrating with SIEM or threat intelligence platforms
- Use `--git-history` on onboarding new repositories to audit past commits
- Combine with **YARA**, **Semgrep**, or **TheHive** for a complete analysis workflow
- Use `fail_on.critical: true` in CI/CD to block deployments with critical vulnerabilities

---

## Keeping Drogonsec Updated

```bash
cd drogonsec
git pull origin main
make install
```
