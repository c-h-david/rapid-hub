# Testing

## Testing Overview

RAPID employs a comprehensive testing strategy that combines static code analysis with runtime testing to ensure code quality and scientific integrity.

## Static Code Analysis

RAPID uses multiple static analysis tools to enforce consistency and quality:

### Markdown Linting
```bash
pymarkdown scan *.md
```
Enforces a maximum line width of 79 characters using the `.pymarkdown.yml` configuration file.

### YAML Linting
```bash
yamllint .*.yml .github/*/*.yml
```
Validates YAML configuration files with a maximum line width of 79 characters using `.yamllint.yml`.

### Dockerfile Linting
```bash
hadolint --ignore DL3008 --ignore SC2046 Dockerfile
wc -L Dockerfile | awk '{exit $1 > 79}'
```
Lints Docker configuration and enforces 79-character line width.

### Python Code Linting
```bash
flake8 src/*.py src/rapid2/*.py
```
Enforces Python code style with a maximum line width of 79 characters.

### Python Type Checking
```bash
mypy --strict src/*.py src/rapid2/*.py
```
Performs strict static and dynamic typing analysis.

## Runtime Testing

### Python Docstring Testing
```bash
python3 -m doctest src/rapid2/*.py
```
Validates examples in function docstrings to ensure code documentation remains accurate.

### Scientific Integrity Testing
*Replication of past results testing is planned for future implementation.*

## Testing Guidelines

### For Developers
1. **Run all static analysis tools** before submitting code
2. **Ensure doctest examples pass** for any modified functions
3. **Maintain type hints** for all function parameters and returns
4. **Follow naming conventions** and code style guidelines

### Test Commands Summary
Run all tests with these commands:
```bash
# Static analysis
pymarkdown scan *.md
yamllint .*.yml .github/*/*.yml
hadolint --ignore DL3008 --ignore SC2046 Dockerfile
flake8 src/*.py src/rapid2/*.py
mypy --strict src/*.py src/rapid2/*.py

# Runtime testing
python3 -m doctest src/rapid2/*.py
```

---

For complete testing procedures and configuration details, see [TESTING.md]({{ extra.rapid_repo }}/blob/main/TESTING.md) in the [RAPID repository]({{ extra.rapid_repo }}).
