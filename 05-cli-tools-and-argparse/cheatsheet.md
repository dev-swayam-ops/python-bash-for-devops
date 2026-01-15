# CLI Tools and Argparse: Cheatsheet

## Basic Setup

| Task | Syntax | Example |
|------|--------|---------|
| Create parser | `parser = argparse.ArgumentParser()` | Initialize parser |
| Add help | `description="Help text"` | `ArgumentParser(description="...")` |
| Parse args | `args = parser.parse_args()` | Get parsed arguments |
| Print help | `parser.print_help()` | Show help message |

## Positional Arguments

| Task | Syntax | Example |
|------|--------|---------|
| Add argument | `parser.add_argument("name")` | Required positional |
| Help text | `help="description"` | `add_argument("file", help="...")` |
| Multiple | `nargs=N` | `nargs=3` accepts exactly 3 |
| Variable count | `nargs="*"` | Zero or more arguments |
| At least one | `nargs="+"` | One or more arguments |
| Optional positional | `nargs="?"` | Zero or one argument |

## Optional Arguments

| Task | Syntax | Example |
|------|--------|---------|
| Optional arg | `--option` | `add_argument("--name")` |
| Short form | `-o` | `add_argument("-n", "--name")` |
| Default value | `default=value` | `default=22` |
| Required option | `required=True` | Makes `--option` mandatory |
| Help text | `help="text"` | Always include for clarity |

## Type Conversion

| Type | Syntax | Example |
|------|--------|---------|
| String (default) | No type needed | `add_argument("name")` |
| Integer | `type=int` | `add_argument("--count", type=int)` |
| Float | `type=float` | `add_argument("--timeout", type=float)` |
| File | `type=argparse.FileType('r')` | Opens file automatically |
| Custom | `type=function` | Process with custom function |

## Boolean Flags

| Task | Syntax | Example |
|------|--------|---------|
| Flag (True) | `action="store_true"` | `add_argument("--verbose", action="store_true")` |
| Flag (False) | `action="store_false"` | Stores False when present |
| Count | `action="count"` | `-vvv` counts occurrences |
| Append | `action="append"` | Adds to list each occurrence |

## Choices and Validation

| Task | Syntax | Example |
|------|--------|---------|
| Restrict values | `choices=[...]` | `choices=["dev", "prod"]` |
| Enum-like | `choices=list` | Prevents invalid input |
| Custom validation | Check after parse | `if args.value < 0: error()` |

## Subcommands

| Task | Syntax | Example |
|------|--------|---------|
| Create subparsers | `subparsers = parser.add_subparsers(dest="command")` | Multiple commands |
| Add subcommand | `sub = subparsers.add_parser("name")` | Create subcommand |
| Subcommand help | `help="text"` | Description for subcommand |
| Access command | `args.command` | Check which subcommand used |

## Advanced Features

| Task | Syntax | Example |
|------|--------|---------|
| Version | `action="version", version="1.0"` | `--version` flag |
| Mutually exclusive | `group = parser.add_mutually_exclusive_group()` | Only one allowed |
| Argument groups | `group = parser.add_argument_group("title")` | Organize help output |
| Custom formatter | `formatter_class=...` | Change help formatting |

## Common Patterns

### Basic Script
```python
import argparse

parser = argparse.ArgumentParser(description="Tool description")
parser.add_argument("file", help="File to process")
parser.add_argument("--output", help="Output file")
args = parser.parse_args()

print(f"Processing {args.file}")
```

### With Subcommands
```python
parser = argparse.ArgumentParser()
subparsers = parser.add_subparsers(dest="cmd")

start = subparsers.add_parser("start")
start.add_argument("service")

stop = subparsers.add_parser("stop")
stop.add_argument("service")

args = parser.parse_args()
if args.cmd == "start":
    print(f"Starting {args.service}")
```

### With Validation
```python
args = parser.parse_args()

if args.port < 1 or args.port > 65535:
    parser.error("Port must be 1-65535")
```

### With Defaults
```python
parser.add_argument("--threads", 
                   type=int, 
                   default=4,
                   help="Number of threads (default: 4)")
```

## Access Parsed Arguments

| Task | Syntax | Example |
|------|--------|---------|
| Get positional | `args.name` | `args.file` |
| Get optional | `args.name` | `args.output` |
| Check if provided | `args.name is not None` | If user provided value |
| Get with default | Automatic | Uses default if not provided |
| Convert to dict | `vars(args)` | `vars(args)` → dict |

## Help Message Customization

| Element | Syntax | Example |
|---------|--------|---------|
| Usage line | Auto-generated | Shows command format |
| Description | `description=...` | Main help text |
| Epilog | `epilog=...` | Text at end of help |
| Help text | `help=...` | Per-argument description |
| Suppress help | `help=argparse.SUPPRESS` | Hide from help |

## Error Handling

| Task | Syntax | Example |
|------|--------|---------|
| Print error | `parser.error("message")` | Shows message and exits |
| Exit code | Automatic | 0=success, 2=error |
| Invalid args | Automatic | Shows error and usage |
| Type error | Automatic | Shows when type conversion fails |

## Common Argument Names

| Use Case | Short | Long |
|----------|-------|------|
| Verbose output | `-v` | `--verbose` |
| Debug mode | `-d` | `--debug` |
| Output file | `-o` | `--output` |
| Input file | `-i` | `--input` |
| Configuration | `-c` | `--config` |
| Version | None | `--version` |
| Help | `-h` | `--help` |
| Dry run | None | `--dry-run` |
| Quiet | `-q` | `--quiet` |
