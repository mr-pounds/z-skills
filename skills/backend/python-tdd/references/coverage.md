# Test Coverage Configuration and Reporting

This reference covers coverage.py configuration, reporting options, and best practices for maintaining high test coverage in Python projects.

## Installation

```bash
pip install coverage
```

## Basic Usage

### Running Tests with Coverage
```bash
coverage run -m pytest
coverage run -m unittest discover
coverage run --source=my_module test_module.py
```

### Generating Reports
```bash
coverage report          # Terminal report
coverage html            # HTML report
coverage xml             # XML report (for CI)
coverage json            # JSON report
```

## Configuration

### .coveragerc
```ini
[run]
source = my_module
omit = */tests/*
       */__init__.py
       */conftest.py
branch = True
parallel = True
concurrency = thread
timing = True

[report]
exclude_lines = pragma: no cover
    def __repr__
    raise AssertionError
    raise NotImplementedError
    if TYPE_CHECKING:
    @abstractmethod
show_missing = True
skip_empty = True

[html]
directory = coverage_html
title = MyModule Coverage Report
show_contexts = True

[xml]
output = coverage.xml
```

### pyproject.toml
```toml
[tool.coverage.run]
source = ["my_module"]
branch = true
omit = [
    "tests/*",
    "*/__init__.py",
    "*/conftest.py",
]
parallel = true
concurrency = ["thread"]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
    "if TYPE_CHECKING:",
    "@abstractmethod",
]
show_missing = true
skip_empty = true
fail_under = 80

[tool.coverage.html]
directory = "coverage_html"
title = "MyModule Coverage Report"
show_contexts = true

[tool.coverage.xml]
output = "coverage.xml"
```

### setup.cfg
```ini
[coverage:run]
source = my_module
branch = True
omit = tests/*,*/__init__.py

[coverage:report]
exclude_lines = pragma: no cover
show_missing = True
fail_under = 80
```

## Coverage Types

### Statement Coverage
```bash
coverage run test_module.py
coverage report
```

### Branch Coverage
```bash
coverage run --branch test_module.py
coverage report
```

### Line Coverage
```bash
coverage run --parallel-mode test_module.py
coverage report --show-missing
```

## Excluding Code

### File-level Exclusion
```python
# coverage: exclude
def unreachable_code():
    pass
```

### Line-level Exclusion
```python
if condition:  # pragma: no cover
    do_something()

try:
    complex_operation()
except Exception:  # pragma: no cover
    handle_error()
```

### Pattern Exclusion
```ini
[report]
exclude_patterns = */test_*,
                   */legacy/*,
                   */migrations/*
```

### Module-level Exclusion
```bash
coverage run --omit="*/test_helpers.py" test_module.py
```

## Reporting Options

### Terminal Report
```bash
coverage report --show-missing
coverage report -i  # Include ignored files
```

### HTML Report
```bash
coverage html
# Open htmlcov/index.html in browser
```

### XML Report (CI/CD)
```bash
coverage xml -o coverage.xml
```

### JSON Report
```bash
coverage json -o coverage.json
```

### Annotated Source
```bash
coverage annotate
# Creates .coveragg files with coverage markers
```

## Coverage Thresholds

### Minimum Coverage Enforcement
```bash
coverage run test_module.py
coverage report --fail-under=80
```

### Strict Mode
```bash
coverage run test_module.py
coverage check --fail-under=80 --strict
```

### Partial Branch Coverage
```bash
# In .coveragerc
[report]
partial_branches = if TYPE_CHECKING:
                       ...
                   else:
                       ...
```

## Combining Coverage Data

### Parallel Test Runs
```bash
coverage run --parallel-mode -m pytest -n 4
coverage combine
coverage report
```

### Multi-module Projects
```bash
coverage run --source=module1 test_module1.py
coverage run --source=module2 test_module2.py
coverage combine
coverage report
```

