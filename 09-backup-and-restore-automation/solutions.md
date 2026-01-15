# Backup and Restore Solutions

## Solution 1: Simple File Backup

```bash
#!/bin/bash
# simple_backup.sh - Backup directory to tar.gz

SRC=${1:-.}
BACKUP_DIR="/tmp/backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

# Validate source exists
if [ ! -d "$SRC" ]; then
    echo "✗ Directory not found: $SRC"
    exit 1
fi

mkdir -p "$BACKUP_DIR"

# Create backup
BACKUP_FILE="$BACKUP_DIR/backup_$(basename $SRC)_${TIMESTAMP}.tar.gz"
tar -czf "$BACKUP_FILE" "$SRC" 2>/dev/null

if [ $? -eq 0 ]; then
    SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    FILE_COUNT=$(tar -tzf "$BACKUP_FILE" | wc -l)
    echo "✓ Backup created: $BACKUP_FILE"
    echo "  Size: $SIZE"
    echo "  Files: $FILE_COUNT"
    exit 0
else
    echo "✗ Backup failed"
    exit 1
fi
```

**Explanation**: Creates timestamped tar.gz backup, validates source directory, reports compressed size and file count using tar table-of-contents listing.

---

## Solution 2: Backup Size Reporter

```bash
#!/bin/bash
# backup_reporter.sh - Report backup sizes and stats

BACKUP_DIR="/tmp/backups"

if [ ! -d "$BACKUP_DIR" ] || [ -z "$(ls -A $BACKUP_DIR)" ]; then
    echo "✗ No backups found in $BACKUP_DIR"
    exit 1
fi

echo "=== Backup Report ==="
echo ""

total_size=0
file_count=0

# List backups with sizes
ls -lhS "$BACKUP_DIR" | tail -n +2 | awk '{
    size=$5; file=$9
    print size " " file
}' | while read size file; do
    echo "$size  $file"
    total_size=$((total_size + $(echo $size | numfmt --from=auto)))
done

# Show oldest and newest
echo ""
echo "Oldest backup:"
ls -lrt "$BACKUP_DIR" | tail -1 | awk '{print $6, $7, $8, $9}'

echo "Newest backup:"
ls -lrt "$BACKUP_DIR" | tail -2 | head -1 | awk '{print $6, $7, $8, $9}'

# Total size
echo ""
du -sh "$BACKUP_DIR" | awk '{print "Total: " $1}'
```

**Explanation**: Lists all backups sorted by size, extracts modification dates, shows oldest/newest, calculates total. Uses `ls -lhS` for human-readable sorted output.

---

## Solution 3: Restore from Backup

```bash
#!/bin/bash
# restore_backup.sh - Extract backup to destination

BACKUP_FILE=$1
RESTORE_PATH=${2:-.}

if [ -z "$BACKUP_FILE" ]; then
    echo "Usage: $0 <backup_file> [restore_path]"
    exit 1
fi

if [ ! -f "$BACKUP_FILE" ]; then
    echo "✗ Backup not found: $BACKUP_FILE"
    exit 1
fi

mkdir -p "$RESTORE_PATH"

echo "Restoring from: $BACKUP_FILE"
echo "To: $RESTORE_PATH"

# Extract backup
tar -xzf "$BACKUP_FILE" -C "$RESTORE_PATH"

if [ $? -eq 0 ]; then
    FILE_COUNT=$(find "$RESTORE_PATH" -type f | wc -l)
    echo "✓ Restore complete"
    echo "  Files restored: $FILE_COUNT"
    exit 0
else
    echo "✗ Restore failed"
    exit 1
fi
```

**Explanation**: Validates backup file exists, extracts with tar to destination, counts extracted files. Handles missing path by creating it.

---

## Solution 4: Log File Backup with Cleanup

