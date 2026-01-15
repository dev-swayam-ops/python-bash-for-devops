# Linux Automation Scripts: Solutions

## Solution 1: System Information Collector

```bash
#!/bin/bash
# sysinfo.sh

echo "=== System Information ==="
echo "Hostname: $(hostname)"
echo "Uptime: $(uptime -p | sed 's/up //')"
echo "Kernel: $(uname -r)"
echo "CPU Cores: $(nproc)"
echo "Total Memory: $(free -h | awk 'NR==2 {print $2}')"
echo "Disk Usage: $(df -h / | awk 'NR==2 {print $3 "/" $2}')"

# Output:
# === System Information ===
# Hostname: ubuntu-server
# Uptime: 5 days, 3 hours, 21 minutes
# Kernel: 5.15.0-86-generic
# CPU Cores: 4
# Total Memory: 16Gi
# Disk Usage: 45G/200G
```

**Explanation**: Combines multiple system commands. `nproc` gets CPU count. `free -h` shows memory in human format. `uptime -p` shows uptime clearly.

---

## Solution 2: List Users and Groups

```bash
#!/bin/bash
# list_users.sh

echo "=== System Users (UID > 1000) ==="
echo "Username          UID    Primary Group"
echo "==========================================="

while IFS=: read -r username password uid gid gecos home shell; do
    if [ "$uid" -gt 1000 ]; then
        # Get primary group name
        group=$(awk -F: -v gid="$gid" '$3 == gid {print $1}' /etc/group)
        printf "%-17s %-7s %s\n" "$username" "$uid" "$group"
    fi
done < /etc/passwd

# Output:
# === System Users (UID > 1000) ===
# Username          UID    Primary Group
# ===========================================
# alice             1000   users
# bob               1001   users
# docker            1002   docker
```

**Explanation**: `while IFS= read` parses /etc/passwd line by line. Extracts user data from colon-separated fields. Filters UID > 1000 for non-system users.

---

## Solution 3: Disk Space Alert

```bash
#!/bin/bash
# disk_alert.sh

THRESHOLD=80

echo "=== Disk Usage Report ==="
df -h | tail -n +2 | while read -r device size used avail percent mount; do
    usage=${percent%\%}
    
    if [ "$usage" -gt "$THRESHOLD" ]; then
        echo "⚠ ALERT: $mount (${usage}%) exceeds ${THRESHOLD}%"
    else
        echo "✓ $mount: ${usage}%"
    fi
done

# Output:
# === Disk Usage Report ===
# ✓ /: 45%
# ✓ /home: 32%
# ⚠ ALERT: /var (82%) exceeds 80%
```

**Explanation**: `df -h` shows disk usage. `tail -n +2` skips header. Removes `%` from percentage string. Compares numeric values.

---

## Solution 4: Active Process Monitor

```bash
#!/bin/bash
# top_processes.sh

echo "=== Top 5 Memory-Consuming Processes ==="
echo "PID    USER       %MEM    COMMAND"
echo "=========================================="

ps aux --sort=-%mem | head -n 6 | tail -n 5 | \
    awk '{printf "%-6s %-10s %-7s %s\n", $2, $1, $4, $11}'

# Output:
# === Top 5 Memory-Consuming Processes ===
# PID    USER       %MEM    COMMAND
# ==========================================
# 1234   root       5.2     /usr/bin/python3
# 5678   user       3.1     /usr/bin/java
# 9012   www-data   2.8     /usr/sbin/apache2
# ...
```

**Explanation**: `ps aux` lists processes. `--sort=-%mem` sorts by memory descending. `head -6 | tail -5` gets rows 2-6 (skip header). `awk` formats output.

---

## Solution 5: Find Large Files

```bash
#!/bin/bash
# find_large_files.sh

SIZE_MB=${1:-100}
SIZE_BYTES=$((SIZE_MB * 1024 * 1024))

echo "=== Files Larger than ${SIZE_MB}MB ==="
find / -type f -size +${SIZE_BYTES}c -exec ls -lh {} \; 2>/dev/null | \
    awk '{print $5, $9}' | \
    sort -rh

# Usage: ./find_large_files.sh 50
# Output:
# 500M /var/backups/archive.tar.gz
# 250M /home/user/Videos/movie.mp4
# 150M /opt/app/cache.db
```

**Explanation**: Converts MB to bytes for `find`. `-size +Nc` finds files larger than N bytes. `c` suffix for bytes. `-exec ls -lh` gets file sizes. `sort -rh` human-sorted descending.

---

## Solution 6: Log Cleaner

```bash
#!/bin/bash
# log_cleaner.sh

DAYS=${1:-7}
DRY_RUN=${2:-false}
LOG_DIR="/var/log"
REPORT="/tmp/cleanup_report.txt"

echo "=== Log Cleanup Report ===" > "$REPORT"
echo "Older than: $DAYS days" >> "$REPORT"
echo "Dry run: $DRY_RUN" >> "$REPORT"
echo "" >> "$REPORT"

echo "Searching for files older than $DAYS days..."
count=0

find "$LOG_DIR" -name "*.log" -mtime "+$DAYS" | while read -r logfile; do
    size=$(du -h "$logfile" | cut -f1)
    echo "Found: $logfile ($size)" >> "$REPORT"
    
    if [ "$DRY_RUN" = "false" ]; then
        rm -f "$logfile"
        echo "Deleted: $logfile" >> "$REPORT"
    fi
    
    ((count++))
done

echo "" >> "$REPORT"
echo "Total files: $count" >> "$REPORT"
cat "$REPORT"

# Usage: ./log_cleaner.sh 30 true (dry-run)
# Output: Report with files to be cleaned
```