### Docker/CI Environments
```bash
# In Dockerfile
RUN coverage run --parallel-mode manage.py test
RUN coverage combine
RUN coverage report --fail-under=80
```

## CI/CD Integration

### GitHub Actions
```yaml
name: Coverage

on: [push, pull_request]

jobs:
  coverage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - run: pip install coverage pytest
      - run: coverage run -m pytest
      - run: coverage report --fail-under=80
      - run: coverage xml -o coverage.xml
      - uses: codecov/codecov-action@v3
        with:
          files: ./coverage.xml
```

### GitLab CI
```yaml
test:
  image: python:3.11
  script:
    - pip install coverage pytest
    - coverage run -m pytest
    - coverage report --fail-under=80
    - coverage xml
  artifacts:
    reports:
      junit: report.xml
      coverage_report:
        coverage.xml
```

### CircleCI
```yaml
workflows:
  version: 2
  test:
    jobs:
      - test:
          post-steps:
            - run:
                name: Coverage report
                command: |
                  pip install coverage
                  coverage report --fail-under=80
                  codecov
```

## Coverage Metrics

### What to Measure
- **Statements**: Every executable line
- **Branches**: Conditional paths (if/else, ternary)
- **Functions**: Function/method entry points
- **Lines**: Individual lines of code

### Coverage Goals
| Metric     | Minimum | Good | Excellent |
| ---------- | ------- | ---- | --------- |
| Statements | 80%     | 90%  | 95%       |
| Branches   | 70%     | 80%  | 90%       |
| Functions  | 90%     | 95%  | 100%      |
| Lines      | 80%     | 90%  | 95%       |

## Best Practices

### 1. Start with High Coverage
```bash
# New project
coverage run --branch -m pytest
coverage report --fail-under=90
```

### 2. Exclude Appropriately
```python
# Test infrastructure
if TYPE_CHECKING:  # pragma: no cover
    from typing import TYPE_CHECKING

# Abstract methods
@abstractmethod  # pragma: no cover
def abstract_method(self):
    pass
```

### 3. Use Branch Coverage
```bash
coverage run --branch test_module.py
```

### 4. Monitor Over Time
```bash
# Add to CI
coverage run --branch test_module.py
coverage report --fail-under=80
coverage history  # Track trends
```

### 5. Focus on Missing Coverage
```bash
coverage report --show-missing
# Look for "Missing" column entries
```

### 6. Combine with Code Quality
```bash
# Install tools
pip install coverage flake8 pylint

# Run all checks
coverage run -m pytest && coverage report --fail-under=80
flake8 my_module/
pylint my_module/
```

### 7. Incremental Coverage
```bash
# Check new code
coverage run -m pytest tests/new_feature/
coverage report --show-missing
```

### 8. Don't Sacrifice Quality
- High coverage doesn't mean good tests
- Test behavior, not implementation
- Cover edge cases and error paths
- Avoid testing trivial code just for coverage

## Troubleshooting

### Low Coverage on New Code
```bash
# Use incremental coverage
coverage run --append -m pytest
coverage report
```

### Parallel Test Issues
```bash
# Ensure --parallel-mode is used
coverage run --parallel-mode -m pytest -n auto
coverage combine
coverage report
```

### Dynamic Code Coverage
```bash
# For dynamic imports
coverage run --source=package_name test_module.py
```

### Thread/Async Coverage
```bash
# For threading
coverage run --concurrency=thread test_module.py

# For asyncio
coverage run --concurrency=multiprocessing test_module.py
```

## Advanced Configuration

### Context Coverage
```bash
coverage run --context=test_class test_module.py
coverage report --show-contexts
```

### Dynamic Exclusion
```python
# In test files
def skip_in_coverage(func):
    """Decorator to skip coverage for debug functions."""
    func.__coverage__ = False
    return func
```

### Custom Reporters
```python
from coverage.python import PythonFileReporter

class MyReporter:
    def report(self, analysis):
        # Custom reporting logic
        pass
```
