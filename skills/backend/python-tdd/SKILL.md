---
name: python-tdd
description: Test-driven development workflow for Python projects using pytest. Write tests first with 80%+ coverage including unit, integration, and E2E tests. Use when developing new features, fixing bugs, or refactoring Python code with pytest, pytest-asyncio, and coverage tools.
---

# Python Test-Driven Development (TDD)

This skill ensures all Python code development follows TDD principles with comprehensive test coverage using pytest, pytest-asyncio, and related tools.

## When to Activate

- Writing new Python features or functions
- Fixing bugs or issues in Python code
- Refactoring existing Python modules
- Creating API endpoints (FastAPI/Flask)
- Building data processing pipelines
- Database operations with tests

## Core Principles

### 1. Tests BEFORE Code
ALWAYS write failing tests first, then implement code to make tests pass.

### 2. Coverage Requirements
- Minimum 80% coverage (unit + integration + E2E)
- All edge cases covered
- Error scenarios tested
- Boundary conditions verified

### 3. Test Types

#### Unit Tests
- Individual functions and utilities
- Class methods and logic
- Pure functions
- Helper utilities

#### Integration Tests
- API endpoints (FastAPI/Flask/Django)
- Database operations
- Service interactions
- External API calls

#### E2E Tests (Playwright)
- Critical user flows
- Complete workflows
- Browser automation
- API interaction flows

## TDD Workflow Steps

### Step 1: Write User Stories
```
As a [role], I want to [action], so that [benefit]

Example:
As a data analyst, I want to filter datasets by date range,
so that I can focus on relevant time periods for reporting.
```

### Step 2: Generate Test Cases
For each user story, create comprehensive test cases:

```python
def test_date_filter_returns_correct_results():
    """Test that date filter returns expected data subset."""
    # Test implementation
    pass

def test_date_filter_handles_empty_range():
    """Test edge case where no data matches date range."""
    pass

def test_date_filter_falls_back_to_full_dataset_when_invalid():
    """Test fallback behavior for invalid date inputs."""
    pass

def test_date_filter_sorts_results_by_date():
    """Test that results are sorted chronologically."""
    pass
```

### Step 3: Run Tests (They Should Fail)
```bash
pytest tests/ -v
# Tests should fail - we haven't implemented yet
```

### Step 4: Implement Code
Write minimal code to make tests pass:

```python
def filter_by_date_range(data: List[dict], start_date: str, end_date: str) -> List[dict]:
    """Filter dataset by date range."""
    # Implementation here
    pass
```

### Step 5: Run Tests Again
```bash
pytest tests/ -v
# Tests should now pass
```

### Step 6: Refactor
Improve code quality while keeping tests green:
- Remove duplication
- Improve naming
- Optimize performance
- Enhance readability

### Step 7: Verify Coverage
```bash
pytest --cov=my_module --cov-report=html
# Verify 80%+ coverage achieved
```

## Testing Patterns

### Unit Test Pattern (pytest)
```python
import pytest
from my_module import Calculator

class TestCalculator:
    def test_addition_returns_correct_result(self):
        calc = Calculator()
        assert calc.add(2, 3) == 5

    def test_multiplication_with_zero(self):
        calc = Calculator()
        assert calc.multiply(0, 5) == 0

    def test_division_by_zero_raises_error(self):
        calc = Calculator()
        with pytest.raises(ZeroDivisionError):
            calc.divide(10, 0)

    def test_is_disabled_when_flag_is_true(self):
        calc = Calculator(disabled=True)
        assert calc.is_disabled() is True
```

### Async Test Pattern (pytest-asyncio)
```python
import pytest
import pytest_asyncio
from my_module import AsyncDatabase

@pytest_asyncio.fixture
async def db():
    database = AsyncDatabase()
    await database.connect()
    yield database
    await database.disconnect()

@pytest.mark.asyncio
async def test_async_query_returns_data(db):
    result = await db.query("SELECT * FROM users")
    assert len(result) > 0
    assert "id" in result[0]

@pytest.mark.asyncio
async def test_async_connection_handles_errors(db):
    with pytest.raises(ConnectionError):
        await db.query("INVALID QUERY")
```

### API Integration Test Pattern (FastAPI)
```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_get_users_returns_200():
    response = client.get("/users")
    assert response.status_code == 200
    assert response.json()["success"] is True
    assert isinstance(response.json()["data"], list)

def test_create_user_validates_input():
    response = client.post("/users", json={"name": ""})
    assert response.status_code == 422

def test_get_user_handles_not_found():
    response = client.get("/users/999")
    assert response.status_code == 404
```

### API Integration Test Pattern (Flask)
```python
from flask import Flask
from my_app import app

client = app.test_client()

def test_health_endpoint():
    response = client.get("/health")
    assert response.status_code == 200
    data = response.get_json()
    assert data["status"] == "healthy"

def test_create_item():
    response = client.post("/items", json={"name": "test"})
    assert response.status_code == 201
    assert "id" in response.get_json()
```

### E2E Test Pattern (Playwright Python)
```python
from playwright.sync_api import sync_playwright

def test_user_can_search_and_filter():
    with sync_playwright() as p:
        browser = p.chromium.launch()
        page = browser.new_page()
        
        # Navigate to page
        page.goto("/")
        page.click('a[href="/items"]')
        
        # Verify page loaded
        assert page.locator("h1").text_content() == "Items"
        
        # Search for items
        page.fill('input[placeholder="Search items"]', "widget")
        
        # Wait for results
        page.wait_for_selector('[data-testid="item-card"]')
        
        # Verify results displayed
        cards = page.locator('[data-testid="item-card"]')
        assert cards.count() == 5
        
        # Verify results contain search term
        first_card = cards.first
        assert "widget" in first_card.text_content().lower()
        
        # Filter by status
        page.click('button:has-text("Active")')
        
        # Verify filtered results
        assert cards.count() == 3
```

