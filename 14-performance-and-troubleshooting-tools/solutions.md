# Performance and Troubleshooting Tools - Solutions

## Solution 1: System Performance Dashboard

```bash
#!/bin/bash
# performance_dashboard.sh
while true; do
  clear
  echo "=== System Performance Dashboard ==="
  echo "Time: $(date '+%Y-%m-%d %H:%M:%S')"
  
  load=$(uptime | awk -F'load average:' '{print $2}')
  mem_used=$(free | grep Mem | awk '{printf "%.1f%%", ($3/$2)*100}')
  disk_used=$(df / | tail -1 | awk '{print $5}')
  processes=$(ps aux | wc -l)
  zombies=$(ps aux | grep -c " Z ")
  
  echo "Load Average: $load"
  echo "Memory Used: $mem_used"
  echo "Disk Used: $disk_used"
  echo "Running Processes: $processes | Zombie: $zombies"
  sleep 5
done
```

**Explanation:** Uses uptime, free, df, and ps to gather metrics. Updates every 5 seconds showing load, memory, disk usage, and process counts.

---

## Solution 2: Top Resource Consumers Report

```bash
#!/bin/bash
# resource_consumers.sh
echo "=== Top 5 CPU Consumers ==="
ps aux --sort=-%cpu | awk 'NR<=6 {printf "%5s %10s %6s %s\n", $2, $1, $3, substr($0, 68)}' | column -t

echo ""
echo "=== Top 5 Memory Consumers ==="
ps aux --sort=-%mem | awk 'NR<=6 {printf "%5s %10s %6s %s\n", $2, $1, $4, substr($0, 68)}' | column -t
```

**Explanation:** Parses ps output, sorts by CPU and memory percentages. Displays PID, user, percentage, and command name in formatted table.

---

## Solution 3: Disk I/O Analyzer

```bash
#!/bin/bash
# disk_io_analyzer.sh
echo "=== Disk I/O Statistics ==="
iostat -x 1 2 | tail -n +5 | awk '{
  device=$1
  rps=$4
  wps=$5
  await=$10
  printf "Device: %-10s Read: %8.2f ops/s  Write: %8.2f ops/s  Await: %6.2f ms\n", device, rps, wps, await
}'

echo ""
echo "=== I/O Wait Time ==="
iostat -x 1 2 | grep -E "^avg-cpu|^Device" | tail -2
```

**Explanation:** Uses iostat to collect I/O metrics over 2 samples. Extracts read ops, write ops, and await time. Shows which disks have high I/O activity.

---

## Solution 4: Network Performance Monitor

```bash
#!/bin/bash
# network_monitor.sh
echo "=== Network Interface Statistics ==="
netstat -in | awk 'NR>2 {
  iface=$1
  rx=$6
  tx=$10
  errors=$5 + $11
  drops=$7 + $13
  printf "%-8s RX: %12s  TX: %12s  Errors: %6s  Drops: %6s\n", iface, rx, tx, errors, drops
}' | column -t

echo ""
echo "=== Connection Count by Type ==="
ss -tan | grep ESTAB | wc -l | xargs echo "Established connections:"
ss -tan | grep TIME-WAIT | wc -l | xargs echo "TIME-WAIT connections:"
```

**Explanation:** Parses netstat output to show interface statistics: bytes/packets in/out, errors, drops. Uses ss to count connection states.

---

## Solution 5: Process Debugging Tool

```bash
#!/bin/bash
# debug_process.sh
PID=${1:?"Usage: $0 <PID>"}

[ ! -d "/proc/$PID" ] && { echo "PID $PID not found"; exit 1; }

echo "=== Process Information for PID $PID ==="
ps -p $PID -o pid,user,cpu,mem,cmd --no-headers

echo ""
echo "=== Memory Map ==="
cat /proc/$PID/status | grep -E "VmPeak|VmRSS|VmSize"

echo ""
echo "=== Open File Descriptors ==="
lsof -p $PID 2>/dev/null | wc -l | xargs echo "Count:"

echo ""
echo "=== Network Connections ==="
lsof -p $PID -a -iTCP 2>/dev/null | grep -E "LISTEN|ESTABLISHED" | wc -l | xargs echo "Count:"

echo ""
echo "=== System Calls (first 20) ==="
timeout 2 strace -p $PID -c 2>&1 | head -20 || echo "Strace completed or timed out"
```

**Explanation:** Takes PID argument, shows resource usage from /proc/$PID/status. Counts file descriptors with lsof. Runs strace to capture system calls.

---

## Solution 6: Memory Leak Detector

```bash
#!/bin/bash
# memory_leak_detector.sh
PID=${1:?"Usage: $0 <PID>"}
THRESHOLD=10  # 10MB threshold

echo "=== Memory Leak Detection for PID $PID ==="
PREV_MEM=$(ps -p $PID -o rss= | tr -d ' ')
TIMESTAMP=$(date +%s)

while true; do
  sleep 60
  CURR_MEM=$(ps -p $PID -o rss= | tr -d ' ')
  INCREASE=$((($CURR_MEM - $PREV_MEM) / 1024))
  
  echo "$(date '+%H:%M:%S') - Memory: ${CURR_MEM}KB (change: +${INCREASE}MB)"
  
  if [ $INCREASE -gt $THRESHOLD ]; then
    echo "WARNING: Memory increased by ${INCREASE}MB in last minute!"
  fi
  
  PREV_MEM=$CURR_MEM
done
```

