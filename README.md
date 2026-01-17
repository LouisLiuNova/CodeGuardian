# CodeGuardian

CodeGuardian is an intelligent code migration and audit Agent system built on LangGraph. It empowers developers to automate the refactoring of legacy code into modern tech stack code, with robust guarantees for syntactic accuracy and consistent code styling compliance.

[![Python 3.14+](https://img.shields.io/badge/python-3.14%2B-blue)](https://www.python.org/downloads/release/python-3142/)
[![LangGraph](https://img.shields.io/badge/powered%20by-LangGraph-emerald)](https://langchain.com/langgraph)
[![DevContainer](https://img.shields.io/badge/environment-DevContainer%2Buv-orange)](https://code.visualstudio.com/docs/devcontainers/containers)
[![MIT License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

> **An intelligent code migration & audit Agent system powered by LangGraph**  
> Automate refactoring of legacy codebases to modern tech stacks while ensuring syntactic accuracy and consistent code styling.

## 🌟 Core Features

Tailored for developers/teams tackling legacy code modernization, CodeGuardian combines the power of LangGraph (for agentic workflow orchestration) and containerized environments (for consistency) with ultra-fast dependency management via `uv`:

### 🚀 Key Capabilities

- **Intelligent Code Migration**: Supports multi-language legacy-to-modern refactoring (Python/Java/JavaScript/Django/Java Spring, etc.).
- **Automated Audit**: Validates syntax correctness, style compliance (PEP8/Google Style), security best practices, and performance optimizations.
- **Flexible Model Integration**: Works with external LLM endpoints (OpenAI/Gemini/智谱/本地 Ollama) – no local model installation required.
- **Consistent Development Env**: Built on `DevContainer + uv` for 100% cross-platform consistency (Windows/macOS/Linux).
- **Scalable Workflows**: From single-file refactoring to Git repo-level migration, with CI/CD integration.
- **Customizable Rules**: Define team-specific migration/audit rules (tech stack standards, coding styles, performance benchmarks).

### 🛠️ Tech Stack

- **Core Framework**: LangChain + LangGraph (agentic workflow)
- **Python Version**: 3.14.2+ (stable, with `bookworm` OS base)
- **Vector DB**: Chroma + FAISS (for code semantic analysis/caching)
- **Deployment**: LangServe (FastAPI-based API service)
- **Env Management**: DevContainer + uv (virtual environment/dependency management)

## 🚀 Quick Start

Get up and running in 5 minutes with the pre-configured `DevContainer` environment (no manual setup of Python, system dependencies, or LLMs).

### 1. Prerequisites

- Install [Docker Desktop](https://www.docker.com/products/docker-desktop/) (Windows/macOS) or Docker Engine (Linux).
- Install [VS Code](https://code.visualstudio.com/) + [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension.

### 2. Clone the Repo

```bash
git clone https://github.com/your-org/codeguardian.git
cd codeguardian
```

### 3. Launch the DevContainer

1. Open the project in VS Code.
2. Press `F1` → Run command: `Remote-Containers: Reopen in Container`.
3. Wait for the container to build (auto-installs Python 3.14.2, `uv`, dependencies like Chroma/FAISS, and VS Code extensions).

### 4. Configure External LLM

CodeGuardian uses external LLM endpoints. Generate and edit the config file:

```bash
# Generate default model config
codeguardian init-config
```

Edit `configs/model_config.yaml` (add your API key/env vars):

```yaml
default_model: "openai-gpt4"
models:
  openai-gpt4:
    type: "openai"
    base_url: "https://api.openai.com/v1"
    api_key: "${OPENAI_API_KEY}"  # Load from env vars (no hardcoding!)
    temperature: 0.1  # Low temperature for code accuracy
  zhipu-glm4:
    type: "zhipu"
    base_url: "https://open.bigmodel.cn/api/paas/v4"
    api_key: "${ZHIPU_API_KEY}"
    temperature: 0.1
  local-ollama:
    type: "ollama"
    base_url: "http://localhost:11434"  # Connect to external Ollama instance
    model_name: "llama3:8b"
    temperature: 0.1
```

Set environment variables (container terminal):

```bash
# Temporary (container session only)
export OPENAI_API_KEY="your-api-key-here"

# Permanent (save to .env file, auto-loaded by DevContainer)
echo "OPENAI_API_KEY=your-api-key-here" >> .env
```

### 5. Run Your First Migration

Let’s migrate a Python 2 sync script to Python 3.14 async code:

#### Step 1: Create a Legacy Code File

Create `examples/legacy_python2.py`:

```python
import urllib2
def fetch_data(url):
    response = urllib2.urlopen(url)
    return response.read()
```

#### Step 2: Run Migration via CLI

```bash
# Activate uv virtual environment (auto-created by DevContainer)
source .venv/bin/activate

# Run migration
codeguardian migrate \
  --input ./examples/legacy_python2.py \
  --rule "python2→python3.14; 同步urllib2→异步aiohttp" \
  --output ./examples/modern_python3.py \
  --audit-report ./examples/audit_report.json
```

#### Step 3: Check Results

1. **Modern Code**: `examples/modern_python3.py` (async, type-annotated, PEP8-compliant):

   ```python
   import aiohttp
   import asyncio

   async def fetch_data(url: str) -> str:
       async with aiohttp.ClientSession() as session:
           async with session.get(url) as response:
               response.raise_for_status()  # Added error handling (audit suggestion)
               return await response.text()
   ```

2. **Audit Report**: `examples/audit_report.json` (validates syntax/style/optimizations):

   ```json
   {
     "file": "legacy_python2.py",
     "status": "success",
     "syntax_check": "pass",
     "style_compliance": "pass",
     "optimizations": [
       "Replaced urllib2 → aiohttp (async adaptation)",
       "Added type annotations (Python 3.14 feature)",
       "Added HTTP error handling"
     ],
     "compatibility_issues": [],
     "performance_suggestions": ["Add request timeout: timeout=aiohttp.ClientTimeout(total=10)"]
   }
   ```

## 📚 Detailed Usage

CodeGuardian supports 5 core workflows – from quick single-file migration to enterprise-scale CI/CD integration.

### 1. CLI Migration (Single File/Folder)

```bash
# Migrate an entire Django 1.8 project to Django 4.2
codeguardian migrate \
  --input ./legacy_django_project/ \
  --rule "python-django1.8→django4.2" \
  --output ./modern_django_project/ \
  --audit-report ./django_migration_report.md \
  --report-format markdown  # Use markdown for human-readable reports
```

### 2. Custom Migration Rules

Define team-specific rules in `configs/custom_rules.yaml`:

```yaml
rule_set:
  python-django1.8→django4.2:
    description: "Django 1.8 → 4.2 (Python 3.14 compatible)"
    steps:
      - "Replace django.core.urlresolvers → django.urls"
      - "Replace HttpResponseRedirect → redirect (shortcut)"
      - "Template syntax: {% load staticfiles %} → {% load static %}"
    style_rules:
      - "Function names: snake_case only"
      - "Line length ≤ 88 characters (PEP8 extended)"
```

Run with custom rules:

```bash
codeguardian migrate \
  --input ./legacy_django_project/ \
  --rule-file ./configs/custom_rules.yaml \
  --rule-name "python-django1.8→django4.2" \
  --output ./modern_django_project/
```

### 3. Git Repo-Level Migration

Migrate directly from a Git repo (supports incremental migration):

```bash
codeguardian migrate-repo \
  --repo-url "https://github.com/your-org/legacy-project.git" \
  --repo-branch "main" \
  --rule-name "python2→python3.14" \
  --output-repo ./modern-repo/ \
  --incremental true  # Only migrate changed files
  --commit-message "feat: migrate to Python 3.14 via CodeGuardian"
```

### 4. API Service (LangServe)

Expose CodeGuardian as a REST API for integration with internal tools:

```bash
# Start API service (port 8000, auto-forwarded by DevContainer)
codeguardian serve --host 0.0.0.0 --port 8000 --api-key "your-secret-key"
```

Access the auto-generated Swagger UI: `http://localhost:8000/docs` (test endpoints online).

Example Python API call:

```python
import requests

url = "http://localhost:8000/migrate"
headers = {"Content-Type": "application/json", "X-API-Key": "your-secret-key"}
data = {
    "input_code": "import urllib2\n def fetch_data(url):\n    response = urllib2.urlopen(url)\n    return response.read()",
    "migrate_rule": "python2→python3.14; 同步→异步aiohttp"
}

response = requests.post(url, json=data)
print("Modern Code:\n", response.json()["modern_code"])
```

### 5. CI/CD Integration (GitHub Actions)

Add automated migration/audit to your PR workflow. Create `.github/workflows/codeguardian-audit.yml`:

```yaml
name: CodeGuardian Audit
on:
  pull_request:
    branches: [main]
    paths: ["**.py", "**.java"]

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      # Use DevContainer for consistent environment
      - name: Start DevContainer
        uses: devcontainers/ci@v0.3
        with:
          imageName: mcr.microsoft.com/devcontainers/python:3.14.2-bookworm
          push: never
      
      # Set LLM API keys (stored in GitHub Secrets)
      - name: Set Env Vars
        run: |
          echo "OPENAI_API_KEY=${{ secrets.OPENAI_API_KEY }}" >> .env
      
      # Run audit (block PR if critical issues are found)
      - name: Run Audit
        run: |
          source .venv/bin/activate
          codeguardian audit \
            --input ./src/ \
            --rule "python3.14 style; security best practices" \
            --audit-report ./audit_report_ci.json \
            --fail-on-error true  # Fail CI if syntax/security issues exist
      
      # Upload report for PR review
      - name: Upload Audit Report
        uses: actions/upload-artifact@v4
        with:
          name: audit-report
          path: ./audit_report_ci.json
```

---

## ⚙️ Configuration

All configuration files live in the `configs/` directory (auto-generated via `codeguardian init-config`):

| File                  | Purpose                                  | Key Customizations                          |
|-----------------------|------------------------------------------|---------------------------------------------|
| `model_config.yaml`   | LLM endpoint setup                       | Add/remove models, adjust temperature/base_url |
| `custom_rules.yaml`   | Migration/audit rules                    | Define tech stack standards, style rules    |
| `audit_rules.yaml`    | Audit dimensions (security/performance)  | Add custom checks (e.g., "ban eval()")      |
| `.codeguardianignore` | Exclude files from migration             | Similar to .gitignore (e.g., `__pycache__/`) |

---

## 🛠️ Troubleshooting

### Common Issues & Fixes

1. **LLM Call Failed**:
   - Verify `model_config.yaml` has correct `base_url` and `api_key`.
   - Run `codeguardian check-deps --verbose` to test network connectivity.

2. **Syntax Errors in Migrated Code**:
   - Lower the LLM temperature (e.g., `--temperature 0.05`).
   - Split complex rules into smaller steps.

3. **Port 8000 Already in Use**:
   - Change the API port: `codeguardian serve --port 8080`.
   - Update DevContainer `forwardPorts` in `.devcontainer/devcontainer.json`.

4. **Dependency Installation Slow**:
   - The `uv` package manager (pre-installed) accelerates installs – ensure the virtual environment is activated (`source .venv/bin/activate`).

---

## 🤝 Contributing

We welcome contributions to extend features, add language support, or improve workflows.

### Development Setup

1. Fork the repo and clone your fork.
2. Launch the DevContainer (as in Quick Start) – all dev dependencies are pre-installed.
3. Create a feature branch: `git checkout -b feature/your-feature`.
4. Run tests: `pytest tests/ -v`.
5. Submit a PR with a clear description of changes.

### Contribution Guidelines

- Follow PEP8 style (enforced by `black` and `flake8`).
- Add tests for new features.
- Update documentation (README/usage examples) for changes.

---

## 📄 License

CodeGuardian is released under the [MIT License](LICENSE) – free for personal/commercial use.

---

## 📞 Contact

For questions, feature requests, or bug reports:

- GitHub Issues: [Submit an Issue](https://github.com/your-org/codeguardian/issues)
- Email: <team@codeguardian.dev>

---

> Built for developers, by developers. Let CodeGuardian handle the legacy code – so you can focus on building the future. 🚀
