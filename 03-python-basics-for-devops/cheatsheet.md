# Python Basics for DevOps: Cheatsheet

## Data Types

| Type | Example | Purpose |
|------|---------|---------|
| `str` | `"hello"` | Text data |
| `int` | `42` | Whole numbers |
| `float` | `3.14` | Decimal numbers |
| `bool` | `True` | True/False values |
| `list` | `[1, 2, 3]` | Ordered, mutable sequence |
| `tuple` | `(1, 2, 3)` | Ordered, immutable sequence |
| `dict` | `{"key": "value"}` | Key-value pairs |
| `set` | `{1, 2, 3}` | Unique, unordered items |
| `None` | `None` | Null/empty value |

## Variables and Assignment

| Operation | Syntax | Example |
|-----------|--------|---------|
| Assignment | `var = value` | `name = "Alice"` |
| Multiple | `a, b = 1, 2` | `x, y, z = 10, 20, 30` |
| Swap | `a, b = b, a` | `x, y = y, x` |
| Unpack | `first, *rest = list` | `first, *others = [1, 2, 3, 4]` |
| Type check | `type(var)` | `type(42)` → `<class 'int'>` |
| Convert | `str()`, `int()`, `float()` | `int("42")` → 42 |

## String Operations

| Operation | Syntax | Example |
|-----------|--------|---------|
| Concatenate | `str1 + str2` | `"hello" + " world"` |
| Format | `f"text {var}"` | `f"Hello {name}"` |
| Length | `len(string)` | `len("hello")` → 5 |
| Case | `upper()`, `lower()` | `"hello".upper()` → "HELLO" |
| Replace | `replace(old, new)` | `"abc".replace("a", "x")` → "xbc" |
| Split | `split(separator)` | `"a,b,c".split(",")` → ["a", "b", "c"] |
| Join | `separator.join(list)` | `",".join(["a", "b"])` → "a,b" |
| Strip | `strip()` | `" hello ".strip()` → "hello" |
| Check | `in`, `startswith()`, `endswith()` | `"lo" in "hello"` → True |

## Lists

| Operation | Syntax | Example |
|-----------|--------|---------|
| Create | `[item1, item2]` | `[1, 2, 3]` |
| Index | `list[index]` | `list[0]` → first item |
| Slice | `list[start:end]` | `list[1:3]` → items 1-2 |
| Append | `list.append(item)` | `list.append(4)` |
| Extend | `list.extend(other)` | `list.extend([4, 5])` |
| Insert | `list.insert(index, item)` | `list.insert(1, "x")` |
| Remove | `list.remove(item)` | `list.remove(1)` |
| Pop | `list.pop(index)` | `list.pop(0)` → removes first |
| Length | `len(list)` | `len([1, 2, 3])` → 3 |
| Sort | `sort()` | `list.sort()` |
| Reverse | `reverse()` | `list.reverse()` |

## Dictionaries

| Operation | Syntax | Example |
|-----------|--------|---------|
| Create | `{"key": value}` | `{"name": "Alice"}` |
| Access | `dict[key]` | `dict["name"]` |
| Get safe | `dict.get(key, default)` | `dict.get("age", 0)` |
| Add | `dict[key] = value` | `dict["age"] = 30` |
| Update | `dict.update(other)` | `dict.update({"x": 1})` |
| Delete | `del dict[key]` | `del dict["name"]` |
| Keys | `dict.keys()` | `dict.keys()` → dict_keys object |
| Values | `dict.values()` | `dict.values()` → dict_values object |
| Items | `dict.items()` | `dict.items()` → key-value pairs |
| Check | `"key" in dict` | `"name" in dict` → True/False |

## Control Flow

| Structure | Syntax | Example |
|-----------|--------|---------|
| If | `if condition:` | `if x > 0:` |
| Elif | `elif condition:` | `elif x == 0:` |
| Else | `else:` | `else:` |
| Ternary | `value if condition else other` | `"yes" if x > 0 else "no"` |
| For | `for item in iterable:` | `for x in [1, 2, 3]:` |
| While | `while condition:` | `while x < 10:` |
| Break | `break` | Exit loop |
| Continue | `continue` | Skip to next iteration |
| Range | `range(start, end, step)` | `range(1, 10, 2)` → 1,3,5,7,9 |

