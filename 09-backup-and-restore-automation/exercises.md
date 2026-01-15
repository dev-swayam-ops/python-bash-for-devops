# Backup and Restore Exercises

## Easy Exercises (1-4)

### Exercise 1: Simple File Backup
Create a script that:
- Accepts a directory path as argument
- Creates a tar.gz backup with timestamp
- Saves to `/tmp/backups/`
- Reports backup size

**Hint**: Use `tar -czf` and `date +%Y%m%d_%H%M%S`

**Requirements**:
- Validate source directory exists
- Create backup directory if missing
- Show file count in backup

### Exercise 2: Backup Size Reporter
Write a script that:
- Lists all backups in `/tmp/backups/`
- Shows size of each backup
- Calculates total backup size
- Shows oldest and newest backup

**Hint**: Use `du -h`, `ls -lrt`, `date`

**Requirements**:
- Format output clearly
- Handle no backups case
- Sort by date

### Exercise 3: Restore from Backup
Create a script that:
- Takes backup filename and restore path as arguments
- Extracts tar.gz backup to destination
- Validates extraction succeeded
- Shows file count restored

**Hint**: Use `tar -xzf`

**Requirements**:
- Check backup file exists
- Create restore path if needed
- Verify file count matches

### Exercise 4: Log File Backup
Write a script that:
- Backs up system logs from `/var/log/`
- Compresses with gzip
- Keeps only last 5 backups
- Deletes old ones

**Hint**: Use `tar -czf` and `ls -t | tail -n +6`

**Requirements**:
- Exclude large files if needed
- Auto-cleanup old backups
- Show deleted files

## Medium Exercises (5-10)

### Exercise 5: Incremental Backup with rsync
Build a script that:
- Uses rsync for incremental backup
- Syncs `/home/user/data/` to backup location
- Tracks backup date
- Shows files added/deleted/changed

**Hint**: Use `rsync -avz --delete` with backup dirs

**Requirements**:
- Create dated directories for each backup
- Show summary of changes
- Handle symlinks properly

### Exercise 6: Database Backup with Verification
Create a script that:
- Backs up MySQL/PostgreSQL database
- Compresses dump file
- Verifies backup integrity
- Logs backup to CSV

**Hint**: Use `mysqldump` or `pg_dump`

**Requirements**:
- Accept database name/host as args
- Verify compressed size is reasonable
- Log timestamp, size, status

### Exercise 7: Rotation Policy with Cleanup
Write a script that:
- Keeps daily backups for 7 days
- Weekly backups for 4 weeks
- Monthly backups for 12 months
- Automatically deletes older backups

**Hint**: Use find with -mtime and date calculations

**Requirements**:
- Define retention policy clearly
- Show deleted files
- Prevent deleting protected backups

### Exercise 8: Encrypted Backup System
Build a script that:
- Creates backup of sensitive data
- Encrypts with GPG or openssl
- Stores encryption key separately
- Provides restore with decryption

**Hint**: Use `gpg -c` or `openssl enc`

**Requirements**:
- Password protection
- Verify encrypted file size
- Test decrypt/restore flow

### Exercise 9: Multi-Host Backup Aggregator
Create a script that:
- SSHs to multiple servers
- Triggers backup on each
- Collects backups to central location
- Generates backup manifest

**Hint**: Use SSH, loop through server list

**Requirements**:
- Handle server unreachable gracefully
- Track backup status per server
- Create manifest file with checksums

### Exercise 10: Complete Backup Strategy with Testing
Build comprehensive script that:
- Full backup weekly (Sunday)
- Incremental backup daily (Mon-Sat)
- Automatically verifies all backups
- Tests monthly restore on test system
- Sends report to admin email
- Auto-cleanup based on retention policy

**Hint**: Combine all previous concepts

**Requirements**:
- Cron-schedulable
- Includes logging
- Email notification
- Dry-run testing capability
- Off-site storage consideration
