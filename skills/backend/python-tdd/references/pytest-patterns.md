# Pytest Patterns and Fixtures

This reference covers comprehensive pytest patterns, fixtures, and best practices for Python testing.

## Fixture Patterns

### Basic Fixture
```python
import pytest

@pytest.fixture
def sample_data():
    return {"name": "test", "value": 42}

def test_uses_fixture(sample_data):
    assert sample_data["value"] == 42
```

### Fixture with Setup/Teardown
```python
@pytest.fixture
def database():
    db = DatabaseConnection()
    db.connect()
    yield db
    db.close()

@pytest.fixture
def temp_file(tmp_path):
    file = tmp_path / "test.txt"
    file.write_text("initial")
    return file
```

### Fixture Scope
```python
@pytest.fixture(scope="session")
def browser():
    return setup_browser()

@pytest.fixture(scope="module")
def db_session():
    return create_test_session()

@pytest.fixture(scope="function")
def fresh_user():
    return create_fresh_user()
```

### Fixture with Dependencies
```python
@pytest.fixture
def authenticated_client(client, login):
    return client.with_auth(login)
```

### Factory Fixture
```python
@pytest.fixture
def user_factory(db):
    def create_user(name="Test User"):
        return db.users.create(name=name)
    return create_user

def test_create_multiple_users(user_factory):
    user1 = user_factory("Alice")
    user2 = user_factory("Bob")
    assert user1.id != user2.id
```

### Parametrized Fixture
```python
@pytest.fixture(params=[1, 2, 3])
def numeric_value(request):
    return request.param

def test_numeric_operations(numeric_value):
    assert numeric_value * 2 == numeric_value + numeric_value
```

## Parametrized Tests

### Basic Parametrization
```python
@pytest.mark.parametrize("input,expected", [
    ("2+2", 4),
    ("3+5", 8),
    ("10-5", 5),
])
def test_calculator_parsing(input, expected):
    assert eval(input) == expected
```

### Multiple Parameters
```python
@pytest.mark.parametrize("a,b,result", [
    (2, 3, 5),
    (0, 0, 0),
    (-1, 1, 0),
    (100, 200, 300),
])
def test_addition(a, b, result):
    assert a + b == result
```

### Parametrize with IDs
```python
@pytest.mark.parametrize("input,expected", [
    ("hello", "HELLO"),
    ("world", "WORLD"),
    ("test", "TEST"),
], ids=["hello_to_uppercase", "world_to_uppercase", "test_to_uppercase"])
def test_uppercase(input, expected):
    assert input.upper() == expected
```

## Markers

### Custom Markers
```python
# pytest.ini
[pytest]
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    integration: marks integration tests
    api: marks API tests

@pytest.mark.slow
def test_heavy_computation():
    ...

@pytest.mark.integration
def test_database_integration():
    ...

@pytest.mark.api
def test_api_endpoint():
    ...
```

### Skip and SkipIf
```python
import pytest
import sys

@pytest.mark.skip(reason="Feature not implemented yet")
def test_future_feature():
    ...

@pytest.mark.skipif(sys.version_info < (3, 10), reason="Requires Python 3.10+")
def test_new_feature():
    ...
```

### Filter Warnings
```python
import warnings

@pytest.mark.filterwarnings("ignore::DeprecationWarning")
def test_old_code():
    ...
```

## Assertions

### Basic Assertions
```python
def test_assertions():
    assert True
    assert 1 + 1 == 2
    assert "hello" in "hello world"
    assert [1, 2] == [1, 2]
```

### Exception Testing
```python
def test_raises_exception():
    with pytest.raises(ValueError):
        raise ValueError("Invalid value")

def test_raises_exception_with_message():
    with pytest.raises(ValueError, match="Invalid"):
        raise ValueError("Invalid value")
```

### Approximate Comparisons
```python
import pytest

def test_float_comparison():
    assert 0.1 + 0.2 == pytest.approx(0.3)

def test_list_comparison():
    assert [0.1, 0.2] == pytest.approx([0.1, 0.2])

def test_dict_comparison():
    assert {"x": 0.1, "y": 0.2} == pytest.approx({"x": 0.1, "y": 0.2})
```

### raises for Context Manager
```python
def test_raises_in_context_manager():
    with pytest.raises((ValueError, TypeError)):
        if True:
            raise ValueError()
        else:
            raise TypeError()
```

## Mocking with pytest-mock

### Basic Mock
```python
from unittest.mock import Mock, MagicMock

def test_with_mock(mocker):
    mock_db = mocker.patch("myapp.database")
    mock_db.get_user.return_value = {"id": 1, "name": "Alice"}
    
    result = myapp.get_user_name(1)
    assert result == "Alice"
```

