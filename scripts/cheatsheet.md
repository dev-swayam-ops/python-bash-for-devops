# Scripts - Cheatsheet

## Script Structure Basics

| Element | Purpose | Example |
|---------|---------|---------|
| Shebang | Specify interpreter | `#!/bin/bash` |
| Source file | Import functions | `source ./utils.sh` or `. ./utils.sh` |
| Function definition | Reusable code block | `function_name() { commands; }` |
| Return code | Exit status | `return 0` (success) or `return 1` (failure) |
| Error check | Test command result | `command || { echo "Failed"; exit 1; }` |
| Arg access | Command arguments | `$1, $2, ... $@, $#` |

## Function Definition and Usage

| Pattern | Purpose | Example |
|---------|---------|---------|
| Define function | Create reusable block | `log_info() { echo "INFO: $1"; }` |
| Call function | Execute function | `log_info "Starting"` |
| Return value | Exit function | `return 0` for success |
| Function args | Pass parameters | `function_name arg1 arg2` |
| Local variables | Function scope | `local var=$1` |
| Global access | Set globally | `GLOBAL_VAR=$1` (no local) |

## Logging Patterns

| Command | Purpose | Example |
|---------|---------|---------|
| `echo "$string"` | Print to stdout | Regular output |
| `echo "$string" >&2` | Print to stderr | Error output |
| `date '+%Y-%m-%d %H:%M:%S'` | Timestamp | `[$(date +'%H:%M:%S')] Message` |
| `echo -e "\033[0;31m"` | Color codes | RED='\033[0;31m' |
| `tee` | Write to file and stdout | `command \| tee output.log` |
| `logger` | System log | `logger "Message"` |

## Error Handling

| Pattern | Purpose | Example |
|---------|---------|---------|
| Exit on error | Stop script | `set -e` or check each command |
| Trap errors | Cleanup on exit | `trap cleanup EXIT` |
| Check exit code | Test success | `cmd && echo ok \|\| echo failed` |
| Error context | Show where failed | `echo "Failed at line $LINENO"` |
| Return code | Function status | `return 1` indicates failure |
| Validation | Check inputs | `[ -z "$var" ] && exit 1` |

## Variable Handling

| Syntax | Purpose | Example |
|--------|---------|---------|
| `$1, $2, ...` | Positional args | `$1` is first argument |
| `$@` | All arguments | `"$@"` preserves spacing |
| `$#` | Argument count | Validate `[ $# -lt 2 ]` |
| `${var:-default}` | Default value | `${1:-"default"}` |
| `${var:?error}` | Required variable | `${DBPASS:?DB password required}` |
| `${var%pattern}` | Remove suffix | `${file%.txt}` |
| `${var#pattern}` | Remove prefix | `${path#*/}` |

## Sourcing and Libraries

| Command | Purpose | Example |
|---------|---------|---------|
| `source file.sh` | Import functions | `source ./utils.sh` |
| `. file.sh` | Alternative source | Same as source |
| Export function | Make available | `export -f function_name` |
| Command -v | Check if exists | `command -v docker` |
| Which | Find command | `which python` |
| Type | Show command type | `type ls` (builtin/alias/file) |

## Configuration File Handling

| Pattern | Purpose | Example |
|---------|---------|---------|
| Read key=value | Parse config | `grep "^KEY=" config.conf` |
| Export variables | Use in script | `export $(cat config.conf)` |
| Skip comments | Ignore lines | `grep -v "^#"` |
| Set defaults | Fallback values | `${ENV_VAR:-/default/path}` |
| Validate config | Check required | `[ -z "$REQUIRED_VAR" ] && exit 1` |

## File Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `[ -f file ]` | File exists | Check before reading |
| `[ -d dir ]` | Directory exists | Check before entering |
| `[ -r file ]` | Readable | Check permissions |
| `[ -w file ]` | Writable | Check permissions |
| `[ -x file ]` | Executable | Check if runnable |
| `[ -s file ]` | Non-empty | Check file has content |
| `[ -L file ]` | Symlink | Check if symbolic link |

