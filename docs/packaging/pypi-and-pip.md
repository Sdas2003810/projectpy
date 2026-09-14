# PyPI & Advanced `pip` Configuration

The **Python Package Index (PyPI)** hosts hundreds of thousands of open-source Python packages. Navigating PyPI safely and configuring `pip` for enterprise environments, private registries, and CI/CD pipelines requires mastering advanced tools and security practices.

---

## PyPI Security & Modern Publishing

### 1. Mandatory Two-Factor Authentication (2FA)

PyPI requires two-factor authentication (via WebAuthn security keys or TOTP authenticator apps) for all maintainers to prevent account takeover attacks.

### 2. API Tokens

Never authenticate to PyPI using your username and password. Always generate an **API Token**:
- Scope the token to a single specific project whenever possible.
- PyPI tokens begin with the prefix `pypi-`.

### 3. Trusted Publishers (OIDC Keyless Publishing)

The modern gold standard for publishing Python packages is **Trusted Publishing** via OpenID Connect (OIDC).
Instead of storing sensitive API tokens inside GitHub Actions secrets, PyPI establishes a cryptographic trust relationship directly with your GitHub repository:

```yaml
# .github/workflows/publish.yml
name: Publish to PyPI
on:
  release:
    types: [published]

jobs:
  pypi-publish:
    runs-on: ubuntu-latest
    permissions:
      id-token: write  # Crucial for OIDC token exchange!
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install build && python -m build
      - name: Publish package distributions to PyPI
        uses: pypa/gh-action-pypi-publish@release/v1
```

---

## Private Package Indexes & Mirrors

Enterprises often store proprietary internal libraries on private registries (like AWS CodeArtifact, JFrog Artifactory, Sonatype Nexus, or Azure Artifacts):

### Connecting to a Private Index

```bash
# Set primary index URL
python -m pip install my-internal-lib --index-url https://mycompany.jfrog.io/artifactory/api/pypi/pypi-local/simple

# Or supply an extra fallback index (check private first, then public PyPI)
python -m pip install my-internal-lib --extra-index-url https://pypi.org/simple
```

### Persistent Configuration: `pip.conf`

Instead of passing flags on every command, store settings in `pip.conf` (Linux/macOS: `~/.config/pip/pip.conf`; Windows: `%APPDATA%\pip\pip.ini`):

```ini
[global]
timeout = 60
index-url = https://mycompany.jfrog.io/artifactory/api/pypi/pypi-local/simple
extra-index-url = https://pypi.org/simple
trusted-host = mycompany.jfrog.io
```

You can view and modify these settings via CLI:

```bash
pip config list
pip config set global.timeout 60
```

---

## Advanced `pip` Techniques

### Offline / Air-Gapped Environments

To install packages on servers without internet access:

1. **Download wheels on a connected machine**:
    ```bash
    python -m pip download -r requirements.txt -d ./offline_wheels
    ```
2. **Transfer directory to the offline server and install**:
    ```bash
    python -m pip install --no-index --find-links=./offline_wheels -r requirements.txt
    ```

### Managing the `pip` Cache

`pip` caches downloaded wheels locally to speed up subsequent installations:

```bash
# Locate cache directory
pip cache dir

# Inspect cache size and items
pip cache list

# Purge cache to reclaim disk space
pip cache purge
```

### Dry Run Installations

Test dependency resolution without modifying the disk:

```bash
python -m pip install --dry-run -r requirements.txt
```
