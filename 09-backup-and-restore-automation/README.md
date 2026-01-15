# Backup and Restore Automation

## What You'll Learn
- Creating automated backup strategies (full, incremental, differential)
- Backing up files, directories, and databases
- Backup compression and encryption
- Restoring data from backups
- Scheduling backups with cron/systemd
- Backup verification and testing
- Off-site backup storage
- Disaster recovery workflows

## Prerequisites
- Completed: [08-monitoring-and-alerting-scripts](../08-monitoring-and-alerting-scripts)
- Basic understanding of tar, gzip, rsync
- Database backup knowledge helpful
- Storage/filesystem concepts

## Key Concepts
- **Full Backup**: Complete copy of all data
- **Incremental**: Only changed files since last backup
- **Differential**: Only changed files since last full backup
- **Compression**: Reduce backup size (gzip, bzip2, xz)
- **Encryption**: Protect sensitive backup data
- **Retention**: Keep backups for N days/weeks/months
- **RTO/RPO**: Recovery Time/Point Objectives
- **Versioning**: Keep multiple backup versions

## Hands-on Lab: Complete Backup Solution

### Step 1: Full File Backup
```bash
cat > backup_files.sh << 'EOF'
#!/bin/bash

SRC="/home/user/documents"
BACKUP_DIR="/backup/files"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/backup_$TIMESTAMP.tar.gz"

mkdir -p "$BACKUP_DIR"

# Create compressed backup
tar -czf "$BACKUP_FILE" -C "$(dirname $SRC)" "$(basename $SRC)" 2>/dev/null

if [ $? -eq 0 ]; then
    SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    echo "✓ Backup created: $BACKUP_FILE ($SIZE)"
else
    echo "✗ Backup failed"
    exit 1
fi
EOF

chmod +x backup_files.sh
./backup_files.sh

# Expected output:
# ✓ Backup created: /backup/files/backup_20260115_120530.tar.gz (2.5M)
```

### Step 2: Database Backup (MySQL)
```bash
cat > backup_database.sh << 'EOF'
#!/bin/bash

DB_NAME="myapp"
DB_USER="root"
BACKUP_DIR="/backup/databases"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/${DB_NAME}_${TIMESTAMP}.sql.gz"

mkdir -p "$BACKUP_DIR"

# Dump and compress database
mysqldump -u "$DB_USER" "$DB_NAME" 2>/dev/null | gzip > "$BACKUP_FILE"

if [ $? -eq 0 ]; then
    SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    echo "✓ Database backup: $BACKUP_FILE ($SIZE)"
else
    echo "✗ Database backup failed"
    exit 1
fi
EOF

chmod +x backup_database.sh
./backup_database.sh

# Expected output:
# ✓ Database backup: /backup/databases/myapp_20260115_120530.sql.gz (1.2M)
```

### Step 3: Incremental Backup with rsync
```bash
cat > incremental_backup.sh << 'EOF'
#!/bin/bash

SRC="/home/user/data/"
DEST="/backup/rsync/"
TIMESTAMP=$(date +%Y%m%d)

mkdir -p "$DEST"

# Backup only changed files
rsync -avz --delete "$SRC" "$DEST/$TIMESTAMP/" > /dev/null 2>&1

if [ $? -eq 0 ]; then
    COUNT=$(find "$DEST/$TIMESTAMP" -type f | wc -l)
    echo "✓ Incremental backup complete ($COUNT files)"
else
    echo "✗ Incremental backup failed"
    exit 1
fi
EOF

chmod +x incremental_backup.sh
./incremental_backup.sh

# Expected output:
# ✓ Incremental backup complete (1523 files)
```

### Step 4: Backup Verification
```bash
cat > verify_backup.sh << 'EOF'
#!/bin/bash

BACKUP_FILE=$1

if [ ! -f "$BACKUP_FILE" ]; then
    echo "✗ Backup file not found"
    exit 1
fi

echo "Verifying: $BACKUP_FILE"

# Check file integrity
if tar -tzf "$BACKUP_FILE" > /dev/null 2>&1; then
    FILES=$(tar -tzf "$BACKUP_FILE" | wc -l)
    SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    echo "✓ Backup valid ($FILES files, $SIZE)"
    exit 0
else
    echo "✗ Backup corrupted"
    exit 1
fi
EOF

chmod +x verify_backup.sh
./verify_backup.sh /backup/files/backup_20260115_120530.tar.gz

# Expected output:
# Verifying: /backup/files/backup_20260115_120530.tar.gz
# ✓ Backup valid (245 files, 2.5M)
```

### Step 5: Restore from Backup
```bash
cat > restore_backup.sh << 'EOF'
#!/bin/bash

BACKUP_FILE=$1
RESTORE_PATH=${2:-./restored}

if [ ! -f "$BACKUP_FILE" ]; then
    echo "✗ Backup not found: $BACKUP_FILE"
    exit 1
fi

echo "Restoring from: $BACKUP_FILE"
mkdir -p "$RESTORE_PATH"

# Extract backup
tar -xzf "$BACKUP_FILE" -C "$RESTORE_PATH"

if [ $? -eq 0 ]; then
    FILES=$(find "$RESTORE_PATH" -type f | wc -l)
    echo "✓ Restored $FILES files to $RESTORE_PATH"
else
    echo "✗ Restore failed"
    exit 1
fi
EOF

chmod +x restore_backup.sh
./restore_backup.sh /backup/files/backup_20260115_120530.tar.gz /tmp/restored

# Expected output:
# Restoring from: /backup/files/backup_20260115_120530.tar.gz
# ✓ Restored 245 files to /tmp/restored
```

## Validation Checklist
- [ ] Full backup created successfully
- [ ] Database backup created and verified
- [ ] Incremental backup detects changes
- [ ] Backup verification confirms integrity
- [ ] Restore successfully recovers files
- [ ] Backup files compressed properly
- [ ] Backup schedule running correctly

## Cleanup
```bash
rm -f backup_files.sh backup_database.sh incremental_backup.sh verify_backup.sh restore_backup.sh
rm -rf /tmp/restored /backup/files
```

## Common Mistakes
1. **Not testing restores**: Always verify you can restore
2. **Storing backups locally**: Keep off-site copies
3. **No retention policy**: Backups accumulate, disk fills
4. **Unencrypted sensitive data**: Encrypt backups
5. **Forgetting database locks**: Lock DB during backup
6. **Wrong backup size**: Incremental vs full confusion

## Troubleshooting

### tar: cannot open file (permission denied)
- **Cause**: No read permissions on source
- **Solution**: Run with sudo or fix file permissions

### gzip: not found
- **Cause**: gzip not installed
- **Solution**: `sudo apt install gzip`

### rsync taking too long
- **Cause**: First run copies everything
- **Solution**: Normal for initial backup, subsequent runs are incremental

### Backup restore incomplete
- **Cause**: Interrupted or corrupted backup
- **Solution**: Verify backup with tar -tzf, restore from another copy

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [backup cheatsheet](cheatsheet.md)
3. Implement automated backup schedule
4. Test disaster recovery monthly
5. Consider 3-2-1 backup strategy (3 copies, 2 media, 1 offsite)