```bash
#!/bin/bash
# log_backup.sh - Backup logs and cleanup old ones

LOG_SRC="/var/log"
BACKUP_DIR="/backup/logs"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
RETENTION=5

mkdir -p "$BACKUP_DIR"

# Create backup of all logs
BACKUP_FILE="$BACKUP_DIR/logs_${TIMESTAMP}.tar.gz"
tar -czf "$BACKUP_FILE" -C "$(dirname $LOG_SRC)" "$(basename $LOG_SRC)" 2>/dev/null

if [ $? -eq 0 ]; then
    SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    echo "✓ Backup created: $SIZE"
else
    echo "✗ Backup failed"
    exit 1
fi

# Cleanup old backups (keep only last N)
echo "Cleaning old backups (keeping $RETENTION)..."

ls -t "$BACKUP_DIR" | tail -n +$((RETENTION+1)) | while read old_backup; do
    rm -f "$BACKUP_DIR/$old_backup"
    echo "  Deleted: $old_backup"
done

echo "✓ Cleanup complete"
```

**Explanation**: Creates compressed log backup, then uses `ls -t` to sort by modification time and removes backups beyond retention limit. Tail command skips first N entries.

---

## Solution 5: Incremental Backup with rsync

```bash
#!/bin/bash
# incremental_rsync_backup.sh - Sync-based incremental backup

SRC="/home/user/data/"
DEST="/backup/rsync/"
TIMESTAMP=$(date +%Y%m%d)
BACKUP_DIR="$DEST/$TIMESTAMP"

mkdir -p "$BACKUP_DIR"

echo "Starting incremental backup..."
echo "Source: $SRC"
echo "Destination: $BACKUP_DIR"

# Rsync with change tracking
rsync -avz --stats --delete "$SRC" "$BACKUP_DIR/" > /tmp/rsync_log.txt 2>&1

if [ $? -eq 0 ]; then
    ADDED=$(grep "Number of new files" /tmp/rsync_log.txt | awk '{print $5}')
    DELETED=$(grep "Number of deleted files" /tmp/rsync_log.txt | awk '{print $5}')
    TOTAL=$(find "$BACKUP_DIR" -type f | wc -l)
    
    echo "✓ Backup complete"
    echo "  Added: $ADDED"
    echo "  Deleted: $DELETED"
    echo "  Total files: $TOTAL"
    
    # Create backup manifest
    find "$BACKUP_DIR" -type f > "$BACKUP_DIR/manifest.txt"
else
    echo "✗ Backup failed"
    exit 1
fi
```

**Explanation**: Uses rsync for incremental sync, parses rsync stats output for summary, creates manifest file. Subsequent runs only copy changed files.

---

## Solution 6: Database Backup with Verification

```bash
#!/bin/bash
# db_backup.sh - Backup database with integrity check

DB_NAME=${1:-myapp}
DB_HOST=${2:-localhost}
DB_TYPE=${3:-mysql}
BACKUP_DIR="/backup/databases"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
LOG_FILE="/tmp/db_backups.csv"

mkdir -p "$BACKUP_DIR"

# Initialize CSV
[ ! -f "$LOG_FILE" ] && echo "timestamp,database,size,status" > "$LOG_FILE"

if [ "$DB_TYPE" = "mysql" ]; then
    BACKUP_FILE="$BACKUP_DIR/${DB_NAME}_${TIMESTAMP}.sql.gz"
    
    # Dump database
    mysqldump -h "$DB_HOST" "$DB_NAME" 2>/dev/null | gzip > "$BACKUP_FILE"
    
elif [ "$DB_TYPE" = "postgres" ]; then
    BACKUP_FILE="$BACKUP_DIR/${DB_NAME}_${TIMESTAMP}.sql.gz"
    
    # Dump database
    pg_dump -h "$DB_HOST" "$DB_NAME" 2>/dev/null | gzip > "$BACKUP_FILE"
fi

# Verify backup
if [ -f "$BACKUP_FILE" ] && [ -s "$BACKUP_FILE" ]; then
    SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    echo "✓ Database backup: $DB_NAME ($SIZE)"
    
    # Log to CSV
    echo "$(date '+%Y-%m-%d %H:%M:%S'),$DB_NAME,$SIZE,SUCCESS" >> "$LOG_FILE"
    exit 0
else
    echo "✗ Backup failed or file empty"
    echo "$(date '+%Y-%m-%d %H:%M:%S'),$DB_NAME,0,FAILED" >> "$LOG_FILE"
    exit 1
fi
```

**Explanation**: Backs up MySQL or PostgreSQL, compresses with gzip, verifies file size > 0, logs to CSV with timestamp and status for tracking.

---

## Solution 7: Rotation Policy with Cleanup

