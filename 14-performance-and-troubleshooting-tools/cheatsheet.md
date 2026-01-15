# Performance and Troubleshooting Tools - Cheatsheet

## CPU and Load Monitoring

| Command | Purpose | Example |
|---------|---------|---------|
| `top` | Interactive process viewer | `top -u username` |
| `top -b -n 1` | Non-interactive single shot | See all processes once |
| `htop` | Enhanced top (easier to use) | `htop -p PID` |
| `uptime` | Load average and uptime | Shows 1, 5, 15 min avg |
| `mpstat` | Per-CPU statistics | `mpstat -P ALL 1` |
| `sar -u` | CPU usage over time | `sar -u 1 5` |
| `ps aux --sort=-%cpu` | Processes by CPU usage | Top CPU consumers |

## Memory Monitoring

| Command | Purpose | Example |
|---------|---------|---------|
| `free -h` | Memory summary | Human-readable format |
| `free -t` | Total memory including swap | Show all memory types |
| `vmstat` | Virtual memory stats | `vmstat 1 5` |
| `ps aux --sort=-%mem` | Processes by memory | Top memory consumers |
| `smem` | Detailed memory usage | `smem -p PID` |
| `cat /proc/$PID/status` | Process memory details | Show VmRSS, VmSize |
| `pmap -x PID` | Process memory map | Show all memory regions |

## Disk I/O Performance

| Command | Purpose | Example |
|---------|---------|---------|
| `iostat -x 1 5` | Disk I/O stats | 5 samples, 1 second interval |
| `iotop` | Disk I/O by process | Real-time I/O activity |
| `df -h` | Disk space usage | Human-readable sizes |
| `du -sh /path` | Directory size | Total size of directory |
| `lsof -p PID` | Files opened by process | Check file descriptors |
| `fstab` | Mounted filesystems | Check mount points |
| `tune2fs -l /dev/sda1` | Filesystem info | Show ext4 details |

## Disk Usage Analysis

| Command | Purpose | Example |
|---------|---------|---------|
| `du -sh *` | Directory sizes in path | `cd /var && du -sh *` |
| `ncdu /path` | Interactive disk usage | Better than du |
| `find /path -size +100M` | Large files | Find files > 100MB |
| `df -i` | Inode usage | Check inode exhaustion |
| `stat /path` | File detailed info | Show permissions, size, time |

## Network Performance

| Command | Purpose | Example |
|---------|---------|---------|
| `netstat -in` | Interface statistics | Bytes in/out per interface |
| `ss -tln` | Listening ports | TCP listening sockets |
| `ss -tan` | TCP connections | Show established, time-wait |
| `iftop` | Bandwidth by connection | Real-time traffic |
| `nethogs` | Bandwidth by process | Which process uses network |
| `mtr host` | Traceroute + ping | Network path analysis |
| `iperf` | Bandwidth testing | Throughput between hosts |

## Process Monitoring and Debugging

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | All processes | Full process list |
| `ps -ef --forest` | Process tree | Parent-child relationships |
| `pgrep -u user` | Processes by user | Find user processes |
| `ps -p PID -L` | Threads of process | Show thread count |
| `top -p PID` | Monitor single process | Watch one PID |
| `watch 'ps aux | grep PID'` | Watch process | Monitor continuously |

## System Call Tracing

| Command | Purpose | Example |
|---------|---------|---------|
| `strace -p PID` | Trace system calls | See what process does |
| `strace -c -p PID` | Syscall statistics | Count syscalls by type |
| `strace -e open,read -p PID` | Specific syscalls | Filter by syscall name |
| `ltrace -p PID` | Library call trace | Track function calls |
| `strace -o trace.log -p PID` | Write to file | Log long traces |

## Open Files and Descriptors

| Command | Purpose | Example |
|---------|---------|---------|
| `lsof -p PID` | Open files/sockets | By process |
| `lsof -i` | Network connections | All active network |
| `lsof -i :8080` | Connections on port | Which process uses port |
| `lsof +D /path` | Open files in directory | Lock/busy files |
| `ls -l /proc/PID/fd` | File descriptors | Direct view |
| `ulimit -n` | Max open files | Show limit |

## Process States and Signals

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | Process state in column | See Z, S, R states |
| `kill -l` | List all signals | Available signals |
| `kill -9 PID` | SIGKILL (force) | Terminate immediately |
| `kill -15 PID` | SIGTERM (graceful) | Request shutdown |
| `killall name` | Kill by process name | All matching processes |
| `pkill -f pattern` | Kill by command pattern | Regex matching |

## System Performance Analysis

| Command | Purpose | Example |
|---------|---------|---------|
| `sar -u 1 10` | CPU usage history | 10 samples, 1 sec interval |
| `sar -r` | Memory usage history | Track memory over time |
| `sar -b` | I/O statistics | Block device activity |
| `sar -n DEV` | Network history | Per-interface statistics |
| `dstat` | Live system stats | Dashboard-style output |
| `sysstat` | System statistics | Comprehensive tool |

## Resource Limits

| Command | Purpose | Example |
|---------|---------|---------|
| `ulimit -a` | Show all limits | Current shell limits |
| `ulimit -n 65536` | Set max open files | Increase limit |
| `ulimit -u 4096` | Set max processes | Process limit |
| `cat /proc/sys/fs/file-max` | System file limit | Global max files |
| `prlimit --pid PID --nofile` | Process limits | Show PID's limits |
| `prlimit --pid PID --nofile=65536:65536` | Change PID limits | Adjust process limit |

## Troubleshooting Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `dmesg \| tail` | Kernel messages | Check for errors |
| `journalctl -xe` | Recent systemd errors | System journal |
| `journalctl -u service` | Service logs | Service-specific |
| `tail -f /var/log/syslog` | System log | Real-time log |
| `timeout 5 strace -p PID` | Timeout strace | Trace with limit |
| `ps auxww` | Full command display | Long command lines |

## Performance Tuning

| Parameter | Tool | Typical Value |
|-----------|------|----------------|
| I/O Scheduler | `cat /sys/block/sda/queue/scheduler` | noop, deadline, cfq |
| CPU Governor | `cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor` | performance, ondemand |
| Swappiness | `cat /proc/sys/vm/swappiness` | 10-20 (lower = less swap) |
| File Caching | `cat /proc/sys/vm/dirty_ratio` | 10-20 (% memory) |
| TCP Buffers | `cat /proc/sys/net/ipv4/tcp_rmem` | Size in bytes |

## Quick Diagnostics Script

```bash
#!/bin/bash
echo "=== Quick System Check ==="
echo "Uptime:" $(uptime | cut -d',' -f1)
echo "Load:" $(uptime | awk -F'load average:' '{print $2}')
echo "Memory:" $(free | grep Mem | awk '{printf "%.1f%%", ($3/$2)*100}')
echo "Disk:" $(df / | tail -1 | awk '{print $5}')
echo "Processes:" $(ps aux | wc -l)
echo "Zombies:" $(ps aux | grep -c " Z ")
```

## Process State Codes

| Code | Meaning | Example |
|------|---------|---------|
| R | Running | Active on CPU |
| S | Sleeping | Waiting for I/O |
| D | Disk Sleep | Uninterruptible I/O |
| Z | Zombie | Dead, waiting parent |
| T | Stopped | Paused by signal |
| W | Paging | Swapping to disk |

## Quick Tuning Commands

```bash
# Increase file descriptors
ulimit -n 65536

# Check CPU count
nproc

# Show core info
lscpu

# Memory details
cat /proc/meminfo

# Disk queue length
iostat -x | awk '{print $NF}' | tail -5
```
