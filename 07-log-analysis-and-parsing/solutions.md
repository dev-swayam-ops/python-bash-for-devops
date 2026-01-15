# Log Analysis and Parsing: Solutions

## Solution 1: Count Errors in Log

```bash
#!/bin/bash
# count_errors.sh

LOG_FILE=${1:-.}

echo "=== Error Count ==="
echo "Errors: $(grep -c "ERROR" "$LOG_FILE" 2>/dev/null || echo 0)"
echo "Warnings: $(grep -c "WARN" "$LOG_FILE" 2>/dev/null || echo 0)"
echo "Criticals: $(grep -c "CRITICAL" "$LOG_FILE" 2>/dev/null || echo 0)"

# Usage: ./count_errors.sh app.log
# Output:
# === Error Count ===
# Errors: 5
# Warnings: 3
# Criticals: 1
```

**Explanation**: `grep -c` counts matching lines. `2>/dev/null` suppresses errors if file not found. Provides fallback to 0.

---

## Solution 2: Filter by Time Range

```bash
#!/bin/bash
# filter_by_time.sh

LOG_FILE=$1
START_TIME=${2:-10:30}
END_TIME=${3:-10:35}

echo "=== Logs from $START_TIME to $END_TIME ==="
awk -v start="$START_TIME" -v end="$END_TIME" \
    '$2 >= start && $2 <= end' "$LOG_FILE"

# Usage: ./filter_by_time.sh app.log 10:30 10:35
# Output: All logs in that time range
```

**Explanation**: `awk` compares times as strings (works for HH:MM format). Variables passed with `-v`. Filters second field (timestamp).

---

## Solution 3: Extract Specific Columns

```bash
#!/bin/bash
# extract_columns.sh

LOG_FILE=$1

echo "=== Timestamp | Message ==="
awk '{print $1 " " $2, $(NF-1), $NF}' "$LOG_FILE" | column -t

# Usage: ./extract_columns.sh app.log
# Output:
# 2024-01-15 10:30:45   Starting application
# 2024-01-15 10:30:46   Connected to database
```

**Explanation**: Extracts first field (date), second (time), penultimate (severity), last (message). `column -t` formats as table.

---

## Solution 4: Find Critical Errors

```bash
#!/bin/bash
# find_critical.sh

LOG_FILE=$1

echo "=== Critical and Error Logs ==="
grep -n -E "CRITICAL|ERROR" "$LOG_FILE"

# Usage: ./find_critical.sh app.log
# Output:
# 3:2024-01-15 10:30:47 ERROR Connection timeout
# 6:2024-01-15 10:31:06 ERROR Unexpected error
# 8:2024-01-15 10:31:08 CRITICAL Service unavailable
```

**Explanation**: `grep -n` shows line numbers. `-E` allows regex patterns. `|` means OR.

---

## Solution 5: Log Statistics Report

```bash
#!/bin/bash
# log_stats.sh

LOG_FILE=$1

{
    echo "=== Log Statistics ==="
    echo "Total lines: $(wc -l < "$LOG_FILE")"
    echo "Errors: $(grep -c "ERROR" "$LOG_FILE" 2>/dev/null || echo 0)"
    echo "Warnings: $(grep -c "WARN" "$LOG_FILE" 2>/dev/null || echo 0)"
    echo "Criticals: $(grep -c "CRITICAL" "$LOG_FILE" 2>/dev/null || echo 0)"
    
    if grep -q "^[0-9]\." "$LOG_FILE"; then
        echo "Unique IPs: $(grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' "$LOG_FILE" | sort -u | wc -l)"
    fi
} | tee report.txt

echo "Report saved to: report.txt"

# Output:
# === Log Statistics ===
# Total lines: 8
# Errors: 2
# Warnings: 1
# Criticals: 1
```

**Explanation**: Braces group output for logging. `grep -q` quiet mode (yes/no). IPv4 regex extracts IPs. `sort -u` unique IPs.

---

## Solution 6: Parse Apache Access Logs