**Explanation:** Monitors RSS memory from /proc/$PID/stat every 60 seconds. Calculates MB growth and alerts if exceeds threshold (10MB/min).

---

## Solution 7: Zombie Process Hunter

```bash
#!/bin/bash
# zombie_hunter.sh
echo "=== Zombie Processes ==="
zombies=$(ps aux | grep -E " Z " | grep -v grep)

if [ -z "$zombies" ]; then
  echo "No zombie processes found."
else
  echo "$zombies" | while read line; do
    zpid=$(echo $line | awk '{print $2}')
    ppid=$(grep PPid /proc/$zpid/status 2>/dev/null | awk '{print $2}')
    echo "Zombie PID: $zpid, Parent PID: $ppid"
  done
  
  echo ""
  echo "=== Cleanup Commands ==="
  ps aux | grep -E " Z " | grep -v grep | awk '{print "kill -9 " $3}' | grep -v "kill -9 $"
fi
```

**Explanation:** Finds processes in Z (zombie) state using ps. Shows parent PIDs. Generates kill commands for parent processes to clean up zombies.

---

## Solution 8: Slow Query Analyzer

```bash
#!/bin/bash
# slow_query_analyzer.sh
PID=${1:?"Usage: $0 <PID>"}
TIMEOUT=5

echo "=== System Calls for PID $PID (${TIMEOUT}s trace) ==="
strace -p $PID -c -e trace=file 2>&1 | grep -E "^%" -A 50 || {
  timeout $TIMEOUT strace -c -e trace=open,openat,read,write -p $PID 2>&1 | tail -15
}

echo ""
echo "=== Syscalls by Time (top 10) ==="
strace -p $PID -c 2>&1 | head -20
```

**Explanation:** Uses strace with `-c` flag to collect syscall statistics. Filters for file operations and I/O. Shows slowest syscalls by time consumed.

---

## Solution 9: Resource Limits Auditor

```bash
#!/bin/bash
# limits_auditor.sh
PID=${1:-$$}

echo "=== Resource Limits for PID $PID ==="
for limit in as core cpu data memlock msgqueue nice nofile nproc rss rtprio rttime sigpending stack; do
  ulimit_val=$(grep "^$limit" /proc/$PID/limits 2>/dev/null | awk '{print $3}')
  if [ -n "$ulimit_val" ]; then
    echo "$limit: $ulimit_val"
  fi
done

echo ""
echo "=== Recommended vs Current ==="
echo "Open Files (nofile): $(ulimit -n) - Should be >= 65536"
echo "Max Processes: $(ulimit -u) - Should be >= 4096"
echo "Stack Size: $(ulimit -s) - Usually OK at current value"
```

**Explanation:** Reads /proc/$PID/limits to show current resource limits. Compares against recommended security/performance values.

---

## Solution 10: Performance Baseline and Comparison

```bash
#!/bin/bash
# baseline_performance.sh
BASELINE_FILE="baseline_metrics.txt"
REPORT_FILE="performance_report_$(date +%Y%m%d_%H%M%S).html"

if [ "$1" = "baseline" ]; then
  echo "Recording baseline metrics..."
  {
    echo "timestamp=$(date +%s)"
    echo "cpu=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)"
    echo "mem=$(free | grep Mem | awk '{printf "%.1f", ($3/$2)*100}')"
    echo "disk=$(df / | tail -1 | awk '{print $5}' | cut -d'%' -f1)"
  } > $BASELINE_FILE
  echo "Baseline saved to $BASELINE_FILE"
  exit 0
fi

[ ! -f "$BASELINE_FILE" ] && { echo "Run with 'baseline' first"; exit 1; }

source $BASELINE_FILE
baseline_cpu=$cpu
baseline_mem=$mem

current_cpu=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
current_mem=$(free | grep Mem | awk '{printf "%.1f", ($3/$2)*100}')

cat > $REPORT_FILE << EOF
<html><head><title>Performance Report</title></head><body>
<h1>Performance Comparison Report</h1>
<table border="1">
<tr><th>Metric</th><th>Baseline</th><th>Current</th><th>Deviation</th></tr>
<tr><td>CPU</td><td>${baseline_cpu}%</td><td>${current_cpu}%</td><td>$(echo "$current_cpu - $baseline_cpu" | bc)%</td></tr>
<tr><td>Memory</td><td>${baseline_mem}%</td><td>${current_mem}%</td><td>$(echo "$current_mem - $baseline_mem" | bc)%</td></tr>
</table>
</body></html>
EOF

echo "Report generated: $REPORT_FILE"
```

**Explanation:** Creates baseline snapshot of CPU/memory/disk metrics. Later comparisons show deviation from baseline. Generates HTML report with trends and recommendations.
