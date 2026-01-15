# Bash Basics: Cheatsheet

## Script Structure

| Component | Syntax | Purpose |
|-----------|--------|---------|
| Shebang | `#!/bin/bash` | Specifies bash interpreter (must be first line) |
| Comment | `# comment text` | Add explanatory notes |
| Execute | `chmod +x script.sh` | Make script runnable |
| Run | `./script.sh` | Execute script from current directory |

## Variables

| Operation | Syntax | Example |
|-----------|--------|---------|
| Define | `VAR=value` | `NAME="John"` |
| Access | `$VAR` | `echo $NAME` |
| In quotes | `"$VAR"` | `echo "Hello, $NAME"` |
| No expansion | `'$VAR'` | `echo '$VAR'` outputs `$VAR` |
| Built-in | `$0, $1, $2, $#` | Script name, args, arg count |
| Command output | `$(command)` | `DATE=$(date)` |

## Conditionals

| Operator | Meaning | Example |
|----------|---------|---------|
| `-f file` | File exists | `if [ -f "file.txt" ]` |
| `-d dir` | Directory exists | `if [ -d "folder" ]` |
| `-z "$VAR"` | Variable is empty | `if [ -z "$VAR" ]` |
| `-n "$VAR"` | Variable is not empty | `if [ -n "$VAR" ]` |
| `-eq` | Equal (numbers) | `if [ $num -eq 5 ]` |
| `-ne` | Not equal | `if [ $num -ne 5 ]` |
| `-lt` | Less than | `if [ $num -lt 10 ]` |
| `-gt` | Greater than | `if [ $num -gt 10 ]` |
| `-le` | Less or equal | `if [ $num -le 10 ]` |
| `-ge` | Greater or equal | `if [ $num -ge 10 ]` |

## Conditionals Structure

```bash
if [ condition ]; then
    echo "True"
elif [ condition ]; then
    echo "Elif"
else
    echo "False"
fi
```

## Loops

| Type | Syntax | Use Case |
|------|--------|----------|
| For loop | `for var in list; do command; done` | Iterate over list items |
| While loop | `while [ condition ]; do command; done` | Repeat while condition true |
| Until loop | `until [ condition ]; do command; done` | Repeat until condition true |
| Range | `for i in {1..5}; do echo $i; done` | Count from 1 to 5 |

## Arrays

| Operation | Syntax | Example |
|-----------|--------|---------|
| Define | `ARRAY=(val1 val2 val3)` | `TOOLS=("Git" "Docker")` |
| Access | `${ARRAY[0]}` | `${TOOLS[0]}` → Git |
| All elements | `${ARRAY[@]}` | `for t in "${ARRAY[@]}"` |
| Length | `${#ARRAY[@]}` | `echo ${#TOOLS[@]}` → 2 |
| Append | `ARRAY+=(new_val)` | `TOOLS+=("Kubernetes")` |

## Functions

| Element | Syntax | Purpose |
|---------|--------|---------|
| Definition | `func_name() { commands; }` | Define a function |
| Parameters | `$1, $2, $3` | Access function arguments |
| Return | `return 0` | Exit code (0=success) |
| Output | `echo "result"` | Return value to caller |
| Call | `func_name arg1 arg2` | Execute function |

## Input/Output

| Command | Purpose | Example |
|---------|---------|---------|
| `read var` | Get user input | `read -p "Enter name: " name` |
| `echo` | Print text | `echo "Hello"` |
| `printf` | Formatted output | `printf "%d\n" 42` |
| `>` | Redirect to file | `echo "text" > file.txt` |
| `>>` | Append to file | `echo "text" >> file.txt` |
| `<` | Read from file | `cat < file.txt` |
| `\|` | Pipe | `cat file.txt \| grep "pattern"` |
| `2>` | Redirect errors | `command 2> errors.log` |
| `&>` | Redirect all output | `command &> output.log` |

## String Operations

| Operation | Syntax | Example |
|-----------|--------|---------|
| Length | `${#string}` | `${#NAME}` |
| Substring | `${string:start:length}` | `${NAME:0:3}` → first 3 chars |
| Replace | `${string/old/new}` | `${NAME/John/Jane}` |
| Remove suffix | `${string%suffix}` | `${FILE%.txt}` |
| Remove prefix | `${string#prefix}` | `${PATH#/usr/}` |
| Convert to uppercase | No native way | Use `tr '[:lower:]' '[:upper:]'` |

## Common Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `pwd` | Print working directory | `pwd` |
| `cd` | Change directory | `cd /home/user` |
| `ls` | List files | `ls -la` |
| `mkdir` | Make directory | `mkdir -p dir/subdir` |
| `touch` | Create file | `touch file.txt` |
| `cat` | Display file | `cat file.txt` |
| `grep` | Search pattern | `grep "error" log.txt` |
| `sed` | Stream editor | `sed 's/old/new/g' file.txt` |
| `awk` | Text processor | `awk '{print $1}' file.txt` |
| `wc` | Count lines/words | `wc -l file.txt` |

## Debugging

| Method | Usage | Purpose |
|--------|-------|---------|
| `set -x` | Add at start of script | Print each command before execution |
| `set -e` | Add at start of script | Exit on first error |
| `bash -x script.sh` | Command line | Run script in debug mode |
| `echo $var` | In script | Print variable value |
| `echo "Checkpoint 1"` | In script | Mark execution point |

## Special Variables

| Variable | Meaning | Example |
|----------|---------|---------|
| `$0` | Script name | `echo $0` → script.sh |
| `$1, $2...` | Arguments | `script.sh arg1 arg2` |
| `$#` | Argument count | `echo $#` → 2 |
| `$@` | All arguments | `for arg in "$@"` |
| `$?` | Exit status of last command | `echo $?` → 0 (success) |
| `$$` | Current process ID | `echo $$` |
| `$!` | Last background process ID | `command & echo $!` |

## Common Patterns

### Check if script arguments provided
```bash
if [ $# -lt 2 ]; then
    echo "Usage: $0 arg1 arg2"
    exit 1
fi
```

### Check if running as root
```bash
if [ "$EUID" -ne 0 ]; then
    echo "Must run as root"
    exit 1
fi
```

### Process all files in directory
```bash
for file in *.txt; do
    [ -f "$file" ] && echo "$file"
done
```

### Error handling
```bash
command || { echo "Command failed"; exit 1; }
```