## Functions

| Element | Syntax | Example |
|---------|--------|---------|
| Define | `def func_name(params):` | `def greet(name):` |
| Return | `return value` | `return True` |
| Default param | `def func(x=default):` | `def power(x=2):` |
| Variable args | `def func(*args):` | `def sum_all(*nums):` |
| Keyword args | `def func(**kwargs):` | `def config(**opts):` |
| Call | `func(args)` | `greet("Alice")` |
| Docstring | `"""Description"""` | Inside function |

## Error Handling

| Structure | Syntax | Purpose |
|-----------|--------|---------|
| Try | `try:` | Code that might error |
| Except | `except ErrorType:` | Handle specific error |
| Except all | `except:` | Catch any error |
| Else | `else:` | Run if no error |
| Finally | `finally:` | Always execute |
| Raise | `raise ErrorType()` | Throw error |

## File Operations

| Operation | Syntax | Example |
|-----------|--------|---------|
| Open read | `open(file, "r")` | `f = open("data.txt", "r")` |
| Open write | `open(file, "w")` | `f = open("log.txt", "w")` |
| Open append | `open(file, "a")` | `f = open("log.txt", "a")` |
| With context | `with open() as f:` | Auto-closes file |
| Read all | `f.read()` | Returns entire content |
| Read line | `f.readline()` | Returns one line |
| Read lines | `f.readlines()` | Returns list of lines |
| Write | `f.write(text)` | Write string |
| Write lines | `f.writelines(list)` | Write list of lines |
| Close | `f.close()` | Release file |

## Modules

| Module | Purpose | Example |
|--------|---------|---------|
| `os` | Operating system operations | `os.getcwd()`, `os.listdir()` |
| `sys` | System operations | `sys.argv`, `sys.exit()` |
| `json` | JSON data | `json.dump()`, `json.load()` |
| `datetime` | Date and time | `datetime.datetime.now()` |
| `subprocess` | Run commands | `subprocess.run()` |
| `pathlib` | File paths | `Path("file.txt")` |
| `re` | Regular expressions | `re.search()`, `re.findall()` |
| `csv` | CSV files | `csv.reader()`, `csv.writer()` |

## Common Patterns

### Check and Get Dictionary Value
```python
if "key" in dict:
    value = dict["key"]
else:
    value = "default"

# Or shorter:
value = dict.get("key", "default")
```

### Loop with Index
```python
for i, item in enumerate(list):
    print(f"{i}: {item}")
```

### Safe Type Conversion
```python
try:
    num = int(input_string)
except ValueError:
    num = 0
```

### List from Command Output
```python
import subprocess
result = subprocess.run(["ls"], capture_output=True, text=True)
lines = result.stdout.split("\n")
```

### Read All Lines from File
```python
with open("file.txt") as f:
    for line in f:
        print(line.strip())
```

## Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `+` | Addition | `10 + 5` → 15 |
| `-` | Subtraction | `10 - 5` → 5 |
| `*` | Multiplication | `10 * 5` → 50 |
| `/` | Division | `10 / 5` → 2.0 |
| `//` | Floor division | `10 // 3` → 3 |
| `%` | Modulo (remainder) | `10 % 3` → 1 |
| `**` | Exponent | `2 ** 3` → 8 |
| `==` | Equal | `5 == 5` → True |
| `!=` | Not equal | `5 != 3` → True |
| `<` | Less than | `5 < 10` → True |
| `>` | Greater than | `10 > 5` → True |
| `<=` | Less or equal | `5 <= 5` → True |
| `>=` | Greater or equal | `5 >= 5` → True |
| `and` | Logical AND | `x > 0 and x < 10` |
| `or` | Logical OR | `x < 0 or x > 100` |
| `not` | Logical NOT | `not x == 0` |
| `in` | Membership | `1 in [1, 2, 3]` → True |
