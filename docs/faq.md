# FAQ & Troubleshooting

---

## General Questions

### What is Drogonsec?

Drogonsec is an open-source, modular security scanner written in Go. It combines SAST, SCA, secret detection, and IaC analysis into a single tool, aligned with OWASP Top 10:2025 standards.

### Who is Drogonsec for?

- Security researchers and malware hunters
- Red teamers and penetration testers
- SOC analysts and threat intelligence professionals
- DevSecOps engineers integrating security into CI/CD pipelines
- Anyone who wants to audit code for vulnerabilities

### Is Drogonsec free?

Yes. Drogonsec is open source under the **Apache License 2.0**. Free to use, modify, and distribute.

### What is the difference between Drogonsec and Horusec?

Drogonsec is inspired by Horusec but is its modern, actively maintained successor. Key differences:

| | Drogonsec | Horusec |
|--|-----------|---------|
| OWASP alignment | Top 10:2025 | Top 10:2021 |
| AI remediation | ✅ Coming soon | ❌ |
| Active development | ✅ | ❌ Inactive |
| IaC support | ✅ | Limited |

### What operating systems are supported?

Drogonsec is written in Go and supports:
- **Linux** (primary platform)
- **macOS**
- **Windows** (via cross-compilation or Docker)

---

## Installation Issues

### `cannot find package "." in ./cmd/drogonsec/main.go`

**Cause:** Nested directory. You cloned into a folder already named `drogonsec`.

**Fix:**

```bash
find ~ -name "main.go" 2>/dev/null
cd ~/drogonsec    # NOT ~/drogonsec/drogonsec
make install
```

### `go: command not found`

```bash
sudo apt update && sudo apt install golang-go
go version   # verify
```

### `make: command not found`

```bash
sudo apt install make
```

### `Everything up-to-date` on git push but files are missing

The files were not staged. Run:

```bash
git status
git add docs/
git commit -m "add docs"
git push origin main
```

---

## Runtime Issues

### Binary not found after build

```bash
ls -la ./bin/
# If empty:
make install
```

### Permission denied when running

```bash
chmod +x ./bin/drogonsec
./bin/drogonsec --version
```

### Scan returns no findings

- Check that the target path is correct
- Verify that the relevant engines are not disabled (`--no-sast`, `--no-sca`, `--no-leaks`)
- Try lowering the minimum severity: `--severity LOW`
- Enable verbose output for debugging

### Docker image not found

```bash
docker pull ghcr.io/drogonsec/drogonsec:latest
docker run --rm -v $(pwd):/scan ghcr.io/drogonsec/drogonsec scan /scan
```

---

## CI/CD Integration

### How do I fail the pipeline on critical vulnerabilities?

Add to your `.drogonsec.yaml`:

```yaml
fail_on:
  critical: true
  high: true
```

Or Drogonsec will exit with a non-zero code when critical findings are detected, which most CI systems interpret as a failure automatically.

### How do I integrate with GitHub Security tab?

```yaml
- name: DragonSec Scan
  run: drogonsec scan . --format sarif --output results.sarif

- name: Upload SARIF
  uses: github/codeql-action/upload-sarif@v3
  with:
    sarif_file: results.sarif
```

---

## Contributing

### How do I contribute?

1. Fork the repository on GitHub
2. Create a branch: `git checkout -b feat/my-contribution`
3. Make your changes
4. Commit: `git commit -m "Add: description of change"`
5. Push and open a Pull Request

### What can I contribute?

- New SAST rules for any supported language
- Additional secret detection patterns
- Bug fixes and performance improvements
- Documentation improvements
- New language parsers

### Where are the rules defined?

Rules are YAML files in the `rules/` directory. They are community-extensible and follow a simple schema — see the [Modules](./modules.md) page for the full rule format.

---

## Getting Help

- Open an issue: [github.com/filipi86/drogonsec/issues](https://github.com/filipi86/drogonsec/issues)
- Include your Go version (`go version`), OS, the command you ran, and the full error output
- Check existing issues before opening a new one
