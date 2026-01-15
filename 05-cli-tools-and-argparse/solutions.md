# CLI Tools and Argparse: Solutions

## Solution 1: Simple Argument Parser

```python
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="Server info tool")
parser.add_argument("hostname", help="Server hostname or IP")
parser.add_argument("--port", type=int, default=22, help="SSH port (default: 22)")

args = parser.parse_args()

print(f"Server: {args.hostname}")
print(f"Port: {args.port}")
print(f"Connection: ssh {args.hostname} -p {args.port}")

# Usage: python3 solution1.py web01 --port 2222
# Output:
# Server: web01
# Port: 2222
# Connection: ssh web01 -p 2222
```

**Explanation**: Positional argument `hostname` is required. Optional `--port` uses default 22. `type=int` converts string to integer.

---

## Solution 2: Multiple Arguments

```python
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="User info tool")
parser.add_argument("--name", help="User name")
parser.add_argument("--age", type=int, help="User age")
parser.add_argument("--role", help="User role")

args = parser.parse_args()

print("=== User Information ===")
if args.name:
    print(f"Name: {args.name}")
if args.age:
    print(f"Age: {args.age}")
if args.role:
    print(f"Role: {args.role}")

# Usage: python3 solution2.py --name Alice --age 30 --role Engineer
# Output:
# === User Information ===
# Name: Alice
# Age: 30
# Role: Engineer
```

**Explanation**: Multiple optional arguments. Only prints values that were provided. No defaults means None if not supplied.

---

## Solution 3: Type Conversion

```python
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="Calculate metrics")
parser.add_argument("--count", type=int, required=True, help="Number of items")
parser.add_argument("--timeout", type=float, default=30.5, help="Timeout in seconds")

args = parser.parse_args()

total_time = args.count * args.timeout
print(f"Count: {args.count}")
print(f"Timeout per item: {args.timeout}s")
print(f"Total time: {total_time}s")

# Usage: python3 solution3.py --count 5 --timeout 10.2
# Output:
# Count: 5
# Timeout per item: 10.2s
# Total time: 51.0s
```

**Explanation**: `type=int` and `type=float` automatically convert arguments. Invalid input shows error automatically.

---

## Solution 4: Argument Choices

```python
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="Deploy tool")
parser.add_argument("--environment", 
                   choices=["dev", "staging", "prod"],
                   required=True,
                   help="Deployment environment")

args = parser.parse_args()

config = {
    "dev": {"replicas": 1, "cpu": "100m"},
    "staging": {"replicas": 2, "cpu": "200m"},
    "prod": {"replicas": 5, "cpu": "1000m"}
}

env_config = config[args.environment]
print(f"Deploying to: {args.environment}")
print(f"Replicas: {env_config['replicas']}")
print(f"CPU: {env_config['cpu']}")

# Usage: python3 solution4.py --environment prod
# Output:
# Deploying to: prod
# Replicas: 5
# CPU: 1000m
```

**Explanation**: `choices` restricts valid values. Invalid choice shows error with valid options. Useful for enum-like arguments.

---

## Solution 5: Boolean Flags

```python
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="Advanced tool")
parser.add_argument("--verbose", action="store_true", help="Verbose output")
parser.add_argument("--debug", action="store_true", help="Debug mode")
parser.add_argument("--dry-run", action="store_true", help="Simulate without changes")

args = parser.parse_args()

if args.verbose:
    print("Verbose mode ON")
if args.debug:
    print("Debug mode ON")
if args.dry_run:
    print("DRY RUN: No actual changes will be made")

print("Tool execution complete")

# Usage: python3 solution5.py --verbose --dry-run
# Output:
# Verbose mode ON
# DRY RUN: No actual changes will be made
# Tool execution complete
```

**Explanation**: `action="store_true"` creates boolean flags. Defaults to False. `--dry-run` becomes `args.dry_run` (hyphen → underscore).

---

## Solution 6: Required Arguments with Validation

```python
#!/usr/bin/env python3
import argparse
import sys

parser = argparse.ArgumentParser(description="User registration")
parser.add_argument("--username", required=True, help="Username (required)")
parser.add_argument("--password", required=True, help="Password (min 8 chars)")

args = parser.parse_args()

# Validation
if len(args.password) < 8:
    print("Error: Password must be at least 8 characters")
    sys.exit(1)

print(f"✓ Username: {args.username}")
print(f"✓ Password: {'*' * len(args.password)}")
print("✓ Registration successful")

# Usage: python3 solution6.py --username alice --password mypassword123
# Output:
# ✓ Username: alice
# ✓ Password: **********************
# ✓ Registration successful
```

