# Python Advanced for Automation: Cheatsheet

## Subprocess

| Operation | Syntax | Example |
|-----------|--------|---------|
| Run command | `subprocess.run(cmd)` | `subprocess.run(["ls", "-la"])` |
| Capture output | `capture_output=True` | `subprocess.run(cmd, capture_output=True)` |
| Text mode | `text=True` | Returns strings not bytes |
| Shell command | `shell=True` | `subprocess.run("ls -la", shell=True)` |
| Get output | `result.stdout` | Access captured stdout |
| Get errors | `result.stderr` | Access captured stderr |
| Return code | `result.returncode` | 0=success, else error |
| Timeout | `timeout=10` | Raise TimeoutExpired after 10s |
| Check | `check=True` | Raise error if returncode != 0 |

## Regular Expressions (re)

| Operation | Syntax | Example |
|-----------|--------|---------|
| Search | `re.search(pattern, text)` | `re.search(r"error", log)` |
| Find all | `re.findall(pattern, text)` | `re.findall(r"\d+", text)` |
| Replace | `re.sub(pattern, repl, text)` | `re.sub(r"\s+", " ", text)` |
| Split | `re.split(pattern, text)` | `re.split(r",", csv_line)` |
| Match | `re.match(pattern, text)` | Match at start only |
| Compile | `re.compile(pattern)` | Pre-compile for reuse |
| Group | Match with `()` | `(abc)` creates group 1 |
| Escape | `re.escape(text)` | Escape special chars |

## Common Regex Patterns

| Pattern | Matches | Example |
|---------|---------|---------|
| `\d` | Digit | `[0-9]` |
| `\w` | Word char | `[a-zA-Z0-9_]` |
| `\s` | Whitespace | Space, tab, newline |
| `.` | Any char | Except newline |
| `+` | One or more | `a+` matches a, aa, aaa |
| `*` | Zero or more | `a*` matches "", a, aa |
| `?` | Optional | `a?` matches "", a |
| `^` | Start | `^error` at line start |
| `$` | End | `error$` at line end |
| `[]` | Set | `[a-z]` any lowercase |
| `[^]` | Negated | `[^0-9]` not digit |

## API Requests

| Operation | Syntax | Example |
|-----------|--------|---------|
| GET request | `requests.get(url)` | `requests.get("http://api.com")` |
| POST request | `requests.post(url, data)` | `requests.post(url, json=data)` |
| Headers | `headers=dict` | `headers={"Authorization": "token"}` |
| Query params | `params=dict` | `params={"page": 1}` |
| Request timeout | `timeout=10` | `requests.get(url, timeout=10)` |
| Get response | `response.status_code` | Check HTTP status |
| Response JSON | `response.json()` | Parse JSON response |
| Response text | `response.text` | Get raw text |
| Raise error | `raise_for_status()` | Raise on bad status |

## Logging

| Operation | Syntax | Example |
|-----------|--------|---------|
| Setup | `logging.basicConfig()` | Configure root logger |
| Get logger | `logging.getLogger(name)` | Create named logger |
| Log debug | `logger.debug(msg)` | Detailed info |
| Log info | `logger.info(msg)` | General info |
| Log warning | `logger.warning(msg)` | Warning message |
| Log error | `logger.error(msg)` | Error message |
| Log critical | `logger.critical(msg)` | Critical error |
| Set level | `logger.setLevel(level)` | Set minimum level |
| File handler | `FileHandler(file)` | Log to file |
| Console handler | `StreamHandler()` | Log to console |
| Formatter | `Formatter(format)` | Format log messages |

## Environment Variables

| Operation | Syntax | Example |
|-----------|--------|---------|
| Get variable | `os.getenv(key)` | `os.getenv("PATH")` |
| Get with default | `os.getenv(key, default)` | `os.getenv("DEBUG", "false")` |
| Set variable | `os.environ[key] = value` | `os.environ["MY_VAR"] = "test"` |
| Check exists | `key in os.environ` | `if "PATH" in os.environ:` |
| Get all | `os.environ` | Dictionary of all variables |