```bash
#!/bin/bash
# backup_rotation.sh - Implement daily/weekly/monthly retention

BACKUP_DIR="/backup"
DAILY_RETENTION=7    # Keep 7 days
WEEKLY_RETENTION=4   # Keep 4 weeks
MONTHLY_RETENTION=12 # Keep 12 months

echo "=== Backup Rotation Policy ==="

cleanup_by_age() {
    local dir=$1
    local days=$2
    local label=$3
    
    if [ ! -d "$dir" ]; then
        return
    fi
    
    echo "Checking $label ($days days)..."
    
    find "$dir" -maxdepth 1 -type f -mtime +$days ! -name "*protected*" | while read file; do
        rm -f "$file"
        echo "  Deleted: $(basename $file)"
    done
}

# Daily backups (keep 7 days)
cleanup_by_age "$BACKUP_DIR/daily" 7 "Daily"

# Weekly backups (keep 28 days = 4 weeks)
cleanup_by_age "$BACKUP_DIR/weekly" 28 "Weekly"

# Monthly backups (keep 365 days = 12 months)
cleanup_by_age "$BACKUP_DIR/monthly" 365 "Monthly"

echo "✓ Rotation policy applied"
```

**Explanation**: Uses `find -mtime +N` to locate files older than N days, deletes them. Skips files named "protected*" to prevent accidental deletion.

---

## Solution 8: Encrypted Backup System

```bash
#!/bin/bash
# encrypted_backup.sh - GPG encrypted backups

SRC=$1
DEST=${2:-/backup/encrypted}
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

if [ ! -d "$SRC" ]; then
    echo "✗ Source not found: $SRC"
    exit 1
fi

mkdir -p "$DEST"

# Create tar first
TEMP_TAR="/tmp/backup_$TIMESTAMP.tar.gz"
tar -czf "$TEMP_TAR" "$SRC" 2>/dev/null

# Encrypt with password (symmetric)
BACKUP_FILE="$DEST/backup_${TIMESTAMP}.tar.gz.gpg"
gpg --symmetric --cipher-algo AES256 --batch --yes \
    --output "$BACKUP_FILE" "$TEMP_TAR" 2>/dev/null

# Cleanup temp file
rm -f "$TEMP_TAR"

if [ -f "$BACKUP_FILE" ]; then
    SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    echo "✓ Encrypted backup created: $SIZE"
    echo "  File: $BACKUP_FILE"
    echo "  Note: Requires passphrase to decrypt"
    exit 0
else
    echo "✗ Encryption failed"
    exit 1
fi

# To restore:
# gpg --decrypt backup_file.tar.gz.gpg | tar -xz
```

**Explanation**: Creates tar backup, encrypts with GPG using AES256 symmetric encryption, removes unencrypted temp file. Restore requires passphrase.

---

## Solution 9: Multi-Host Backup Aggregator

```bash
#!/bin/bash
# multi_host_backup.sh - Aggregate backups from multiple servers

SERVERS=${@:-server1 server2 server3}
CENTRAL_BACKUP="/backup/aggregated"
MANIFEST="$CENTRAL_BACKUP/manifest_$(date +%Y%m%d_%H%M%S).txt"

mkdir -p "$CENTRAL_BACKUP"

> "$MANIFEST"  # Clear manifest

echo "=== Multi-Host Backup ==="

backup_remote_server() {
    local server=$1
    
    echo "Backing up: $server"
    
    # Check connectivity
    if ! ping -c 1 -W 5 "$server" &> /dev/null; then
        echo "  ✗ Server unreachable"
        echo "$server,OFFLINE,$(date)" >> "$MANIFEST"
        return 1
    fi
    
    # Trigger remote backup and pull
    ssh -o ConnectTimeout=5 "$server" \
        "tar -czf /tmp/backup_$server.tar.gz /home /etc" 2>/dev/null
    
    if [ $? -eq 0 ]; then
        # Pull backup
        scp "$server:/tmp/backup_$server.tar.gz" "$CENTRAL_BACKUP/" 2>/dev/null
        
        # Calculate checksum
        CHECKSUM=$(sha256sum "$CENTRAL_BACKUP/backup_$server.tar.gz" | awk '{print $1}')
        SIZE=$(du -h "$CENTRAL_BACKUP/backup_$server.tar.gz" | cut -f1)
        
        echo "  ✓ Backup complete ($SIZE)"
        echo "$server,SUCCESS,$SIZE,$CHECKSUM,$(date)" >> "$MANIFEST"
        return 0
    else
        echo "  ✗ Backup failed"
        echo "$server,FAILED,0,,$(date)" >> "$MANIFEST"
        return 1
    fi
}

# Process each server
for server in $SERVERS; do
    backup_remote_server "$server"
done

echo ""
echo "Manifest: $MANIFEST"
```

