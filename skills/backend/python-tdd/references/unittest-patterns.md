# Unittest Patterns and Best Practices

This reference covers the Python standard library unittest module as an alternative to pytest for test-driven development.

## Basic Test Structure

### TestCase Class
```python
import unittest

class TestMathOperations(unittest.TestCase):
    def test_addition(self):
        self.assertEqual(2 + 2, 4)
    
    def test_multiplication(self):
        self.assertEqual(3 * 5, 15)
    
    def test_division(self):
        self.assertEqual(10 / 2, 5)
```

### Running Tests
```bash
python -m unittest test_module.py
python -m unittest discover -s tests
python -m unittest test_module.TestClass.test_method
```

## Assertions

### Equality Assertions
```python
class TestAssertions(unittest.TestCase):
    def test_assertEqual(self):
        self.assertEqual(actual, expected)
    
    def test_assertNotEqual(self):
        self.assertNotEqual(actual, expected)
```

### Boolean Assertions
```python
class TestBoolean(unittest.TestCase):
    def test_assertTrue(self):
        self.assertTrue(condition)
    
    def test_assertFalse(self):
        self.assertFalse(condition)
    
    def test_assertIs(self):
        self.assertIs(obj1, obj2)
    
    def test_assertIsNot(self):
        self.assertIsNot(obj1, obj2)
```

### Collection Assertions
```python
class TestCollections(unittest.TestCase):
    def test_assertListEqual(self):
        self.assertListEqual(actual_list, expected_list)
    
    def test_assertDictEqual(self):
        self.assertDictEqual(actual_dict, expected_dict)
    
    def test_assertSetEqual(self):
        self.assertSetEqual(actual_set, expected_set)
    
    def test_assertIn(self):
        self.assertIn(item, collection)
    
    def test_assertNotIn(self):
        self.assertNotIn(item, collection)
```

### Exception Assertions
```python
class TestExceptions(unittest.TestCase):
    def test_assertRaises(self):
        with self.assertRaises(ValueError):
            raise ValueError("Invalid value")
    
    def test_assertRaisesRegex(self):
        with self.assertRaisesRegex(ValueError, r"Invalid.*"):
            raise ValueError("Invalid input")
```

### Approximate Assertions
```python
class TestApproximate(unittest.TestCase):
    def test_assertAlmostEqual(self):
        self.assertAlmostEqual(0.1 + 0.2, 0.3, places=5)
    
    def test_assertNotAlmostEqual(self):
        self.assertNotAlmostEqual(1.0, 2.0, places=5)
```

### Logging Assertions
```python
class TestLogging(unittest.TestCase):
    def test_assertLogs(self):
        with self.assertLogs('my_module', level='INFO') as cm:
            my_module.do_something()
        self.assertIn('Something happened', cm.output[0])
```

## Setup and Teardown

### setUp and tearDown
```python
class TestDatabase(unittest.TestCase):
    def setUp(self):
        self.db = Database()
        self.db.connect()
    
    def tearDown(self):
        self.db.disconnect()
    
    def test_query(self):
        result = self.db.query("SELECT 1")
        self.assertEqual(result, [(1,)])
```

### setUpClass and tearDownClass
```python
class TestDatabaseClass(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls.db = Database()
        cls.db.connect()
    
    @classmethod
    def tearDownClass(cls):
        cls.db.disconnect()
    
    def test_query(self):
        result = self.db.query("SELECT 1")
        self.assertEqual(result, [(1,)])
```

### setUpModule and tearDownModule
```python
def setUpModule():
    global shared_resource
    shared_resource = initialize_resource()

def tearDownModule():
    global shared_resource
    cleanup_resource(shared_resource)
```

## Mocking with unittest.mock

### Basic Mock
```python
from unittest.mock import Mock

def test_with_mock(self):
    mock_db = Mock()
    mock_db.get_user.return_value = {"id": 1, "name": "Alice"}
    
    service = UserService(mock_db)
    result = service.get_user(1)
    
    self.assertEqual(result["name"], "Alice")
    mock_db.get_user.assert_called_once_with(1)
```

### MagicMock
```python
from unittest.mock import MagicMock

def test_with_magicmock(self):
    mock_service = MagicMock()
    mock_service.method.return_value = "result"
    
    result = mock_service.method()
    
    self.assertEqual(result, "result")
    mock_service.method.assert_called_once()
```

### Patch
```python
from unittest.mock import patch

@patch('my_module.external_service')
def test_with_patch(self, mock_service):
    mock_service.get_data.return_value = {"key": "value"}
    
    result = my_module.process()
    
    self.assertEqual(result["key"], "value")
    mock_service.get_data.assert_called_once()
```

### Property Mock
```python
from unittest.mock import PropertyMock

@patch.object(MyClass, 'my_property', new_callable=PropertyMock, return_value=42)
def test_property_mock(self, mock_property):
    obj = MyClass()
    self.assertEqual(obj.my_property, 42)
```

