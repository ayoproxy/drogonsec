# Installation

---

## Prerequisites

| Requirement | Version | Notes |
|---|---|---|
| **Go** | 1.22+ | [Download Go](https://golang.org/dl/) |
| **Git** | any | For cloning the repository |
| **Make** | any | For running the build system |
| **Docker** | any | Optional — for container-based usage |

Verify your Go installation:

```bash
go version
# Expected: go version go1.22.x linux/amd64
```

---

## Method 1 — From Source (Recommended)

```bash
git clone https://github.com/filipi86/drogonsec
cd drogonsec
make install
```

> ⚠️ **Important:** Run `make install` from inside the `drogonsec/` directory created by `git clone`. Do not run it from a parent folder with the same name.

---

## Method 2 — Docker

```bash
docker run --rm -v $(pwd):/scan ghcr.io/drogonsec/drogonsec scan /scan
```

---

## Method 3 — Manual Build

```bash
git clone https://github.com/filipi86/drogonsec
cd drogonsec
go build -o ./bin/drogonsec ./cmd/drogonsec/main.go
```

---

## Verifying the Installation

```bash
./bin/drogonsec --version
```

---

## Troubleshooting

### `cannot find package "." in ./cmd/drogonsec/main.go`

**Cause:** Nested directory — you cloned inside a folder already named `drogonsec`.

**Fix:**

```bash
find ~ -name "main.go" 2>/dev/null
cd ~/drogonsec    # NOT ~/drogonsec/drogonsec
make install
```

### `go: command not found`

```bash
# Ubuntu/Debian
sudo apt update && sudo apt install golang-go
```

### `make: command not found`

```bash
sudo apt install make
```

### Permission denied on binary

```bash
chmod +x ./bin/drogonsec
./bin/drogonsec --version
```

---

## Uninstalling

```bash
# Remove binary only
rm ./bin/drogonsec

# Remove full project
cd ~
rm -rf drogonsec
```
