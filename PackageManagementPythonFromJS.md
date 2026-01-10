# [title:uv vs Poetry Cheatsheet]# 🐍 uv vs Poetry — Python Package Management

> Quick reference for choosing between `uv` and `poetry`, plus common workflows for both.

---

## 🎯 When to Use What

| Tool       | Best For                                | Speed                       | Philosophy                                              |
|------------|-----------------------------------------|-----------------------------|---------------------------------------------------------|
| **uv**     | Fast installs, CI/CD, modern projects   | ⚡ Blazing fast (Rust-based) | Minimal, fast, drop-in replacement for pip              |
| **Poetry** | Complete project management, publishing | 🐢 Slower but feature-rich  | All-in-one build system, dependency resolver, publisher |

### 💡 Quick Decision Tree
- **Use uv** when: You want speed, simple dependency management, or pip compatibility
- **Use Poetry** when: You're publishing packages to PyPI, need advanced dependency resolution, or want full project scaffolding
- **Use both**: uv for installing, Poetry for publishing (they're compatible!)

---

## 📦 Package Installation (npm install equivalent)

### **pip** (traditional)
```bash
# Install package (⚠️ does NOT auto-save)
pip install requests pandas numpy

# Manually save all dependencies
pip freeze > requirements.txt

# Install from requirements.txt (like npm install)
pip install -r requirements.txt

# No native dev dependencies — manage manually
pip install -r requirements-dev.txt
```

**Note**: pip doesn't auto-update files like `npm i --save`. You must run `pip freeze` or edit `requirements.txt` manually.

### **uv**
```bash
# Install & add to pyproject.toml (like npm i --save)
uv add requests pandas numpy

# Add dev dependency
uv add --dev pytest black ruff

# Add with version constraint
uv add "django>=4.0,<5.0"

# Install from pyproject.toml (like npm install)
uv sync

# Install without syncing pyproject.toml (just install)
uv pip install requests
```

### **Poetry**
```bash
# Install package and add to pyproject.toml
poetry add requests pandas numpy

# Add dev dependency
poetry add --group dev pytest black ruff

# Add with version constraint
poetry add "django>=4.0,<5.0"

# Install from pyproject.toml (like npm install)
poetry install

# Install without dev dependencies
poetry install --no-dev
```

---

## 🆕 Project Initialization

### **uv**
```bash
# Create new project
uv init my-project
cd my-project

# Create pyproject.toml in existing project
uv init

# Specify Python version
uv init --python 3.11
```

### **Poetry**
```bash
# Create new project with structure
poetry new my-project
cd my-project

# Initialize in existing project
poetry init

# Creates full structure:
# my-project/
# ├── pyproject.toml
# ├── README.md
# ├── my_project/
# │   └── __init__.py
# └── tests/
#     └── __init__.py
```

---

## 🔒 Lock Files (package-lock.json equivalent)

### **uv**
```bash
# Generate uv.lock
uv lock

# Install from lock file
uv sync

# Update dependencies
uv lock --upgrade
```

### **Poetry**
```bash
# Generate poetry.lock
poetry lock

# Install from lock file
poetry install

# Update dependencies
poetry update
```

---

## 🗑️ Remove Packages

### **uv**
```bash
# Remove package
uv remove requests

# Remove dev dependency
uv remove --dev pytest
```

### **Poetry**
```bash
# Remove package
poetry remove requests

# Remove dev dependency
poetry remove --group dev pytest
```

---

## 🏃 Running Commands in Virtual Environment

### **uv**
```bash
# Run command in venv (auto-creates if needed)
uv run python script.py
uv run pytest
uv run black .

# Activate venv manually
source .venv/bin/activate  # Linux/Mac
.venv\Scripts\activate     # Windows
```

### **Poetry**
```bash
# Run command in Poetry's venv
poetry run python script.py
poetry run pytest
poetry run black .

# Activate Poetry's venv
poetry shell
```

---

## 📝 pyproject.toml Structure

Both tools use `pyproject.toml` (similar to `package.json`):

### **uv example**
```toml
[project]
name = "my-project"
version = "0.1.0"
description = "My awesome project"
requires-python = ">=3.10"
dependencies = [
    "requests>=2.31.0",
    "pandas>=2.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "black>=23.0.0",
]

[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
]
```

### **Poetry example**
```toml
[tool.poetry]
name = "my-project"
version = "0.1.0"
description = "My awesome project"
authors = ["Your Name <you@example.com>"]

[tool.poetry.dependencies]
python = "^3.10"
requests = "^2.31.0"
pandas = "^2.0.0"

[tool.poetry.group.dev.dependencies]
pytest = "^7.0.0"
black = "^23.0.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

---

## 🔄 Common Workflows Comparison

| Task                | npm                    | pip                               | uv                  | Poetry                       |
|---------------------|------------------------|-----------------------------------|---------------------|------------------------------|
| Install package     | `npm i --save pkg`     | `pip install pkg` (manual save)   | `uv add pkg`        | `poetry add pkg`             |
| Install dev package | `npm i --save-dev pkg` | (manual file separation)          | `uv add --dev pkg`  | `poetry add --group dev pkg` |
| Install all deps    | `npm install`          | `pip install -r requirements.txt` | `uv sync`           | `poetry install`             |
| Remove package      | `npm uninstall pkg`    | `pip uninstall pkg` (manual edit) | `uv remove pkg`     | `poetry remove pkg`          |
| Update deps         | `npm update`           | `pip install --upgrade pkg`       | `uv lock --upgrade` | `poetry update`              |
| Run script          | `npm run script`       | `python script` (in venv)         | `uv run script`     | `poetry run script`          |
| List packages       | `npm list`             | `pip list`                        | `uv pip list`       | `poetry show`                |

---

## 🚀 Speed Comparison (Installing pandas + deps)

```
pip:      ~45 seconds
Poetry:   ~30 seconds
uv:       ~2 seconds ⚡
```

---

## 🛠️ Advanced Usage

### **uv**
```bash
# Use specific Python version
uv venv --python 3.11

# Install from requirements.txt
uv pip install -r requirements.txt

# Compile requirements.txt from pyproject.toml
uv pip compile pyproject.toml -o requirements.txt

# Show dependency tree
uv tree
```

### **Poetry**
```bash
# Build package for distribution
poetry build

# Publish to PyPI
poetry publish

# Show dependency tree
poetry show --tree

# Export requirements.txt
poetry export -f requirements.txt -o requirements.txt

# Add from requirements.txt
cat requirements.txt | xargs poetry add
```

---

## 🎭 Using Both Together

You can use `uv` for fast installs and `poetry` for publishing:

```bash
# Use Poetry for project structure and metadata
poetry new my-project
cd my-project

# Use uv for fast dependency installation
uv sync

# Use Poetry for publishing
poetry build
poetry publish
```

---

## 💾 Migration

### **Poetry → uv**
```bash
# uv can read Poetry's pyproject.toml directly
uv sync  # Just works!
```

### **pip → uv**
```bash
# Convert requirements.txt to pyproject.toml
uv init
uv add $(cat requirements.txt)
```

---

## 🧠 Pro Tips

| Tip                            | Command                                  |
|--------------------------------|------------------------------------------|
| **uv**: Pin exact versions     | `uv add "package==1.2.3"`                |
| **Poetry**: Update one package | `poetry update package-name`             |
| **uv**: Use global tools       | `uv tool install black`                  |
| **Poetry**: Check for updates  | `poetry show --outdated`                 |
| **Both**: Commit lock files    | Always commit `uv.lock` or `poetry.lock` |

---

## 🎯 TL;DR

```bash
# Quick Start with pip (traditional)
python -m venv venv
source venv/bin/activate
pip install requests pandas
pip freeze > requirements.txt
python main.py

# Quick Start with uv (fast & simple)
uv init
uv add requests pandas
uv run python main.py

# Quick Start with Poetry (full-featured)
poetry new my-project
cd my-project
poetry add requests pandas
poetry run python main.py
```

**Bottom line**: Use **pip** for compatibility, **uv** for speed, **Poetry** for publishing, or **both uv+Poetry** for best of both worlds! 🚀

---

## 🎨 Code Formatting & Linting: Ruff & Black

> **JS Equivalent**: Ruff ≈ ESLint, Black ≈ Prettier

### **Quick Setup**

```bash
# Install tools
uv add --dev ruff black pre-commit
# or
poetry add --group dev ruff black pre-commit

# Basic usage
ruff check .                 # Lint all files
ruff check --fix .           # Lint and auto-fix
ruff format .                # Format code (Black-compatible)
black .                      # Format with Black

# Install pre-commit hooks
uv run pre-commit install
```

**Tip**: Ruff is an extremely fast Python linter and formatter (67x faster than Flake8). It replaces Flake8, isort, pydocstyle, pyupgrade, and more. Black is the standard Python code formatter.

📚 **For detailed configurations, rule recommendations, and pre-commit hooks, see [PythonPreCommitRecommendations.md](./PythonPreCommitRecommendations.md)**
