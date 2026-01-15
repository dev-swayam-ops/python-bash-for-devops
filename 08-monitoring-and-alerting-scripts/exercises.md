# Monitoring and Alerting Exercises

## Easy Exercises (1-4)

### Exercise 1: CPU Usage Monitor Script
Write a script that checks CPU usage and prints:
- Current CPU percentage
- Status message (green if < 70%, yellow if 70-85%, red if > 85%)
- Recommendation based on usage

**Hint**: Use `top -bn1` to get CPU info

**Requirements**:
- Accept optional threshold parameter (default 70)
- Return exit code 0 if below threshold, 1 if above
- Show clean formatted output

### Exercise 2: Memory Leak Detector
Create a script that:
- Checks memory usage every 5 seconds for 30 seconds
- Tracks if memory is increasing consistently
- Alerts if memory grows > 10% in that period

**Hint**: Use `free -h` and capture values in a loop

**Requirements**:
- Record 6 measurements
- Calculate percentage change
- Detect upward trend

### Exercise 3: Disk Space Alert
Write a script that:
- Checks all mounted filesystems
- Lists partitions with > 75% usage
- Shows available space for critical filesystems

**Hint**: Parse `df -h` output

**Requirements**:
- Filter for > 75% usage
- Show all problematic partitions
- Include available space in MB/GB

### Exercise 4: Service Status Checker
Create a script that:
- Accepts service name as argument
- Reports if service is running
- Shows service start time if running

**Hint**: Use `systemctl status <service>`

**Requirements**:
- Validate service exists
- Handle service not found gracefully
- Show both status and start time

## Medium Exercises (5-10)

### Exercise 5: Multi-Metric Health Dashboard
Build a script that shows:
- CPU usage with trend (↑/→/↓)
- Memory usage with available RAM
- Disk usage for top 3 full partitions
- Network status (if network up)
- Timestamp and overall health status

**Hint**: Combine multiple monitoring commands

**Requirements**:
- Show all 5 metrics on one screen
- Calculate trends (compare to 2 minutes ago)
- Color coding for health status
- Overall status: OK/WARNING/CRITICAL

### Exercise 6: Application Health Check
Write a monitoring script for a web application that:
- Checks if application process is running
- Tests HTTP endpoint with timeout (e.g., curl localhost:8000)
- Checks application log for recent errors (last 100 lines)
- Records check timestamp and status in CSV

**Hint**: Use `pgrep`, `curl`, and log parsing

**Requirements**:
- Append results to CSV with timestamp
- Include process ID if running
- Count errors in recent logs
- Handle network timeouts gracefully

### Exercise 7: Threshold-based Alert Trigger
Create a monitoring script that:
- Reads configuration from a file (threshold values)
- Monitors 3 metrics (CPU, memory, disk)
- Generates alerts in JSON format with: metric, value, threshold, timestamp
- Saves alerts to a log file with date-based naming

**Hint**: Use case statements for metrics

**Requirements**:
- Config file: metric=threshold format
- JSON output for each alert
- Separate log files by date
- Include alert severity (WARNING/CRITICAL)

### Exercise 8: Multi-System Monitoring Network
Write a script that:
- Takes list of remote servers (IPs or hostnames)
- SSH into each server to check CPU, memory, disk
- Compiles results into a summary report
- Fails gracefully if server unreachable

**Hint**: Use `ssh` with timeout, loop through server list

**Requirements**:
- Accept server list from command line or file
- Parallel checks (background jobs) for faster execution
- Summary table of all servers
- Mark unreachable servers as OFFLINE

### Exercise 9: Database Health Monitoring
Build a script that monitors a database (MySQL/PostgreSQL):
- Connection count and max connections
- Query performance (slow query count)
- Disk usage for DB files
- Replication lag (if replication active)
- Backup age (hours since last backup)

**Hint**: Use DB-specific commands (`mysql -e`, `psql -c`)

**Requirements**:
- Connect to specified host/port
- Handle connection failures
- Show all 5 metrics
- Flag if backup > 24 hours old

### Exercise 10: Self-Healing Monitoring System
Create an advanced monitoring script that:
- Detects service failures
- Attempts automatic restart (up to 3 times)
- Logs all restart attempts with timestamps
- If restart fails, sends alert and escalates
- Tracks restart history for analysis

**Hint**: Use `systemctl restart` and retry logic with delays

**Requirements**:
- Max 3 restart attempts
- Wait 5 seconds between retries
- Log format: timestamp, service, attempt, status
- Escalation alert after final failure
- Don't restart if service marked as "do-not-auto-restart"
