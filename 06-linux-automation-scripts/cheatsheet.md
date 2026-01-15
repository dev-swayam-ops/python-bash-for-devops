# Linux Automation Scripts: Cheatsheet

## System Information Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `uname -a` | Full system info | `uname -a` |
| `uname -r` | Kernel version | `uname -r` |
| `hostname` | System hostname | `hostname` |
| `whoami` | Current user | `whoami` |
| `uptime` | System uptime | `uptime` |
| `uptime -p` | Pretty uptime | `uptime -p` |
| `lsb_release -a` | Linux distribution | `lsb_release -a` |

## User and Group Management

| Command | Purpose | Example |
|---------|---------|---------|
| `sudo useradd -m username` | Create user | With home directory |
| `sudo userdel -r username` | Delete user | Include home directory |
| `sudo usermod -aG group user` | Add user to group | Append to group |
| `id username` | Show user info | UID, GID, groups |
| `groups username` | List user groups | All groups of user |
| `sudo groupadd groupname` | Create group | New group |
| `sudo groupdel groupname` | Delete group | Remove group |

## File and Directory Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `ls -la` | List with details | Permissions, owner, date |
| `find / -name pattern` | Find files | By name |
| `find / -size +100M` | Find large files | Bigger than 100MB |
| `find / -mtime +30` | Find old files | Modified >30 days ago |
| `du -sh directory` | Directory size | Human readable |
| `du -sh *` | All subdirs size | Each subdir |
| `stat filename` | File details | Permissions, dates, size |
| `chmod 755 file` | Change permissions | Owner:rwx, group:rx, other:rx |

## Disk and Storage

| Command | Purpose | Example |
|---------|---------|---------|
| `df -h` | Disk usage | All partitions |
| `df -i` | Inode usage | Inode count |
| `du -sh /path` | Directory size | Total size |
| `lsblk` | Block devices | All disks |
| `mount` | Show mounts | All mounted filesystems |
| `mount /dev/sda1 /mnt` | Mount filesystem | Attach device |
| `umount /mnt` | Unmount | Detach filesystem |

## Processes and Memory

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | All processes | With full info |
| `ps aux --sort=-%mem` | Sorted by memory | Memory descending |
| `top` | Interactive monitor | Real-time stats |
| `top -b -n 1` | Batch mode | Single snapshot |
| `kill PID` | Terminate process | By process ID |
| `pkill pattern` | Kill by pattern | By name |
| `free -h` | Memory usage | Human readable |

## System Monitoring

| Command | Purpose | Example |
|---------|---------|---------|
| `uptime` | Load average | 1, 5, 15 min |
| `vmstat` | Virtual memory stats | Paging, IO |
| `iostat` | IO statistics | Disk activity |
| `sar` | System activity | Historical data |
| `netstat` | Network stats | Connections, ports |

## Services and Daemons

| Command | Purpose | Example |
|---------|---------|---------|
| `systemctl list-units` | List services | All units |
| `systemctl status service` | Service status | Current state |
| `systemctl start service` | Start service | Begin service |
| `systemctl stop service` | Stop service | End service |
| `systemctl restart service` | Restart service | Stop then start |
| `systemctl enable service` | Enable on boot | Auto-start |
| `systemctl disable service` | Disable on boot | Don't auto-start |
| `systemctl is-active service` | Check if running | Return 0/1 |

## Logs and Monitoring

| Command | Purpose | Example |
|---------|---------|---------|
| `journalctl` | System logs | Systemd logs |
| `journalctl -u service` | Service logs | Specific service |
| `journalctl -f` | Follow logs | Live tail |
| `tail -f /var/log/file` | Tail file | Follow output |
| `tail -n 100 /var/log/file` | Last N lines | Show end |
| `grep pattern /var/log/file` | Search logs | Find pattern |
| `dmesg` | Kernel logs | Boot and hardware |

## Network Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ip addr` | IP addresses | All interfaces |
| `ip route` | Routing table | Routes |
| `ping host` | Test connectivity | ICMP echo |
| `ss -tuln` | Network sockets | Listening ports |
| `netstat -tlnp` | Listening ports | With PID |
| `ifconfig` | Interface config | Deprecated (use ip) |
| `iwconfig` | Wireless config | WiFi interfaces |

## Package Management

| Command | Purpose | Example |
|---------|---------|---------|
| `apt update` | Update package list | Debian/Ubuntu |
| `apt install package` | Install package | Debian/Ubuntu |
| `apt remove package` | Remove package | Debian/Ubuntu |
| `apt list --installed` | Installed packages | Show list |
| `yum install package` | Install package | RedHat/CentOS |
| `yum remove package` | Remove package | RedHat/CentOS |
| `dpkg -l` | List packages | Debian |

## Permissions and Ownership

| Command | Purpose | Example |
|---------|---------|---------|
| `chmod 755 file` | Change mode | rwxr-xr-x |
| `chmod +x file` | Add execute | User execute |
| `chmod -R 755 dir` | Recursive chmod | All subdirs |
| `chown user:group file` | Change owner | User and group |
| `chown -R user dir` | Recursive chown | All subdirs |
| `umask 0022` | Set default perms | New files default |

## File Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `tar -czf archive.tar.gz dir` | Create archive | Compressed |
| `tar -tzf archive.tar.gz` | List contents | Show files |
| `tar -xzf archive.tar.gz` | Extract archive | Decompress |
| `gzip file` | Compress file | .gz format |
| `gunzip file.gz` | Decompress | Extract |
| `zip -r archive.zip dir` | Zip archive | ZIP format |
| `unzip archive.zip` | Extract zip | Unpack |

## Cron Jobs

| File | Purpose | Example |
|------|---------|---------|
| `crontab -e` | Edit user cron | Schedule tasks |
| `crontab -l` | List cron jobs | Show schedule |
| `crontab -r` | Remove cron | Delete all |
| `/etc/cron.d/` | System crons | System tasks |
| `/etc/cron.daily/` | Daily tasks | Automatic |

## Cron Schedule Format

```
Minute Hour Day Month DayOfWeek Command
  0-59  0-23 1-31  1-12    0-6
  
  0  12  *   *     * → Daily at noon
  0  0   *   *     0 → Weekly Sunday midnight
  */5 *   *   *     * → Every 5 minutes
  0  2   *   *     * → 2 AM daily
```

## Common Automation Patterns

### Check if Service Running
```bash
if systemctl is-active --quiet nginx; then
    echo "Nginx is running"
fi
```

### Safe Delete with Confirmation
```bash
rm -i file.txt  # Interactive prompt
```

### Backup Before Modification
```bash
cp file file.bak
# Make changes
# Restore if needed: cp file.bak file
```

### Redirect Errors to Log
```bash
command >> logfile.txt 2>&1
```

### Run Script with Timeout
```bash
timeout 30 ./long_running_script.sh
```

### Retry on Failure
```bash
retries=3
while [ $retries -gt 0 ]; do
    command && break
    retries=$((retries - 1))
done
```

## Best Practices

| Practice | Benefit |
|----------|---------|
| `set -euo pipefail` | Error on any failure |
| Use absolute paths | Avoid PATH issues |
| Check permissions | Avoid "Permission denied" |
| Create backups | Safe modifications |
| Use dry-run option | Verify before executing |
| Log operations | Audit trail |
| Error messages to stderr | Proper output routing |
| Exit codes | Proper scripting |