```bash
#!/bin/bash
# top_urls.sh

LOG_FILE=$1

echo "=== Top 5 Requested URLs ==="
awk '{print $7}' "$LOG_FILE" | \
    sort | uniq -c | sort -rn | head -5

# Usage: ./top_urls.sh /var/log/apache2/access.log
# Output:
#   45 /api/users
#   32 /api/products
#   28 /images/logo.png
```

**Explanation**: 7th field is URL in Apache logs. `uniq -c` counts occurrences. `sort -rn` reverse numeric sort.

---

## Solution 7: JSON Log Parser

```python
#!/usr/bin/env python3
# parse_json_logs.py

import json
import sys

log_file = sys.argv[1]

print("=== JSON Log Analysis ===")
with open(log_file) as f:
    for line in f:
        try:
            log_entry = json.loads(line)
            timestamp = log_entry.get("timestamp", "N/A")
            level = log_entry.get("level", "N/A")
            message = log_entry.get("message", "N/A")
            print(f"{timestamp} [{level}] {message}")
        except json.JSONDecodeError:
            print(f"Invalid JSON: {line.strip()}")

# Usage: python3 parse_json_logs.py logs.json
# Expects: {"timestamp":"2024-01-15T10:30:45","level":"ERROR","message":"Connection failed"}
```

**Explanation**: `json.loads()` parses JSON string. `.get()` safely accesses keys. Skips invalid JSON lines.

---

## Solution 8: Log Comparison

```bash
#!/bin/bash
# compare_logs.sh

OLD_LOG=$1
NEW_LOG=$2

echo "=== New Errors Not in Old Log ==="
grep "ERROR" "$NEW_LOG" > /tmp/new_errors.txt
grep "ERROR" "$OLD_LOG" > /tmp/old_errors.txt

comm -13 /tmp/old_errors.txt /tmp/new_errors.txt

echo ""
echo "=== Summary ==="
echo "New errors: $(wc -l < /tmp/new_errors.txt)"
echo "Old errors: $(wc -l < /tmp/old_errors.txt)"

# Usage: ./compare_logs.sh app.log.1 app.log.2
# Output: Lines in new but not in old
```

**Explanation**: `comm -13` shows lines in file2 but not file1. Useful for detecting new issues.

---

## Solution 9: Real-time Log Tail with Filter

```bash
#!/bin/bash
# tail_filter.sh

LOG_FILE=$1
PATTERN=${2:-ERROR}

echo "Tailing $LOG_FILE for $PATTERN..."
tail -f "$LOG_FILE" | grep --line-buffered "$PATTERN" | \
    while read line; do
        echo -e "\033[91m$line\033[0m"  # Red color
    done

# Usage: ./tail_filter.sh app.log ERROR
# Shows: Real-time errors in red
```

**Explanation**: `tail -f` follows file. `--line-buffered` flushes output. ANSI color codes highlight matches.

---

## Solution 10: Log Rotation and Archive

```bash
#!/bin/bash
# rotate_logs.sh
set -euo pipefail

LOG_DIR=${1:-.}
DAYS=${2:-7}

echo "=== Log Rotation ==="
for logfile in "$LOG_DIR"/*.log; do
    if [ ! -f "$logfile" ]; then
        continue
    fi
    
    timestamp=$(date +%Y%m%d_%H%M%S)
    backup="$logfile.$timestamp"
    
    # Rotate
    cp "$logfile" "$backup"
    > "$logfile"  # Truncate
    echo "Rotated: $logfile → $backup"
    
    # Archive old files
    find "$LOG_DIR" -name "*.log.*" -mtime "+$DAYS" | while read old; do
        gzip "$old"
        echo "Archived: $old → ${old}.gz"
    done
done

# Usage: ./rotate_logs.sh /var/log 7
# Rotates logs, compresses files older than 7 days
```

**Explanation**: `cp` creates backup. `> file` truncates original. `find -mtime` finds files older than N days. `gzip` compresses.