### Mock Pattern (unittest.mock)
```python
from unittest.mock import patch, MagicMock
import pytest
from my_service import UserService

def test_get_user_with_mocked_database():
    mock_db = MagicMock()
    mock_db.get_user.return_value = {"id": 1, "name": "Alice"}
    
    service = UserService(db=mock_db)
    user = service.get_user(1)
    
    assert user["name"] == "Alice"
    mock_db.get_user.assert_called_once_with(1)

def test_create_user_with_mocked_email():
    with patch("my_service.send_email") as mock_send:
        mock_send.return_value = True
        
        service = UserService()
        result = service.create_user("alice@example.com")
        
        assert result["email"] == "alice@example.com"
        mock_send.assert_called_once()

@patch("my_module.requests.get")
def test_external_api_call(mock_get):
    mock_response = MagicMock()
    mock_response.json.return_value = {"data": [1, 2, 3]}
    mock_response.status_code = 200
    mock_get.return_value = mock_response
    
    result = fetch_data()
    
    assert result == [1, 2, 3]
    mock_get.assert_called_once_with("https://api.example.com/data")
```

### Database Test Pattern
```python
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from database import Base, User

@pytest.fixture
def db_session():
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    Session = sessionmaker(bind=engine)
    session = Session()
    yield session
    session.close()

def test_create_user(db_session):
    user = User(name="Alice", email="alice@example.com")
    db_session.add(user)
    db_session.commit()
    
    result = db_session.query(User).filter_by(name="Alice").first()
    assert result is not None
    assert result.email == "alice@example.com"

def test_user_email_unique(db_session, db_session):
    user1 = User(name="Alice", email="alice@example.com")
    db_session.add(user1)
    db_session.commit()
    
    user2 = User(name="Bob", email="alice@example.com")
    db_session.add(user2)
    
    with pytest.raises(Exception):
        db_session.commit()
```

## Test File Organization

```
project/
├── src/
│   └── my_module/
│       ├── __init__.py
│       ├── calculator.py
│       └── calculator_test.py    # Unit tests
├── tests/
│   ├── __init__.py
│   ├── conftest.py              # pytest fixtures
│   ├── test_calculator.py       # Unit tests
│   ├── test_api.py              # Integration tests
│   └── e2e/
│       └── test_flows.py        # E2E tests
├── pytest.ini                   # pytest configuration
└── setup.py                     # package setup
```

## Pytest Configuration (pytest.ini)

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
asyncio_mode = auto
addopts = -v --tb=short
filterwarnings =
    ignore::DeprecationWarning
```

## Coverage Configuration (pyproject.toml)

```toml
[tool.pytest.ini_options]
addopts = "--cov=my_module --cov-report=term-missing --cov-report=html"

[tool.coverage.run]
source = ["my_module"]
branch = true
omit = ["tests/*", "*/__init__.py"]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
]
fail_under = 80
```

## Common Testing Mistakes to Avoid

### WRONG: Testing Implementation Details
```python
# Don't test internal state
assert calculator._state.count == 5
```

### CORRECT: Test User-Visible Behavior
```python
# Test what users see
assert calculator.get_count() == 5
```

### WRONG: Brittle Selectors
```python
# Breaks easily
page.click(".css-class-xyz")
```

### CORRECT: Semantic Selectors
```python
# Resilient to changes
page.click('button:has-text("Submit")')
page.click('[data-testid="submit-button"]')
```

### WRONG: No Test Isolation
```python
# Tests depend on each other
def test_creates_user(): ...
def test_updates_same_user(): ...  # depends on previous test
```

### CORRECT: Independent Tests
```python
# Each test sets up its own data
def test_creates_user():
    user = create_test_user()
    # Test logic

def test_updates_user():
    user = create_test_user()
    # Update logic
```

## Continuous Testing

### Watch Mode During Development
```bash
pytest-watch --verbose
# Tests run automatically on file changes
```

### Pre-Commit Hook
```bash
# .pre-commit-config.yaml
- repo: https://github.com/pre-commit/pre-commit-hooks
  rev: v4.4.0
  hooks:
    - id: pytest-check
      args: [--tb=short]
```

### CI/CD Integration
```yaml
# .github/workflows/tests.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - run: pip install -e ".[test]"
      - run: pytest --cov=my_module --cov-report=xml
      - uses: codecov/codecov-action@v3
        with:
          files: ./coverage.xml
```

## Best Practices

1. **Write Tests First** - Always TDD
2. **One Assert Per Test** - Focus on single behavior
3. **Descriptive Test Names** - Explain what's tested
4. **Arrange-Act-Assert** - Clear test structure
5. **Mock External Dependencies** - Isolate unit tests
6. **Test Edge Cases** - Null, undefined, empty, large
7. **Test Error Paths** - Not just happy paths
8. **Keep Tests Fast** - Unit tests < 50ms each
9. **Clean Up After Tests** - No side effects
10. **Review Coverage Reports** - Identify gaps

## Success Metrics

- 80%+ code coverage achieved
- All tests passing (green)
- No skipped or disabled tests
- Fast test execution (< 30s for unit tests)
- E2E tests cover critical user flows
- Tests catch bugs before production

## References

For detailed information on specific topics, see:

- [pytest-patterns.md](references/pytest-patterns.md) - Comprehensive pytest patterns and fixtures
- [unittest-patterns.md](references/unittest-patterns.md) - unittest module alternatives
- [coverage.md](references/coverage.md) - Coverage configuration and reporting

---

**Remember**: Tests are not optional. They are the safety net that enables confident refactoring, rapid development, and production reliability.