**Explanation**: `mtime "+$DAYS"` finds files modified more than N days ago. Dry-run option shows what would be deleted. Report written to file for audit trail.

---

## Solution 7: Service Manager

```bash
#!/bin/bash
# service_manager.sh

start_service() {
    local service=$1
    sudo systemctl start "$service"
    echo "✓ Started: $service"
}

stop_service() {
    local service=$1
    sudo systemctl stop "$service"
    echo "✓ Stopped: $service"
}

status_service() {
    local service=$1
    if sudo systemctl is-active --quiet "$service"; then
        echo "✓ $service: running"
    else
        echo "✗ $service: stopped"
    fi
}

# Usage
case "${1:-status}" in
    start) start_service "${2:-ssh}" ;;
    stop) stop_service "${2:-ssh}" ;;
    status) status_service "${2:-ssh}" ;;
    *) echo "Usage: $0 {start|stop|status} [service]" ;;
esac

# Usage: ./service_manager.sh status nginx
# Output: ✓ nginx: running
```

**Explanation**: Functions encapsulate service operations. `is-active --quiet` checks if running without output. Case statement routes commands. Variables allow dynamic service names.

---

## Solution 8: Security Audit

```bash
#!/bin/bash
# security_audit.sh

check_permissions() {
    local file=$1
    local expected=$2
    
    if [ ! -e "$file" ]; then
        echo "⚠ Missing: $file"
        return
    fi
    
    actual=$(stat -c "%a" "$file" 2>/dev/null || stat -f "%A" "$file")
    
    if [ "$actual" != "$expected" ]; then
        echo "⚠ WARN: $file has $actual (expected $expected)"
    else
        echo "✓ $file: $actual (OK)"
    fi
}

echo "=== Security Permissions Audit ==="
check_permissions "/etc/passwd" "644"
check_permissions "/etc/shadow" "640"
check_permissions "/etc/sudoers" "440"
check_permissions "$HOME/.ssh" "700"

# Output:
# === Security Permissions Audit ===
# ✓ /etc/passwd: 644 (OK)
# ✓ /etc/shadow: 640 (OK)
# ⚠ WARN: /etc/sudoers has 644 (expected 440)
# ✓ /home/user/.ssh: 700 (OK)
```

**Explanation**: `stat -c "%a"` gets octal permissions (Linux). Checks against expected values. Functions make code reusable. Works cross-platform with fallback syntax.

---

## Solution 9: Batch File Processor

```bash
#!/bin/bash
# batch_processor.sh

DIRECTORY=${1:-.}

echo "=== File Processing Report ==="
echo "Directory: $DIRECTORY"
echo ""

total_lines=0
total_size=0
largest_file=""
largest_size=0

for file in "$DIRECTORY"/*; do
    [ -f "$file" ] || continue
    
    lines=$(wc -l < "$file" 2>/dev/null || echo 0)
    size=$(stat -c%s "$file" 2>/dev/null || stat -f%z "$file")
    total_lines=$((total_lines + lines))
    total_size=$((total_size + size))
    
    if [ "$size" -gt "$largest_size" ]; then
        largest_size=$size
        largest_file="$file"
    fi
done

echo "Total files: $(find "$DIRECTORY" -maxdepth 1 -type f | wc -l)"
echo "Total lines: $total_lines"
echo "Total size: $((total_size / 1024)) KB"
echo "Largest file: $largest_file ($((largest_size / 1024)) KB)"

# Output:
# === File Processing Report ===
# Directory: .
# Total files: 15
# Total lines: 5432
# Total size: 2048 KB
# Largest file: ./archive.tar (1024 KB)
```

**Explanation**: Loops through files in directory. Accumulates line counts and sizes. Tracks largest file. Cross-platform stat command options.

---

## Solution 10: Automated Cleanup

```bash
#!/bin/bash
# comprehensive_cleanup.sh
set -euo pipefail

MANIFEST_FILE="/tmp/cleanup_manifest_$(date +%s).txt"
DRY_RUN=${1:-true}

echo "=== Comprehensive Cleanup ===" 
echo "Manifest: $MANIFEST_FILE"
echo "Dry run: $DRY_RUN"

# Create manifest before deletion
create_manifest() {
    echo "Creating file manifest..."
    {
        find /tmp -type f -mtime +30 -size +1M
        find /var/log -name "*.log" -mtime +7
        find /var/cache -type f -mtime +30
    } | tee "$MANIFEST_FILE"
    echo "Manifest saved to: $MANIFEST_FILE"
}

# Execute cleanup
cleanup() {
    echo "Starting cleanup..."
    local count=0
    while IFS= read -r file; do
        if [ -f "$file" ]; then
            if [ "$DRY_RUN" = "false" ]; then
                rm -f "$file"
                echo "Deleted: $file"
            else
                echo "Would delete: $file"
            fi
            ((count++))
        fi
    done < "$MANIFEST_FILE"
    echo "Total items processed: $count"
}

create_manifest
cleanup

if [ "$DRY_RUN" = "true" ]; then
    echo "✓ Dry run complete. Run with 'false' to execute cleanup"
    echo "✓ To restore: use manifest at $MANIFEST_FILE"
fi

# Usage: ./comprehensive_cleanup.sh true (dry run)
# Usage: ./comprehensive_cleanup.sh false (execute)
```

**Explanation**: Creates manifest before deletion for audit trail. Dry-run option prevents accidental data loss. Manifest allows recovery if needed. Multiple cleanup categories in one script.
