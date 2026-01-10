# Python Pre-commit & Ruff Configuration Recommendations

> Community-recommended packages and configurations for Python pre-commit hooks and Ruff linting rules

---

## 📋 Essential Pre-commit Hooks

### **Official pre-commit-hooks**
The most widely-used collection from https://github.com/pre-commit/pre-commit-hooks

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      # File formatting
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: mixed-line-ending
        args: [--fix=lf]

      # File validation
      - id: check-yaml
      - id: check-json
      - id: check-toml
      - id: check-xml

      # Python checks
      - id: check-ast
      - id: debug-statements
      - id: check-builtin-literals

      # Security
      - id: detect-private-key

      # Repository health
      - id: check-added-large-files
        args: [--maxkb=1000]
      - id: check-merge-conflict
      - id: check-case-conflict
```

---

## 🔒 Security Hooks

### **Gitleaks** (Recommended - Fast & Lightweight)
Prevents secrets from being committed (API keys, passwords, tokens)

```yaml
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.1
    hooks:
      - id: gitleaks
```

### **detect-secrets** (Alternative - More Comprehensive)
```yaml
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
```

---

## 🎨 Formatting & Linting

### **Ruff** (Modern All-in-One Solution)
```yaml
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.9
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
```

### **Black** (If not using ruff format)
```yaml
  - repo: https://github.com/psf/black
    rev: 23.12.1
    hooks:
      - id: black
```

### **isort** (If not using ruff's isort rules)
```yaml
  - repo: https://github.com/pycqa/isort
    rev: 5.13.2
    hooks:
      - id: isort
```

---

## 🔍 Type Checking

### **mypy** (Essential for Type Safety)
```yaml
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
        additional_dependencies: [types-requests, types-PyYAML]
        args: [--strict, --ignore-missing-imports]
```

---

## 📝 Documentation

### **pydocstyle** (Docstring Conventions)
```yaml
  - repo: https://github.com/pycqa/pydocstyle
    rev: 6.3.0
    hooks:
      - id: pydocstyle
        args: [--convention=google]  # or numpy, pep257
```

### **interrogate** (Docstring Coverage)
```yaml
  - repo: https://github.com/econchick/interrogate
    rev: 1.5.0
    hooks:
      - id: interrogate
        args: [--verbose, --fail-under=80]
```

### **pymarkdownlnt** (Markdown Linting)
```yaml
  - repo: https://github.com/jackdewinter/pymarkdown
    rev: v0.9.14
    hooks:
      - id: pymarkdown
        args: [scan]
```

---

## 🚀 Recommended Ruff Rule Sets

### **For General Projects**
```toml
[tool.ruff]
line-length = 88
target-version = "py310"

select = [
    "E",      # pycodestyle errors
    "W",      # pycodestyle warnings
    "F",      # pyflakes
    "I",      # isort
    "N",      # pep8-naming
    "UP",     # pyupgrade
    "B",      # flake8-bugbear
    "C4",     # flake8-comprehensions
    "SIM",    # flake8-simplify
    "RET",    # flake8-return
    "PTH",    # flake8-use-pathlib
]

ignore = [
    "E501",   # Line too long (Black/ruff format handles this)
]
```

### **For Libraries/Packages**
```toml
[tool.ruff]
select = [
    "E",      # pycodestyle errors
    "W",      # pycodestyle warnings
    "F",      # pyflakes
    "UP",     # pyupgrade
    "B",      # flake8-bugbear
    "I",      # isort
    "D",      # pydocstyle (docstring checks)
]

[tool.ruff.pydocstyle]
convention = "google"  # or "numpy", "pep257"
```

### **For Data Science Projects**
```toml
[tool.ruff]
select = [
    "E",      # pycodestyle errors
    "W",      # pycodestyle warnings
    "F",      # pyflakes
    "UP",     # pyupgrade
    "B",      # flake8-bugbear
    "I",      # isort
    "PD",     # pandas-vet
    "NPY",    # numpy-specific rules
]
```

### **Aggressive/Strict Configuration**
```toml
[tool.ruff]
select = [
    "E",      # pycodestyle errors
    "W",      # pycodestyle warnings
    "F",      # pyflakes
    "I",      # isort
    "N",      # pep8-naming
    "UP",     # pyupgrade
    "B",      # flake8-bugbear
    "C4",     # flake8-comprehensions
    "SIM",    # flake8-simplify
    "RET",    # flake8-return
    "PTH",    # flake8-use-pathlib
    "TD",     # flake8-todos
    "PL",     # pylint
    "PERF",   # perflint (performance)
    "RUF",    # ruff-specific rules
]