**Explanation**: `required=True` enforces argument. Custom validation checks password length. `sys.exit(1)` indicates error.

---

## Solution 7: Subcommands

```python
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="Server management tool")
subparsers = parser.add_subparsers(dest="command", help="Commands")

# List command
list_parser = subparsers.add_parser("list", help="List all servers")
list_parser.add_argument("--type", choices=["web", "db", "all"], default="all")

# Add command
add_parser = subparsers.add_parser("add", help="Add new server")
add_parser.add_argument("hostname", help="Server hostname")
add_parser.add_argument("--ip", required=True, help="Server IP")

# Delete command
delete_parser = subparsers.add_parser("delete", help="Delete server")
delete_parser.add_argument("hostname", help="Server hostname")

args = parser.parse_args()

if args.command == "list":
    print(f"Listing {args.type} servers")
elif args.command == "add":
    print(f"Adding: {args.hostname} ({args.ip})")
elif args.command == "delete":
    print(f"Deleting: {args.hostname}")
else:
    parser.print_help()

# Usage: python3 solution7.py list --type web
# Output: Listing web servers
```

**Explanation**: `subparsers` creates multiple commands. Each subcommand has own arguments. Useful for tools like `git`, `docker`.

---

## Solution 8: File Arguments

```python
#!/usr/bin/env python3
import argparse
import os
import sys

parser = argparse.ArgumentParser(description="File processor")
parser.add_argument("--input-file", required=True, help="Input file path")
parser.add_argument("--output-file", required=True, help="Output file path")

args = parser.parse_args()

# Validate input file
if not os.path.isfile(args.input_file):
    print(f"Error: Input file not found: {args.input_file}")
    sys.exit(1)

# Check if output directory exists
output_dir = os.path.dirname(args.output_file)
if output_dir and not os.path.isdir(output_dir):
    print(f"Error: Output directory not found: {output_dir}")
    sys.exit(1)

print(f"✓ Input: {args.input_file}")
print(f"✓ Output: {args.output_file}")
print("✓ Files validated")

# Usage: python3 solution8.py --input-file data.txt --output-file result.txt
# Output: (if files valid) ✓ Input: data.txt, etc.
```

**Explanation**: Validates file existence before processing. `os.path.isfile()` checks readable file. `os.path.dirname()` extracts directory path.

---

## Solution 9: Mutually Exclusive Arguments

```python
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="Data export tool")

# Create mutually exclusive group
output_group = parser.add_mutually_exclusive_group(required=True)
output_group.add_argument("--json", action="store_true", help="Export as JSON")
output_group.add_argument("--csv", action="store_true", help="Export as CSV")

parser.add_argument("--data-file", required=True, help="Data file to export")

args = parser.parse_args()

print(f"Exporting: {args.data_file}")
if args.json:
    print("Format: JSON")
elif args.csv:
    print("Format: CSV")

# Usage: python3 solution9.py --json --data-file data.txt
# Error: argument --json: not allowed with argument --csv
```

**Explanation**: `add_mutually_exclusive_group()` prevents conflicting options. Exactly one must be specified. Useful for format selection.

---

## Solution 10: Help and Version

```python
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(
    description="Complete DevOps CLI tool",
    epilog="Examples: tool.py list --type web"
)

# Version
parser.add_argument("--version", 
                   action="version", 
                   version="%(prog)s 1.2.0")

# Main arguments
parser.add_argument("command", 
                   choices=["list", "add", "delete"],
                   help="Command to execute")
parser.add_argument("--verbose", "-v", 
                   action="store_true", 
                   help="Verbose output")
parser.add_argument("--config", "-c", 
                   help="Config file path")

args = parser.parse_args()

print(f"Executing: {args.command}")
if args.verbose:
    print("Verbose: ON")
if args.config:
    print(f"Config: {args.config}")

# Usage: python3 solution10.py --version
# Output: tool.py 1.2.0
#
# Usage: python3 solution10.py --help
# Shows: usage, description, all arguments, epilog
```

**Explanation**: `action="version"` shows version and exits. Short flags `-v`, `-c` alongside long ones. `epilog` shows examples. Help auto-generated.