### Mocking Context Manager
```python
from unittest.mock import patch

def test_context_manager(self):
    mock_file = MagicMock()
        __enter__ = MagicMock(return_value=mock_file)
        __exit__ = MagicMock(return_value=False)
    
    with patch('builtins.open', return_value=mock_file):
        result = read_file("test.txt")
    
    self.assertTrue(mock_file.read.called)
```

## SubTest for Parametrization

### Basic SubTest
```python
class TestMath(unittest.TestCase):
    def test_addition_cases(self):
        test_cases = [
            (1, 2, 3),
            (0, 0, 0),
            (-1, 1, 0),
            (100, 200, 300),
        ]
        
        for a, b, expected in test_cases:
            with self.subTest(a=a, b=b):
                self.assertEqual(a + b, expected)
```

### SubTest with Failure Tracking
```python
class TestDataProcessing(unittest.TestCase):
    def test_process_items(self):
        items = [
            ("item1", "valid"),
            ("item2", "valid"),
            ("item3", "invalid"),
        ]
        
        for name, status in items:
            with self.subTest(item=name):
                if status == "valid":
                    self.assertTrue(process_item(name))
                else:
                    with self.assertRaises(ProcessingError):
                        process_item(name)
```

## Skipping Tests

### Skip Decorator
```python
import unittest

@unittest.skip("Feature not implemented")
def test_future_feature(self):
    ...

@unittest.skipIf(sys.version_info < (3, 10), "Requires Python 3.10+")
def test_new_feature(self):
    ...

@unittest.skipUnless(sys.platform == "win32", "Windows only")
def test_windows_feature(self):
    ...
```

### Skip with Reason
```python
@unittest.skip("Bug #123: This test fails intermittently")
def test_flaky_feature(self):
    ...
```

### Skip in setUp
```python
def setUp(self):
    if not some_condition:
        self.skipTest("Required dependency not available")
```

## Testing Exceptions

### assertRaises
```python
def test_exception(self):
    with self.assertRaises(ValueError):
        validate_input("")
```

### assertRaisesRegex
```python
def test_exception_message(self):
    with self.assertRaisesRegex(ValueError, r"Invalid.*"):
        validate_input("bad")
```

### assertRaises Context Manager
```python
def test_exception_context(self):
    try:
        with self.assertRaises(ValueError):
            raise ValueError("test error")
    except self.failureException:
        self.fail("Exception not raised as expected")
```

## Test Discovery

### Discovery Pattern
```bash
python -m unittest discover -s tests -p "test_*.py"
python -m unittest discover -s tests -p "*_test.py"
python -m unittest discover -s tests -v
```

### Custom Discovery
```python
import unittest

loader = unittest.TestLoader()
suite = loader.discover('tests', pattern='test_*.py', top_level_dir='.')
runner = unittest.TextTestRunner(verbosity=2)
runner.run(suite)
```

## Organizing Tests

### File Structure
```
project/
├── src/
│   └── my_module/
│       ├── __init__.py
│       └── module.py
├── tests/
│   ├── __init__.py
│   ├── test_module.py
│   ├── test_utils.py
│   └── test_integration.py
└── unittest.cfg
```

### unittest.cfg
```ini
[unittest]
verbosity = 2
failfast = True
catchwarnings = True
```

## Test Fixtures with mock

### Mocked Setup
```python
from unittest.mock import patch, MagicMock

class TestWithMocks(unittest.TestCase):
    def setUp(self):
        self.mock_db = MagicMock()
        self.mock_db.get_users.return_value = [
            {"id": 1, "name": "Alice"},
            {"id": 2, "name": "Bob"},
        ]
        self.service = UserService(self.mock_db)
    
    def test_get_users(self):
        users = self.service.get_users()
        self.assertEqual(len(users), 2)
        self.mock_db.get_users.assert_called_once()
```

### Patching at Class Level
```python
@patch('my_module.Database')
class TestDatabasePatched(unittest.TestCase):
    def test_query(self, mock_db):
        mock_instance = mock_db.return_value
        mock_instance.query.return_value = [(1,)]
        
        result = my_module.some_function()
        
        self.assertEqual(result, [(1,)])
        mock_instance.query.assert_called_once()
```

## Best Practices

1. **Use descriptive test names** - `test_user_creation_success` instead of `test1`
2. **One assertion per test** - When possible, test one thing at a time
3. **Test edge cases** - Empty, None, large values, boundaries
4. **Use setUp/tearDown** - Avoid code duplication in setup
5. **Mock external dependencies** - Isolate unit tests
6. **Use subTest for parametrization** - Track individual failures
7. **Keep tests fast** - Unit tests should be quick
8. **Avoid test dependencies** - Tests should run independently
9. **Use meaningful assertions** - Clear failure messages
10. **Clean up resources** - Proper teardown in tearDown
