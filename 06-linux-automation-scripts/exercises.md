# Linux Automation Scripts: Exercises

## Exercise 1 (Easy): System Information Collector
Create a script that gathers and displays system info: hostname, uptime, kernel version, CPU count.

**Hint**: Use `uname`, `uptime`, `hostname`, `/proc/cpuinfo`

---

## Exercise 2 (Easy): List Users and Groups
Write a script that displays all system users with UID > 1000 and their primary groups.

**Hint**: Parse `/etc/passwd` with `awk` or `cut`

---

## Exercise 3 (Easy): Disk Space Alert
Create a script that checks disk usage and alerts if any partition exceeds 80% capacity.

**Hint**: Use `df -h` and parse percentage column

---

## Exercise 4 (Easy): Active Process Monitor
Write a script that lists top 5 processes by memory usage with their PIDs and memory percentage.

**Hint**: Use `ps aux` and `sort` by memory column

---

## Exercise 5 (Medium): Find Large Files
Create a script that finds all files larger than a specified size (in MB) in a directory tree.

**Hint**: Use `find -size` with appropriate size format

---

## Exercise 6 (Medium): Log Cleaner
Write a script that finds and removes log files older than N days, with dry-run option and logging.

**Hint**: Use `find -mtime` and `rm` with confirmation

---

## Exercise 7 (Medium): Service Manager
Create a script with functions to start, stop, restart, and check status of multiple services.

**Hint**: Use `systemctl` with conditional checks

---

## Exercise 8 (Medium): Security Audit
Write a script that checks for insecure permissions on critical files (/etc/passwd, /etc/shadow, SSH keys).

**Hint**: Use `stat` or `ls -l` to check permissions

---

## Exercise 9 (Medium): Batch File Processor
Create a script that processes multiple files: counts lines, lists largest files, reports total size.

**Hint**: Loop through files with `for`, use `wc -l`, `du`

---

## Exercise 10 (Medium): Automated Cleanup
Write a comprehensive cleanup script: old temp files, cache, logs older than N days, with rollback capability.

**Hint**: Create file manifest before deletion, allow restore from manifest
