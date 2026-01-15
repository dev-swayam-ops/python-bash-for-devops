# Bash Advanced Scripting: Cheatsheet

## Error Handling

| Technique | Syntax | Purpose |
|-----------|--------|---------|
| Exit code | `$?` | Get previous command exit status |
| Set mode | `set -e` | Exit on error |
| Set mode | `set -u` | Error on undefined variables |
| Set mode | `set -o pipefail` | Pipeline fails if any command fails |
| Trap errors | `trap 'command' ERR` | Run command on error |
| Check command | `command && echo "ok" \|\| echo "fail"` | Conditional execution |
| Subshell error | `command \|\| return 1` | Return error from subshell |
| Exit code | `exit 0` | Successful exit |
| Exit code | `exit 1` | Error exit |

## Regular Expressions (Regex)

| Pattern | Matches | Example |
|---------|---------|---------|
| `.` | Any single character | `a.c` matches abc, adc |
| `*` | Zero or more | `ab*c` matches ac, abc, abbc |
| `+` | One or more | `ab+c` matches abc, abbc (not ac) |
| `?` | Zero or one | `ab?c` matches ac, abc |
| `^` | Start of line | `^error` matches error at start |
| `$` | End of line | `error$` matches error at end |
| `[]` | Character class | `[0-9]` matches any digit |
| `[^]` | Negated class | `[^0-9]` matches non-digit |
| `()` | Group | `(ab)+` matches ab, abab |
| `\|` | OR | `cat\|dog` matches cat or dog |

## grep - Pattern Matching

| Command | Purpose | Example |
|---------|---------|---------|
| `grep "pattern" file` | Find pattern in file | `grep "error" app.log` |
| `grep -i pattern file` | Case-insensitive search | `grep -i "ERROR" log.txt` |
| `grep -v pattern file` | Invert match (exclude) | `grep -v "debug" log.txt` |
| `grep -c pattern file` | Count matches | `grep -c "error" log.txt` |
| `grep -n pattern file` | Show line numbers | `grep -n "error" log.txt` |
| `grep -E "regex" file` | Extended regex | `grep -E "error\|warning" log.txt` |
| `grep -l pattern file*` | Show filenames | `grep -l "error" *.log` |
| `grep -A 2 pattern file` | Show 2 lines after | `grep -A 2 "error" log.txt` |
| `grep -B 2 pattern file` | Show 2 lines before | `grep -B 2 "error" log.txt` |

## sed - Stream Editor

| Command | Purpose | Example |
|---------|---------|---------|
| `sed 's/old/new/' file` | Replace first occurrence | `sed 's/error/warning/' log.txt` |
| `sed 's/old/new/g' file` | Replace all occurrences | `sed 's/error/warning/g' log.txt` |
| `sed -i 's/old/new/g' file` | Replace in-place | `sed -i 's/127.0.0.1/localhost/g' config` |
| `sed '10d' file` | Delete line 10 | `sed '10d' file.txt` |
| `sed '1,5d' file` | Delete lines 1-5 | `sed '1,5d' file.txt` |
| `sed -n '5p' file` | Print line 5 only | `sed -n '5p' file.txt` |
| `sed 's/^/#/' file` | Add # to start of line | `sed 's/^/#/' config` |
| `sed 's|old\|new|g'` | Use \| as delimiter | `sed 's|/usr/bin|/opt/bin|g'` |

## awk - Text Processing

| Command | Purpose | Example |
|---------|---------|---------|
| `awk '{print}' file` | Print all lines | `awk '{print}' data.txt` |
| `awk '{print $1}' file` | Print first field | `awk '{print $1}' data.txt` |
| `awk '{print $1,$2}' file` | Print first two fields | `awk '{print $1,$2}' data.txt` |
| `awk -F: '{print $1}' file` | Set field separator | `awk -F: '{print $1}' /etc/passwd` |
| `awk '$1 == "error"' file` | Filter by condition | `awk '$1 == "error"' log.txt` |
| `awk '$2 > 100' file` | Numeric comparison | `awk '$2 > 100' data.txt` |
| `awk 'NR == 5' file` | Match line number | `awk 'NR == 5' file.txt` |
| `awk 'END {print NR}' file` | Print total lines | `awk 'END {print NR}' file.txt` |
| `awk '{sum += $1} END {print sum}' file` | Sum column | `awk '{sum += $1} END {print sum}' nums.txt` |

