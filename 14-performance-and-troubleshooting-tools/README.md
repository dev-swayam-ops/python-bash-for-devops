# Performance and Troubleshooting Tools

## What You'll Learn

- Monitor CPU, memory, disk, and network performance
- Identify bottlenecks and slow processes
- Troubleshoot system hangs and failures
- Analyze application performance
- Debug process issues (zombie processes, file descriptors)
- Optimize resource utilization

## Prerequisites

- Basic Linux command line (modules 01-02)
- Understanding of processes and system resources
- Familiarity with top, ps, and basic monitoring
- Optional: Knowledge of profiling tools

## Key Concepts

### Performance Monitoring
- Real-time system metrics (CPU, memory, I/O)
- Process-level resource usage
- Network performance analysis
- Disk I/O profiling

### Troubleshooting
- Process debugging and analysis
- Memory leak detection
- Zombie process cleanup
- Hung process recovery
- System crash diagnosis

### Optimization
- Load balancing
- Cache efficiency
- I/O optimization
- Resource limits tuning

## Hands-on Lab

### Step 1: Monitor System Performance
```bash
#!/bin/bash
echo "=== CPU and Memory Usage ==="
top -bn1 | head -15

echo "=== Memory Breakdown ==="
free -h

echo "=== Disk Usage ==="
df -h | grep -E "Filesystem|/$"
```

**Expected Output:**
```
=== CPU and Memory Usage ===
top - 10:30:45 up 5 days,  2:15,  1 user,  load average: 0.50, 0.45, 0.40
Tasks:  127 total,   1 running, 126 sleeping
...
=== Memory Breakdown ===
              total        used        free
Mem:           7.7G        3.2G        4.5G
=== Disk Usage ===
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        20G  5.2G   14G  27% /
```

### Step 2: Identify Resource Hogs
```bash
echo "=== Top CPU Consuming Processes ==="
ps aux --sort=-%cpu | head -6

echo "=== Top Memory Consuming Processes ==="
ps aux --sort=-%mem | head -6
```

### Step 3: Monitor Network Performance
```bash
echo "=== Network Statistics ==="
netstat -in | head -5

echo "=== Network Connections ==="
ss -tln | grep LISTEN | wc -l
```

### Step 4: Check Disk I/O
```bash
echo "=== Disk I/O Activity ==="
iostat -x 1 2 | head -10
```

### Step 5: Debug Slow Process
```bash
#!/bin/bash
PID=$1
echo "=== Process Details for PID $PID ==="
ps -p $PID -o pid,cmd,vsz,rss
strace -c -p $PID &
sleep 2
kill %1 2>/dev/null
```

## Validation

- [ ] System metrics display correctly
- [ ] CPU/memory hogs identified
- [ ] Network activity visible
- [ ] Disk I/O statistics available
- [ ] Process debugging works
- [ ] No permission errors

## Cleanup

```bash
pkill -f strace 2>/dev/null || true
pkill -f top 2>/dev/null || true
```

## Common Mistakes

- Not running monitoring with sufficient privileges
- Killing critical processes (always verify first)
- Ignoring system vs user time
- Not accounting for swap usage
- Using wrong tools for specific metrics

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Tools not found | Install sysstat: `apt install sysstat` |
| Permission denied | Use `sudo` for system-level monitoring |
| Load spike, can't find cause | Check `iotop` for disk I/O |
| Process unresponsive | Use `strace -p PID` to see what it's doing |
| Memory keeps growing | Check `smem` for memory leaks |

## Next Steps

- Set up continuous monitoring (Prometheus, Grafana)
- Learn advanced profiling (perf, flamegraph)
- Implement automated alerting on thresholds
- Create baseline performance metrics
- Document troubleshooting procedures
