# Bash Advanced Scripting: Solutions

## Solution 1: Exit Code Validation

```bash
#!/bin/bash
# exit_code_check.sh

echo "Attempting command..."
ls /nonexistent 2>/dev/null

exit_code=$?

if [ $exit_code -eq 0 ]; then
    echo "✓ Command succeeded"
else
    echo "✗ Command failed with exit code: $exit_code"
fi

exit $exit_code
```

**Explanation**: `$?` captures the exit code immediately after command. Exit code 0 means success, any other value indicates failure. We save it to avoid losing the value.

---

## Solution 2: Configuration File Parser

```bash
#!/bin/bash
# config_parser.sh

# Create config file
cat > app.conf << 'EOF'
APP_NAME=MyDevOpsApp
VERSION=1.0.0
LOG_LEVEL=INFO
MAX_CONNECTIONS=100
EOF

# Parse and display
echo "=== Application Configuration ==="
while IFS='=' read -r key value; do
    [ -z "$key" ] && continue
    echo "$key: $value"
done < app.conf
```

**Explanation**: `IFS='='` sets field separator to equals sign. `read -r` reads line safely. Loop skips empty lines. This pattern extracts key-value pairs.

---

## Solution 3: Basic Text Processing

```bash
#!/bin/bash
# log_analyzer.sh

cat > app.log << 'EOF'
INFO: Server started
ERROR: Connection timeout
WARNING: Memory usage high
ERROR: Auth failed
INFO: Data synced
EOF

echo "=== Log Analysis ==="
total_lines=$(wc -l < app.log)
error_count=$(grep -c "ERROR" app.log)

echo "Total lines: $total_lines"
echo "Errors found: $error_count"
grep "ERROR" app.log
```

**Explanation**: `wc -l` counts lines. `grep -c` counts matching lines. Standard grep shows matches. Redirect `< file` avoids printing filename.

---

## Solution 4: Simple awk Usage

```bash
#!/bin/bash
# awk_filter.sh

cat > people.csv << 'EOF'
Alice,28
Bob,22
Charlie,30
Diana,25
EOF

echo "=== People Older Than 25 ==="
awk -F',' '$2 > 25 {print $1, "age:", $2}' people.csv

# Output:
# Alice age: 28
# Charlie age: 30
```

**Explanation**: `-F','` sets field separator to comma. `$1` and `$2` access first and second fields. Condition `$2 > 25` filters rows. Pattern-action format is key to awk.

---

## Solution 5: Regex Validation Script

```bash
#!/bin/bash
# email_validator.sh

read -p "Enter email: " email

email_regex="^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"

if [[ $email =~ $email_regex ]]; then
    echo "✓ Valid email: $email"
else
    echo "✗ Invalid email: $email"
fi
```

**Explanation**: `[[ ]]` allows regex matching with `=~`. Pattern checks for alphanumeric chars, @, domain, dot, and TLD. Use double quotes for regex, no slashes needed.

---

## Solution 6: Log File Analyzer

```bash
#!/bin/bash
# advanced_log_analyzer.sh

cat > system.log << 'EOF'
INFO: Process started
ERROR: Database connection failed
WARNING: High memory usage
ERROR: Timeout on API call
INFO: Recovery initiated
WARNING: Disk space low
EOF

LOG_FILE="system.log"
REPORT_FILE="log_report.txt"

{
    echo "=== Log Analysis Report ==="
    echo "Generated: $(date)"
    echo
    echo "Summary:"
    echo "- Total lines: $(wc -l < "$LOG_FILE")"
    echo "- Errors: $(grep -c "ERROR" "$LOG_FILE")"
    echo "- Warnings: $(grep -c "WARNING" "$LOG_FILE")"
    echo
    echo "Error Details:"
    grep "ERROR" "$LOG_FILE"
} > "$REPORT_FILE"

cat "$REPORT_FILE"
```

**Explanation**: Braces `{}` group commands whose output redirects to file. This is cleaner than appending each line. Date command adds timestamp. Statistics extracted with grep.

---

## Solution 7: Backup with Timestamps

```bash
#!/bin/bash
# backup_with_log.sh
set -euo pipefail

source_dir="./data"
backup_dir="./backups"
timestamp=$(date +%Y%m%d_%H%M%S)
backup_file="$backup_dir/backup_$timestamp.tar.gz"

mkdir -p "$backup_dir"

echo "[$(date)] Starting backup of $source_dir"

# Create backup
tar -czf "$backup_file" "$source_dir" 2>&1 || {
    echo "✗ Backup failed"
    exit 1
}

# Validate backup
if tar -tzf "$backup_file" > /dev/null 2>&1; then
    echo "✓ Backup validated: $backup_file"
else
    echo "✗ Backup corrupted"
    exit 1
fi
```

**Explanation**: `set -euo pipefail` enables strict mode. Timestamp ensures unique files. `tar -tzf` validates archive integrity. Error handling exits on failure.

---

## Solution 8: sed for Text Replacement

```bash
#!/bin/bash
# template_engine.sh

cat > app.conf.template << 'EOF'
APP_NAME={{APP_NAME}}
VERSION={{VERSION}}
ENVIRONMENT={{ENVIRONMENT}}
EOF

APP_NAME="MyApp"
VERSION="2.0"
ENVIRONMENT="production"

output_file="app.conf"

sed -e "s|{{APP_NAME}}|$APP_NAME|g" \
    -e "s|{{VERSION}}|$VERSION|g" \
    -e "s|{{ENVIRONMENT}}|$ENVIRONMENT|g" \
    app.conf.template > "$output_file"

echo "Generated configuration:"
cat "$output_file"
```

**Explanation**: Multiple `-e` flags apply sequential replacements. Pipe `|` separator avoids conflicts with `/`. `-g` flag replaces all occurrences. Output redirects to new file.

---

## Solution 9: Retry Logic

```bash
#!/bin/bash
# retry_command.sh

retry_command() {
    local command=$1
    local max_retries=${2:-3}
    local attempt=1

    while [ $attempt -le $max_retries ]; do
        echo "Attempt $attempt of $max_retries"
        
        if eval "$command"; then
            echo "✓ Command succeeded"
            return 0
        fi
        
        if [ $attempt -lt $max_retries ]; then
            echo "Waiting 2 seconds before retry..."
            sleep 2
        fi
        
        ((attempt++))
    done
    
    echo "✗ Command failed after $max_retries attempts"
    return 1
}

# Test with failing then passing command
retry_command "[ $((RANDOM % 2)) -eq 1 ]" 5
```

**Explanation**: Loop with counter tracks attempts. `eval` executes stored command string. Success returns 0 immediately. `sleep` adds delay between retries. Exit status determines retry.

---

## Solution 10: Parallel Job Processing

```bash
#!/bin/bash
# parallel_processor.sh

process_file() {
    local file=$1
    echo "Processing $file..."
    sleep 1  # Simulate work
    echo "✓ Completed $file"
}

# Create test files
touch file1.txt file2.txt file3.txt file4.txt

echo "Starting parallel processing..."

# Process files in background
for file in *.txt; do
    process_file "$file" &
    pids+=($!)  # Store process ID
done

echo "Waiting for all jobs to complete..."
failed=0
for pid in "${pids[@]}"; do
    if ! wait $pid; then
        ((failed++))
    fi
done

if [ $failed -eq 0 ]; then
    echo "✓ All jobs completed successfully"
else
    echo "✗ $failed jobs failed"
    exit 1
fi
```

**Explanation**: `&` launches commands in background. `$!` captures most recent background PID. Array stores all PIDs. Loop waits for each process. Failed counter tracks errors.