### Spy
```python
def test_with_spy(mocker):
    spy = mocker.spy(math, "sqrt")
    
    result = calculate(16)
    
    assert result == 4
    spy.assert_called_once_with(16)
```

### Stub
```python
def test_with_stub(mocker):
    stub = mocker.stub()
    stub(1, 2, 3)
    
    assert stub.call_count == 1
```

### Mock Property
```python
def test_mock_property(mocker):
    mock_service = mocker.patch.object(ServiceClass, "property", new_callable=PropertyMock, return_value=42)
```

## Async Testing

### pytest-asyncio Configuration
```python
# pytest.ini
[pytest]
asyncio_mode = auto
```

### Async Tests
```python
import pytest

@pytest.mark.asyncio
async def test_async_operation():
    result = await async_function()
    assert result == "expected"

@pytest.mark.asyncio
async def test_async_with_timeout():
    result = await asyncio.wait_for(async_function(), timeout=5.0)
    assert result == "expected"
```

### Async Fixtures
```python
import pytest_asyncio

@pytest_asyncio.fixture
async def async_db():
    db = AsyncDatabase()
    await db.connect()
    yield db
    await db.disconnect()

@pytest.mark.asyncio
async def test_async_query(async_db):
    result = await async_db.query("SELECT 1")
    assert result == [(1,)]
```

## Temporary Files and Directories

### tmp_path Fixture
```python
def test_file_operations(tmp_path):
    file = tmp_path / "test.txt"
    file.write_text("content")
    
    result = read_file(str(file))
    assert result == "content"
```

### tmp_path Factory
```python
def test_multiple_files(tmp_path_factory):
    dir1 = tmp_path_factory.mktemp("dir1")
    dir2 = tmp_path_factory.mktemp("dir2")
    
    file1 = dir1 / "file1.txt"
    file2 = dir2 / "file2.txt"
    
    file1.write_text("content1")
    file2.write_text("content2")
```

### tmpdir Fixture
```python
def test_with_tmpdir(tmpdir):
    file = tmpdir.join("test.txt")
    file.write("content")
    
    result = read_file(file.strpath)
    assert result == "content"
```

## Monkeypatching

### Environment Variables
```python
def test_with_env_var(monkeypatch):
    monkeypatch.setenv("API_KEY", "test-key-123")
    
    result = get_api_key()
    assert result == "test-key-123"
```

### Dictionary Values
```python
def test_with_dict_monkeypatch(monkeypatch):
    test_config = {"debug": True, "timeout": 30}
    monkeypatch.setattr(settings, "CONFIG", test_config)
    
    result = load_config()
    assert result["debug"] is True
```

### Class Methods
```python
def test_patch_class_method(monkeypatch):
    monkeypatch.setattr(MyClass, "class_method", lambda: "mocked")
    
    result = MyClass.class_method()
    assert result == "mocked"
```

## Coverage Integration

### Running with Coverage
```bash
pytest --cov=my_module --cov-report=term-missing
```

### Coverage per Test
```bash
pytest --cov=my_module --cov-report=term-missing --cov-context=test
```

### Selective Coverage
```bash
pytest --cov=my_module --cov-report=html --cov-branch tests/unit/
```

## Plugins

### Common pytest Plugins
```bash
pip install pytest-cov pytest-asyncio pytest-mock pytest-xdist pytest-repeat
```

### pytest-xdist (Parallel Testing)
```bash
pytest -n auto  # Use all CPU cores
pytest -n 4     # Use 4 workers
```

### pytest-repeat
```python
@pytest.mark.repeat(10)
def test_repeated():
    ...

# Run test 100 times
pytest --repeat=100 test_file.py
```

## Configuration

### pytest.ini
```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts = -v --tb=short -p no:warnings
filterwarnings =
    ignore::DeprecationWarning
    ignore::PendingDeprecationWarning
asyncio_mode = auto
```

### pyproject.toml
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
addopts = ["-v", "--tb=short"]
asyncio_mode = "auto"

[tool.pytest]
asyncio_mode = "auto"
```

### setup.cfg
```ini
[tool:pytest]
testpaths = tests
python_files = test_*.py
addopts = -v --tb=short
```

## Best Practices

1. **Use fixtures for setup/teardown** - Keep tests clean and DRY
2. **Parametrize repetitive tests** - Reduce code duplication
3. **Use meaningful test names** - Describe what is being tested
4. **Test one thing per test** - Single responsibility
5. **Use markers for categorization** - Easy test selection
6. **Keep tests fast** - Unit tests should be < 50ms
7. **Mock external dependencies** - Isolate unit tests
8. **Use descriptive assertions** - Clear failure messages
9. **Avoid test interdependencies** - Tests should run independently
10. **Clean up after tests** - No side effects between tests