## Text Processing in Scripts

| Command | Purpose | Example |
|---------|---------|---------|
| `grep "pattern"` | Find lines | `grep ERROR logfile` |
| `awk '{print $2}'` | Extract fields | Split by whitespace |
| `sed 's/old/new/'` | Replace text | Stream editor |
| `cut -d: -f3` | Extract column | By delimiter |
| `tr 'a-z' 'A-Z'` | Translate chars | Convert case |
| `sort` | Sort lines | `sort -n` for numeric |
| `uniq` | Remove duplicates | `sort \| uniq` |
| `wc -l` | Count lines | Line count |

## Process Management in Scripts

| Command | Purpose | Example |
|---------|---------|---------|
| `&` | Background process | `long_command &` |
| `$!` | Last PID | Save for later: `PID=$!` |
| `wait $PID` | Wait for process | Block until done |
| `kill -TERM $PID` | Graceful stop | SIGTERM signal |
| `kill -KILL $PID` | Force stop | SIGKILL (last resort) |
| `pgrep pattern` | Find process | Search by name pattern |
| `pkill pattern` | Kill by pattern | Kill matching processes |

## Common Script Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Check required args | Validate input | `[ $# -lt 2 ] && { usage; exit 1; }` |
| Conditional execution | Run if success | `cmd && next_cmd \|\| echo "Failed"` |
| Loop with retry | Repeat until success | `for i in {1..5}; do cmd && break; done` |
| Safe rm with confirm | Prevent accidents | `read -p "Delete?" && rm file` |
| Temp file cleanup | Auto cleanup | `trap "rm -f $tmpfile" EXIT` |
| PID file check | Prevent duplicates | `[ -f .pid ] && exit 1` |

## Testing Script Locally

| Command | Purpose | Example |
|---------|---------|---------|
| `bash -n script.sh` | Syntax check | Validate before running |
| `bash -x script.sh` | Debug mode | Show all commands |
| `set -x` | Enable trace | Inside script |
| `set -e` | Exit on error | Stop on first failure |
| `set -u` | Fail on undefined | Error on $undefined |
| `set -o pipefail` | Pipe errors | Catch intermediate errors |
| `DEBUG=1 script.sh` | Debug output | Check debug logging |

## Script Distribution Best Practices

| Practice | Purpose | Example |
|----------|---------|---------|
| Shebang | Cross-platform | `#!/bin/bash` at top |
| Permissions | Make executable | `chmod +x script.sh` |
| Relative paths | Portability | Use `.` not `/absolute` |
| Check dependencies | Early fail | `command -v docker \|\| exit 1` |
| Help function | Documentation | Show usage with `-h` |
| Version flag | Track changes | `--version` shows current |
| Config files | Flexibility | Use separate config.conf |

## Quick Debugging Checklist

```bash
# Enable debugging
set -x  # Show all commands
set -e  # Exit on error
set -u  # Error on undefined variables
set -o pipefail  # Pipe failures bubble up

# Test function exists
type function_name

# Check variable value
echo "VAR=$VAR"

# Test condition
[ -f file ] && echo "exists" || echo "missing"

# Show line number on error
trap 'echo "Error at line $LINENO"' ERR

# List all variables
set | grep VAR=

# Check script syntax
bash -n script.sh
```

## Common Issues and Fixes

| Problem | Cause | Fix |
|---------|-------|-----|
| "command not found" | Function not sourced | `source ./utils.sh` |
| "Permission denied" | Not executable | `chmod +x script.sh` |
| Variables empty | Wrong scope | Use `export VAR=value` |
| Function undefined | Sourced after use | Move `source` to top |
| Arg parsing fails | Quote issues | Use `"$@"` with quotes |
| Script hangs | Infinite loop | Add timeout, check loops |
| Colors not shown | Wrong escape code | Use `echo -e "\033[color"` |
