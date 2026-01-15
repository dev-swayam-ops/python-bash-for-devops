# Performance and Troubleshooting Tools - Exercises

## Exercise 1: System Performance Dashboard
Create a script that displays current CPU load, memory usage percentage, disk usage, and count of running processes in a formatted dashboard.

**Requirements:**
- Show load average, used/total memory, disk usage %
- Display running vs zombie process count
- Update every 5 seconds
- Format as readable table

## Exercise 2: Top Resource Consumers Report
Write a script that lists top 5 processes consuming CPU and top 5 consuming memory, with PID, process name, and percentage.

**Requirements:**
- Parse ps aux output
- Sort by CPU and memory separately
- Show percentage of system resources
- Format output clearly

## Exercise 3: Disk I/O Analyzer
Create a script that monitors disk I/O activity, showing read/write rates and I/O wait percentage for each disk.

**Requirements:**
- Use iostat command
- Extract read/write IOPS
- Calculate wait time percentage
- Identify high-I/O disks

## Exercise 4: Network Performance Monitor
Write a script that tracks network interface statistics: bytes in/out, packets in/out, errors, and drops per interface.

**Requirements:**
- Parse netstat or ss output
- Show interface-wise breakdown
- Calculate throughput
- Identify problem interfaces

## Exercise 5: Process Debugging Tool
Create a script that takes a PID and shows: CPU%, memory%, open file descriptors, network connections, and recent system calls using strace.

**Requirements:**
- Accept PID as argument
- Show process resource usage
- Count open file descriptors (lsof)
- Capture syscall trace
- Handle invalid PIDs gracefully

## Exercise 6: Memory Leak Detector
Write a script that monitors a process over time and tracks memory growth, alerting if memory increases by more than threshold (e.g., 10MB per minute).

**Requirements:**
- Monitor memory usage periodically
- Calculate growth rate
- Alert on significant increases
- Generate report with timestamps
- Show memory trend

## Exercise 7: Zombie Process Hunter
Create a script that finds and reports all zombie processes, their parent PID, and attempts to clean them up by killing parents.

**Requirements:**
- Identify zombie processes (Z state)
- Show parent process info
- Warn before killing parents
- Provide cleanup commands
- Log actions

## Exercise 8: Slow Query Analyzer
Write a script that identifies slow system calls, processes taking > 1 second to run, and I/O operations that cause delays.

**Requirements:**
- Use strace to trace execution
- Measure syscall duration
- Identify blocking operations
- Show slowest calls
- Provide optimization hints

## Exercise 9: Resource Limits Auditor
Create a script that checks current limits (ulimits) for a process: open files, memory, CPU time, and compares against recommended values.

**Requirements:**
- Show current limits for running processes
- Compare against security/performance recommendations
- Identify undersized limits
- Provide adjustment commands
- Support PID argument

## Exercise 10: Performance Baseline and Comparison
Write a comprehensive script that records baseline metrics (CPU, memory, disk, network), then periodically compares current metrics against baseline and generates a performance report with deviations and recommendations.

**Requirements:**
- Record initial baseline snapshot
- Compare current vs baseline periodically
- Calculate percentage deviation
- Generate HTML report
- Provide optimization recommendations
- Support trending data
