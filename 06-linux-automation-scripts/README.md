# Linux Automation Scripts

## What You'll Learn
- Automating common Linux system administration tasks
- User and group management scripts
- Disk and system monitoring
- Log file analysis and rotation
- Service management automation
- Package management automation
- Backup and restore automation
- Security and permission management

## Prerequisites
- Completed: [05-cli-tools-and-argparse](../05-cli-tools-and-argparse)
- Linux system access (Ubuntu/CentOS/Debian)
- Sudo or root privileges for system tasks
- Comfort with bash scripting and system commands

## Key Concepts
- **System Administration**: Automating repetitive admin tasks
- **User Management**: Creating/deleting users and groups
- **Log Management**: Analyzing and rotating logs
- **Monitoring**: Tracking system health metrics
- **Cron Jobs**: Scheduling automated tasks
- **Service Control**: Starting, stopping, restarting services
- **Backup Strategy**: Implementing automated backups

## Hands-on Lab: Building System Admin Scripts

### Step 1: User Management Script
```bash
cat > manage_users.sh << 'EOF'
#!/bin/bash
set -euo pipefail

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

create_user() {
    local username=$1
    local group=${2:-devops}
    
    if id "$username" &>/dev/null; then
        log "✗ User $username already exists"
        return 1
    fi
    
    sudo useradd -m -g "$group" "$username"
    log "✓ Created user: $username"
}

list_users() {
    log "=== System Users ==="
    awk -F: '$3 >= 1000 {print $1}' /etc/passwd
}

# Usage examples
create_user "developer" "devops"
list_users
EOF

chmod +x manage_users.sh
./manage_users.sh

# Expected output:
# [2024-01-15 10:30:45] ✓ Created user: developer
# [2024-01-15 10:30:45] === System Users ===
# (list of users)
```

### Step 2: Disk Space Monitor
```bash
cat > check_disk_space.sh << 'EOF'
#!/bin/bash

THRESHOLD=80

echo "=== Disk Space Report ==="
while IFS= read -r line; do
    usage=$(echo "$line" | awk '{print $5}' | sed 's/%//')
    mount=$(echo "$line" | awk '{print $6}')
    
    if [ "$usage" -gt "$THRESHOLD" ]; then
        echo "⚠ ALERT: $mount is ${usage}% full"
    else
        echo "✓ $mount: ${usage}%"
    fi
done < <(df -h | tail -n +2)
EOF

chmod +x check_disk_space.sh
./check_disk_space.sh

# Expected output:
# === Disk Space Report ===
# ✓ /: 45%
# ✓ /home: 32%
```

### Step 3: Log File Analyzer
```bash
cat > analyze_logs.sh << 'EOF'
#!/bin/bash

LOG_FILE="${1:-.}"
log_dir=$(find "$LOG_FILE" -name "*.log" -type f | head -1)

if [ -z "$log_dir" ]; then
    echo "✗ No log files found"
    exit 1
fi

echo "=== Log Analysis ==="
echo "Total lines: $(wc -l < "$log_dir")"
echo "Errors: $(grep -c "ERROR\|error" "$log_dir" || true)"
echo "Warnings: $(grep -c "WARN\|warn" "$log_dir" || true)"
echo "Last 3 errors:"
grep -i "error" "$log_dir" | tail -3 || echo "No errors found"
EOF

chmod +x analyze_logs.sh
./analyze_logs.sh /var/log

# Expected output:
# === Log Analysis ===
# Total lines: 1234
# Errors: 45
# Warnings: 12
```

### Step 4: Service Status Check
```bash
cat > check_services.sh << 'EOF'
#!/bin/bash

services=("ssh" "cron" "nginx" "docker")

echo "=== Service Status ==="
for service in "${services[@]}"; do
    if systemctl is-active --quiet "$service" 2>/dev/null; then
        echo "✓ $service: running"
    else
        echo "✗ $service: stopped"
    fi
done
EOF

chmod +x check_services.sh
./check_services.sh

# Expected output:
# === Service Status ===
# ✓ ssh: running
# ✓ cron: running
# ✗ nginx: stopped
```

### Step 5: Automated Backup Script
```bash
cat > automated_backup.sh << 'EOF'
#!/bin/bash
set -euo pipefail

BACKUP_DIR="/backup"
SOURCE_DIR="/home/user"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/backup_$DATE.tar.gz"

mkdir -p "$BACKUP_DIR"

echo "Starting backup..."
tar -czf "$BACKUP_FILE" "$SOURCE_DIR" 2>/dev/null

if [ -f "$BACKUP_FILE" ]; then
    SIZE=$(du -sh "$BACKUP_FILE" | cut -f1)
    echo "✓ Backup created: $BACKUP_FILE ($SIZE)"
else
    echo "✗ Backup failed"
    exit 1
fi

# Keep only last 7 backups
find "$BACKUP_DIR" -name "backup_*.tar.gz" -mtime +7 -delete
EOF

chmod +x automated_backup.sh
# ./automated_backup.sh (requires proper permissions)

# Expected output:
# Starting backup...
# ✓ Backup created: /backup/backup_20240115_103045.tar.gz (120M)
```

## Validation Checklist
- [ ] Created user management script successfully
- [ ] Disk space checker shows correct percentages
- [ ] Log analyzer parses log files correctly
- [ ] Service status script checks all services
- [ ] Backup script creates compressed files
- [ ] All error handling works as expected

## Cleanup
```bash
rm -f manage_users.sh check_disk_space.sh analyze_logs.sh check_services.sh automated_backup.sh
```

## Common Mistakes
1. **Not using set -e**: Script continues after errors
2. **Missing sudo privileges**: Some commands need elevation
3. **Hard-coded paths**: Use variables for flexibility
4. **No error logging**: Always log errors for debugging
5. **Race conditions in loops**: Use `while IFS= read` instead of pipes

## Troubleshooting

### "Permission denied" on script execution
- **Cause**: Script doesn't have execute permission
- **Solution**: Run `chmod +x script.sh`

### "useradd: command not found"
- **Cause**: Command not in PATH or not installed
- **Solution**: Use full path `/usr/sbin/useradd` or install package

### Script stops on first error
- **Cause**: Missing error handling
- **Solution**: Add `set -e` at start or check exit codes

### Cron job not executing
- **Cause**: Wrong path, permissions, or schedule
- **Solution**: Check crontab, use absolute paths, test manually

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [automation cheatsheet](cheatsheet.md)
3. Explore cron scheduling and systemd services
4. Create monitoring dashboards