[tool.ruff.per-file-ignores]
"__init__.py" = ["F401"]  # Unused imports
"tests/**/*.py" = ["S101", "PLR2004"]  # Allow assert, magic values
```

---

## 🎯 Complete Example Configuration

### **Minimal Setup (Quick Start)**
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
      - id: debug-statements

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.9
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
```

### **Production-Ready Setup (Comprehensive)**
```yaml
# .pre-commit-config.yaml
repos:
  # File checks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-toml
      - id: check-ast
      - id: check-added-large-files
      - id: check-merge-conflict
      - id: debug-statements
      - id: detect-private-key

  # Security
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.1
    hooks:
      - id: gitleaks

  # Python formatting & linting
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.9
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix]
      - id: ruff-format

  # Type checking
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
        additional_dependencies: [types-requests]
        args: [--ignore-missing-imports]

  # Docstring coverage
  - repo: https://github.com/econchick/interrogate
    rev: 1.5.0
    hooks:
      - id: interrogate
        args: [--fail-under=80, --verbose]
```

---

## 🧠 Rule Set Descriptions

### Core Rules (Always Enable)
- **E, W** - PyCodeStyle errors and warnings (PEP 8)
- **F** - Pyflakes (logical errors, unused imports)
- **I** - Import sorting (like isort)

### Code Quality
- **N** - PEP 8 naming conventions
- **UP** - Modernize Python syntax (f-strings, type hints)
- **B** - Flake8-bugbear (common bugs and design problems)
- **C4** - Comprehension improvements
- **SIM** - Code simplification suggestions

### Best Practices
- **RET** - Return statement best practices
- **PTH** - Prefer pathlib over os.path
- **TD** - TODO comment formatting
- **PL** - Pylint rules (comprehensive)
- **PERF** - Performance anti-patterns

### Domain-Specific
- **D** - Pydocstyle (docstring conventions)
- **PD** - Pandas-vet (pandas best practices)
- **NPY** - NumPy-specific rules

---

## 📊 Configuration Strategy

### Start Small
```bash
# 1. Begin with defaults
ruff check .

# 2. Add one rule group at a time
# 3. Fix issues before adding more rules
# 4. Document why each rule is enabled
```

### Gradual Adoption
1. **Week 1**: E, W, F (defaults)
2. **Week 2**: Add I (imports)
3. **Week 3**: Add UP, B (upgrades, bugs)
4. **Week 4**: Add N, C4, SIM (style, simplification)
5. **Later**: Add domain-specific rules (D, PD, NPY)

### Team Configuration Tips
- Always comment each rule in your config
- Use `per-file-ignores` for test files
- Run `ruff check --fix` before manual fixes
- Keep pre-commit hooks fast (<10 seconds)
- Pin versions in `.pre-commit-config.yaml`

---

## ⚡ Performance Notes

**Ruff Speed**: 50k lines in 120ms (vs Flake8's 8 seconds = **67x faster**)

**Pre-commit Best Practices**:
- Use `language_version` to pin Python version
- Use `files` regex to limit scope
- Avoid slow hooks in pre-commit (run in CI instead)
- Cache dependencies with `additional_dependencies`

---

## 🔗 Useful Resources

- [Ruff Rules Documentation](https://docs.astral.sh/ruff/rules/)
- [Pre-commit Hooks Repository](https://github.com/pre-commit/pre-commit-hooks)
- [Ruff Configuration Guide](https://docs.astral.sh/ruff/configuration/)
- [Pre-commit Official Site](https://pre-commit.com/)