## Text Manipulation Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `cut -d: -f1 file` | Cut fields by delimiter | `cut -d: -f1 /etc/passwd` |
| `sort file` | Sort lines | `sort names.txt` |
| `sort -n file` | Numeric sort | `sort -n numbers.txt` |
| `sort -r file` | Reverse sort | `sort -r file.txt` |
| `sort -u file` | Sort and remove duplicates | `sort -u names.txt` |
| `uniq file` | Remove consecutive duplicates | `uniq names.txt` |
| `tr 'a-z' 'A-Z' < file` | Translate characters | `tr '[:lower:]' '[:upper:]'` |
| `wc -l file` | Count lines | `wc -l file.txt` |
| `wc -w file` | Count words | `wc -w file.txt` |

## Process Management

| Command | Purpose | Example |
|---------|---------|---------|
| `command &` | Run in background | `long_task &` |
| `$!` | Last background PID | `echo $!` |
| `jobs` | List background jobs | `jobs` |
| `wait` | Wait for all background jobs | `wait` |
| `wait $pid` | Wait for specific job | `wait 1234` |
| `fg %job` | Bring job to foreground | `fg %1` |
| `bg %job` | Resume job in background | `bg %1` |
| `kill $pid` | Terminate process | `kill 1234` |
| `ps aux \| grep pattern` | List processes | `ps aux \| grep python` |

## File Testing

| Operator | Meaning | Example |
|----------|---------|---------|
| `-f file` | File exists | `[ -f "$file" ]` |
| `-d dir` | Directory exists | `[ -d "$dir" ]` |
| `-s file` | File exists and not empty | `[ -s "$file" ]` |
| `-r file` | File is readable | `[ -r "$file" ]` |
| `-w file` | File is writable | `[ -w "$file" ]` |
| `-x file` | File is executable | `[ -x "$file" ]` |
| `-e file` | File exists (any type) | `[ -e "$file" ]` |
| `file1 -nt file2` | file1 newer than file2 | `[ "$f1" -nt "$f2" ]` |
| `file1 -ot file2` | file1 older than file2 | `[ "$f1" -ot "$f2" ]` |

## Configuration File Sourcing

| Method | Purpose | Example |
|--------|---------|---------|
| `source file` | Load config variables | `source /etc/app.conf` |
| `. file` | Same as source | `. config.sh` |
| `set -a; source file; set +a` | Auto-export all vars | Ensures subshells have vars |
| `export VAR=value` | Make variable available | `export JAVA_HOME=/usr/lib/jvm` |
| `${VAR:-default}` | Use default if unset | `${LOG_LEVEL:-INFO}` |
| `${VAR:=default}` | Set and use default | `${COUNT:=0}` |

## Logging Best Practices

| Pattern | Purpose | Example |
|---------|---------|---------|
| `echo "[$(date)]" message` | Timestamp | `echo "[$(date '+%Y-%m-%d %H:%M:%S')] Started"` |
| `command 2>&1 \| tee -a log` | Log and display | Logs to file and stdout |
| `{...} > file 2>&1` | Group and redirect | Entire block redirects |
| `exec 1> log.txt` | Redirect script output | All subsequent output to file |
| `logger -t tag message` | System logging | `logger -t backup "Started"` |

## Common Patterns

### Safe Variable Expansion
```bash
"${var}"          # Always quote variables
"${var:-default}" # Use default if empty
"${var:?error}"   # Error if undefined
```

### Command Substitution
```bash
$(command)        # Modern syntax (preferred)
`command`         # Legacy syntax (avoid)
```

### Pipe to Multiple Commands
```bash
command | tee file1 file2 | grep pattern
```

### Error Handler
```bash
trap 'echo "Error at line $LINENO"' ERR
```