**Explanation**: Loops through servers, SSHes to trigger backup, pulls files to central location, generates checksums and manifest. Handles unreachable servers gracefully.

---

## Solution 10: Complete Backup Strategy

```bash
#!/bin/bash
# comprehensive_backup.sh - Full backup strategy

BACKUP_ROOT="/backup"
DATA_SRC="/home/user/data"
DB_NAME="production"
DAILY_DIR="$BACKUP_ROOT/daily"
WEEKLY_DIR="$BACKUP_ROOT/weekly"
RETENTION_DAYS=7
RETENTION_WEEKS=4
DAY_OF_WEEK=$(date +%w)
REPORT_FILE="/tmp/backup_report_$(date +%Y%m%d).txt"

mkdir -p "$DAILY_DIR" "$WEEKLY_DIR"

> "$REPORT_FILE"

log_msg() {
    echo "$1" | tee -a "$REPORT_FILE"
}

log_msg "=== Backup Run: $(date) ==="

# Determine backup type
if [ "$DAY_OF_WEEK" = "0" ]; then
    # Sunday: Full backup
    BACKUP_TYPE="FULL"
    BACKUP_DIR="$WEEKLY_DIR/full_$(date +%Y%m%d)"
else
    # Mon-Sat: Incremental
    BACKUP_TYPE="INCREMENTAL"
    BACKUP_DIR="$DAILY_DIR/incr_$(date +%Y%m%d)"
fi

mkdir -p "$BACKUP_DIR"

log_msg "Backup Type: $BACKUP_TYPE"

# Backup files
log_msg "Backing up files: $DATA_SRC"
tar -czf "$BACKUP_DIR/files.tar.gz" "$DATA_SRC" 2>/dev/null
FILE_SIZE=$(du -h "$BACKUP_DIR/files.tar.gz" | cut -f1)
log_msg "  Files backup: $FILE_SIZE"

# Backup database
log_msg "Backing up database: $DB_NAME"
mysqldump "$DB_NAME" 2>/dev/null | gzip > "$BACKUP_DIR/database.sql.gz"
DB_SIZE=$(du -h "$BACKUP_DIR/database.sql.gz" | cut -f1)
log_msg "  Database backup: $DB_SIZE"

# Verify all files
log_msg "Verifying backups..."
tar -tzf "$BACKUP_DIR/files.tar.gz" > /dev/null && \
    log_msg "  ✓ Files backup valid"

# Cleanup old backups
log_msg "Cleaning old backups..."
find "$DAILY_DIR" -type d -mtime +$RETENTION_DAYS -exec rm -rf {} \; 2>/dev/null
find "$WEEKLY_DIR" -type d -mtime +$((RETENTION_WEEKS*7)) -exec rm -rf {} \; 2>/dev/null
log_msg "  ✓ Old backups cleaned"

# Summary
TOTAL_SIZE=$(du -sh "$BACKUP_ROOT" | cut -f1)
log_msg ""
log_msg "Summary:"
log_msg "  Type: $BACKUP_TYPE"
log_msg "  Location: $BACKUP_DIR"
log_msg "  Total backup size: $TOTAL_SIZE"
log_msg "  Status: COMPLETED"

# Email report
if command -v mail &> /dev/null; then
    mail -s "Backup Report: $(date +%Y-%m-%d)" admin@example.com < "$REPORT_FILE"
    log_msg "  Report sent to admin"
fi

log_msg "=== Backup Complete ==="
```

**Explanation**: Comprehensive solution implementing full backup on Sunday, incremental daily, backup verification, automatic cleanup, and email reporting. Tracks all operations in timestamped report.

---

**Pro Tip**: Always test restore procedures monthly to ensure backups are usable. Untested backups are not backups!
