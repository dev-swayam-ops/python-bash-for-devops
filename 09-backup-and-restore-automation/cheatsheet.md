# Backup and Restore Cheatsheet

## Backup Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `tar -czf` | Create compressed archive | `tar -czf backup.tar.gz /home` |
| `tar -tzf` | List files in tar.gz | `tar -tzf backup.tar.gz` |
| `tar -xzf` | Extract tar.gz | `tar -xzf backup.tar.gz -C /dest` |
| `gzip -l` | List gzip info | `gzip -l backup.tar.gz` |
| `rsync -avz` | Sync files incrementally | `rsync -avz /src /dst/` |
| `rsync --delete` | Remove deleted files | `rsync -avz --delete /src /dst` |
| `cp -r` | Recursive copy | `cp -r /source /backup` |
| `dd` | Block device copy | `dd if=/dev/sda of=disk.img` |

## Database Backups

| Command | Purpose | Example |
|---------|---------|---------|
| `mysqldump` | MySQL database dump | `mysqldump -u root dbname > backup.sql` |
| `mysqldump -A` | All MySQL databases | `mysqldump -u root -A > all_dbs.sql` |
| `mysql < backup.sql` | Restore MySQL | `mysql -u root dbname < backup.sql` |
| `pg_dump` | PostgreSQL dump | `pg_dump dbname > backup.sql` |
| `pg_restore` | PostgreSQL restore | `pg_restore -d dbname backup.dump` |
| `mongodump` | MongoDB backup | `mongodump -d dbname -o /backup` |

## Compression Formats

| Format | Command | Size | Speed |
|--------|---------|------|-------|
| gzip | `tar -czf` | Medium | Fast |
| bzip2 | `tar -cjf` | Small | Slow |
| xz | `tar -cJf` | Smallest | Slowest |
| zip | `zip -r` | Medium | Medium |
| 7z | `7z a` | Very small | Medium |

## File Size Check Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Archive size | Check backup size | `du -h backup.tar.gz` |
| Directory size | Total size | `du -sh /home` |
| File count | Files in archive | `tar -tzf backup.tar.gz \| wc -l` |
| Disk space | Free space | `df -h /backup` |
| inode usage | Inode count | `df -i /backup` |

## Restore Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `tar -xzf` | Extract tar.gz | `tar -xzf backup.tar.gz` |
| `tar -xzf -C` | Extract to path | `tar -xzf backup.tar.gz -C /dest` |
| `rsync -avz` | Sync restore | `rsync -avz /backup/src /restore` |
| `scp` | Remote restore | `scp backup.tar.gz user@host:` |
| `gunzip` | Decompress | `gunzip backup.tar.gz` |

## Encryption Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `gpg -c` | Symmetric encrypt | `gpg -c backup.tar.gz` |
| `gpg --decrypt` | Decrypt | `gpg --decrypt backup.tar.gz.gpg` |
| `openssl enc` | OpenSSL encrypt | `openssl enc -aes-256-cbc -in backup` |
| `openssl enc -d` | Decrypt | `openssl enc -aes-256-cbc -d -in backup.enc` |

## Verification Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `tar -tzf` | Test tar integrity | `tar -tzf backup.tar.gz > /dev/null` |
| `gzip -t` | Test gzip | `gzip -t backup.tar.gz` |
| `md5sum` | Generate hash | `md5sum backup.tar.gz > backup.md5` |
| `md5sum -c` | Verify hash | `md5sum -c backup.md5` |
| `sha256sum` | SHA256 hash | `sha256sum backup.tar.gz` |
| `diff` | Compare files | `diff -r /original /restored` |

## Scheduling Backups

| Method | Frequency | Example |
|--------|-----------|---------|
| Cron (daily) | 0 2 * * * | `0 2 * * * /backup.sh` |
| Cron (weekly) | 0 3 * * 0 | `0 3 * * 0 /full_backup.sh` |
| Cron (monthly) | 0 4 1 * * | `0 4 1 * * /archive.sh` |
| Systemd timer | Custom | `systemctl enable backup.timer` |
| At command | One-time | `at 2:00 AM tomorrow` |

## Backup Strategies

| Strategy | Full | Incremental | Storage | Speed |
|----------|------|-------------|---------|-------|
| Full only | Every time | No | High | Fast |
| Full + Daily Inc | Weekly | Daily | Medium | Medium |
| Full + Inc + Diff | Weekly | Daily + Diff | Lower | Faster |
| Grandfather | Monthly | Weekly, Daily | Low | Variable |
| 3-2-1 Rule | 3 copies, 2 media, 1 offsite | - | - | - |

## Retention Policies

| Type | Duration | Count | Example |
|------|----------|-------|---------|
| Daily | 7 days | 7 files | `mtime +7` |
| Weekly | 4 weeks | 4 files | `mtime +28` |
| Monthly | 12 months | 12 files | `mtime +365` |
| Yearly | 7 years | 7 files | `mtime +2555` |

## Backup Cleanup Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Age-based | Remove old | `find /backup -mtime +7 -delete` |
| Count-based | Keep N files | `ls -t \| tail -n +6 \| xargs rm` |
| Size-based | Limit space | `du -sh /backup > limit` |
| Pattern-based | Match name | `find /backup -name "*old*" -delete` |

## Performance Tips

1. **Exclude files**: `tar --exclude='.git' --exclude='*.log'`
2. **Compress level**: `tar -czf --zstd` (faster) or `-cJf` (smaller)
3. **Parallel compression**: `pigz` instead of gzip
4. **Remote backup**: Use `--bwlimit=1000` for rsync
5. **Incremental**: Use rsync for only changed files
6. **Full first**: Always start with full backup
7. **Verify early**: Test restore immediately after backup
8. **Schedule off-peak**: Run during low-traffic times

## Backup Testing Checklist

- [ ] Backup completes without errors
- [ ] Backup size is reasonable
- [ ] Backup can be extracted/verified
- [ ] Sample files restored correctly
- [ ] Database restore works
- [ ] Encryption/decryption works
- [ ] Off-site copy verified
- [ ] Restore time acceptable
- [ ] Old backups cleaned up
- [ ] Email report received

## Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Tar: permission denied | No read access | Run with sudo |
| Disk full | Backup too large | Compress more, cleanup old |
| Slow backup | Network/disk I/O | Run off-peak, use rsync |
| Restore incomplete | Corrupted backup | Verify with `tar -tzf` |
| Encryption password lost | Password-only backup | Use key management |
