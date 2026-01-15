# CLI Tools and Argparse: Exercises

## Exercise 1 (Easy): Simple Argument Parser
Create a script that takes a hostname as argument and prints server details. Use `--port` as optional argument with default 22.

**Hint**: Use `parser.add_argument()` for positional, optional with `--port`

---

## Exercise 2 (Easy): Multiple Arguments
Build a script that takes `--name`, `--age`, `--role` and prints formatted output.

**Hint**: All arguments with `--` are optional

---

## Exercise 3 (Easy): Type Conversion
Create a script that takes `--count` (integer) and `--timeout` (float) arguments and performs calculations.

**Hint**: Use `type=int`, `type=float` in add_argument

---

## Exercise 4 (Easy): Argument Choices
Write a script that takes `--environment` with choices (dev, staging, prod) and displays selected environment.

**Hint**: Use `choices=["dev", "staging", "prod"]`

---

## Exercise 5 (Medium): Boolean Flags
Create a script with multiple boolean flags: `--verbose`, `--debug`, `--dry-run` using `action="store_true"`.

**Hint**: `action="store_true"` makes flags that default to False

---

## Exercise 6 (Medium): Required Arguments
Build a tool that requires `--username` and `--password` arguments with validation that passwords are at least 8 chars.

**Hint**: Use `required=True` and check argument values after parsing

---

## Exercise 7 (Medium): Subcommands
Create a tool with subcommands: `list` (shows servers), `add` (adds server), `delete` (removes server).

**Hint**: Use `subparsers = parser.add_subparsers(dest="command")`

---

## Exercise 8 (Medium): File Arguments
Write a script that takes `--input-file` and `--output-file` arguments with validation that files exist/are writable.

**Hint**: Use `type=argparse.FileType()` or manual validation

---

## Exercise 9 (Medium): Mutually Exclusive Arguments
Create a tool with mutually exclusive options like `--json` and `--csv` (can't use both).

**Hint**: Use `group = parser.add_mutually_exclusive_group()`

---

## Exercise 10 (Medium): Help and Version
Build a complete CLI tool with `--version` flag showing version, and detailed help messages for all arguments.

**Hint**: Use `add_argument("--version", action="version", version="1.0.0")`