## Classes and OOP

| Concept | Syntax | Example |
|---------|--------|---------|
| Class def | `class Name:` | `class Server:` |
| Constructor | `def __init__(self):` | Initialize instance |
| Method | `def method(self):` | Instance method |
| Class method | `@classmethod` | Operates on class |
| Static method | `@staticmethod` | No self parameter |
| Property | `@property` | Getter for attribute |
| Inheritance | `class Child(Parent):` | Inherit from parent |
| Docstring | `"""Description"""` | Document class/method |
| Instance var | `self.var = value` | Create instance variable |
| Class var | `var = value` | (at class level) Share across instances |

## File Operations

| Operation | Syntax | Example |
|-----------|--------|---------|
| Exists | `Path(file).exists()` | Check if file exists |
| Is file | `Path(file).is_file()` | Check if regular file |
| Is dir | `Path(file).is_dir()` | Check if directory |
| Read text | `Path(file).read_text()` | Read entire file |
| Write text | `Path(file).write_text(text)` | Write to file |
| Glob | `Path(dir).glob("*.py")` | Find matching files |
| Mkdir | `Path(dir).mkdir()` | Create directory |
| Parent | `Path(file).parent` | Get parent directory |
| Name | `Path(file).name` | Get filename |

## Error Handling Patterns

### Try/Except/Finally
```python
try:
    # Code that might fail
    result = operation()
except SpecificError as e:
    # Handle specific error
    logger.error(f"Failed: {e}")
except Exception as e:
    # Catch all other errors
    logger.error(f"Unexpected error: {e}")
else:
    # Runs if no exception
    logger.info("Success")
finally:
    # Always runs (cleanup)
    cleanup()
```

### Context Managers
```python
with open(file) as f:
    data = f.read()
# File auto-closes

with requests.Session() as session:
    response = session.get(url)
# Session auto-closes
```

## Common Patterns

### Safe Configuration Loading
```python
def load_config(file):
    try:
        with open(file) as f:
            return json.load(f)
    except (FileNotFoundError, json.JSONDecodeError) as e:
        logger.error(f"Config error: {e}")
        return {}
```

### Command Execution with Error Handling
```python
try:
    result = subprocess.run(
        cmd,
        capture_output=True,
        text=True,
        timeout=30,
        check=True
    )
except subprocess.CalledProcessError as e:
    logger.error(f"Command failed: {e.stderr}")
except subprocess.TimeoutExpired:
    logger.error(f"Command timed out: {cmd}")
```

### Logging with Context
```python
logger.info(f"Processing {item_id}")
try:
    process_item(item_id)
    logger.info(f"✓ Completed {item_id}")
except Exception as e:
    logger.error(f"✗ Failed {item_id}: {e}")
```

### Retry Logic
```python
def retry(func, max_attempts=3, delay=1):
    for attempt in range(max_attempts):
        try:
            return func()
        except Exception as e:
            if attempt == max_attempts - 1:
                raise
            logger.warning(f"Attempt {attempt+1} failed, retrying...")
            time.sleep(delay)
```

## Performance Tips

| Tip | Benefit |
|-----|---------|
| Use `subprocess.run()` not `os.system()` | Better control and output capture |
| Pre-compile regex for reuse | Faster repeated searches |
| Use context managers (`with`) | Auto cleanup of resources |
| Use logging not print | Structured output, levels |
| Cache API responses | Reduce API calls |
| Use generator for large files | Memory efficiency |
| Set timeouts on network calls | Prevent hangs |

## Debugging

| Tool | Usage |
|------|-------|
| `import pdb; pdb.set_trace()` | Interactive debugger |
| `import ipdb; ipdb.set_trace()` | Enhanced debugger |
| `logging.DEBUG` | Detailed debug output |
| `print()` with context | Quick inspection |
| `traceback.print_exc()` | Print full stack trace |
| `-m pdb script.py` | Run script with debugger |
