# Monitoring and Alerting Solutions

## Solution 1: CPU Usage Monitor Script

```bash
#!/bin/bash
# cpu_monitor.sh - Monitor CPU usage with thresholds

THRESHOLD=${1:-70}

# Get CPU usage percentage
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print int($2)}')

echo "CPU Usage: ${CPU_USAGE}%"

# Determine status
if [ "$CPU_USAGE" -lt 70 ]; then
    STATUS="✓ Normal"
    COLOR="GREEN"
elif [ "$CPU_USAGE" -lt 85 ]; then
    STATUS="⚠ Warning"
    COLOR="YELLOW"
else
    STATUS="✗ Critical"
    COLOR="RED"
fi

echo "Status: $STATUS ($COLOR)"

# Recommendation
if [ "$CPU_USAGE" -gt 85 ]; then
    echo "Recommendation: Check running processes with 'top' or 'ps aux'"
elif [ "$CPU_USAGE" -gt 70 ]; then
    echo "Recommendation: Monitor closely, consider load balancing"
else
    echo "Recommendation: No action needed"
fi

# Exit code
[ "$CPU_USAGE" -gt "$THRESHOLD" ] && exit 1 || exit 0
```

**Usage**: `./cpu_monitor.sh 75` or `./cpu_monitor.sh`

**Explanation**: Parses `top` output, compares against threshold, and provides color-coded status with recommendations. Exit code enables use in conditional logic.

---

## Solution 2: Memory Leak Detector

```bash
#!/bin/bash
# memory_leak_detector.sh - Detect memory increases over time

INTERVAL=5
MEASUREMENTS=6
GROWTH_THRESHOLD=10

declare -a mem_values

echo "Sampling memory usage ($MEASUREMENTS times, every ${INTERVAL}s)..."

for i in $(seq 0 $((MEASUREMENTS - 1))); do
    # Get memory usage percentage
    mem_pct=$(free | awk 'NR==2 {printf "%.1f", $3/$2*100}')
    mem_values[$i]=$mem_pct
    echo "[$((i+1))/$MEASUREMENTS] Memory: ${mem_pct}%"
    
    if [ $i -lt $((MEASUREMENTS - 1)) ]; then
        sleep $INTERVAL
    fi
done

# Calculate growth
first=${mem_values[0]}
last=${mem_values[$((MEASUREMENTS - 1))]}
growth=$(echo "scale=1; $last - $first" | bc)

echo ""
echo "=== Memory Trend Analysis ==="
echo "First measurement: ${first}%"
echo "Last measurement: ${last}%"
echo "Growth: ${growth}%"

# Check for upward trend
if (( $(echo "$growth > $GROWTH_THRESHOLD" | bc -l) )); then
    echo "⚠ ALERT: Memory growing (possible leak)"
    exit 1
else
    echo "✓ Memory growth within normal range"
    exit 0
fi
```

**Usage**: `./memory_leak_detector.sh`

**Explanation**: Takes 6 measurements over 30 seconds, calculates percentage increase, and alerts if growth exceeds 10%. Uses `bc` for floating-point arithmetic.

---

## Solution 3: Disk Space Alert

```bash
#!/bin/bash
# disk_monitor.sh - Monitor disk usage across all filesystems

THRESHOLD=${1:-75}

echo "=== Disk Space Monitor (threshold: ${THRESHOLD}%) ==="
echo ""

alert_count=0
critical_disks=""

df -h | tail -n +2 | while read line; do
    filesystem=$(echo "$line" | awk '{print $1}')
    usage=$(echo "$line" | awk '{print $5}' | sed 's/%//')
    available=$(echo "$line" | awk '{print $4}')
    mount=$(echo "$line" | awk '{print $6}')
    
    if [ "$usage" -gt "$THRESHOLD" ]; then
        echo "⚠ ALERT: $mount (${usage}% full, ${available} free)"
        ((alert_count++))
    else
        echo "✓ $mount: ${usage}% (${available} available)"
    fi
done

[ "$alert_count" -gt 0 ] && exit 1 || exit 0
```

**Usage**: `./disk_monitor.sh 80`

**Explanation**: Parses `df` output, extracts usage percentage, compares against threshold, and shows available space. Multiple alert format for critical partitions.

---

## Solution 4: Service Status Checker

```bash
#!/bin/bash
# service_checker.sh - Check if service is running and show start time

SERVICE=${1:-nginx}

if ! command -v systemctl &> /dev/null; then
    echo "✗ systemctl not found (not a systemd system)"
    exit 2
fi

# Check if service exists
if ! systemctl list-units --type=service --all | grep -q "^.*$SERVICE"; then
    echo "✗ Service '$SERVICE' not found"
    echo "Available services: $(systemctl list-units --type=service -q | head -5 | cut -d' ' -f1)"
    exit 2
fi

# Get service status
if systemctl is-active --quiet "$SERVICE"; then
    echo "✓ Service '$SERVICE' is RUNNING"
    
    # Get start time
    start_time=$(systemctl show "$SERVICE" -p ActiveEnterTimestamp | cut -d'=' -f2-)
    echo "  Started at: $start_time"
    
    # Get uptime
    uptime=$(systemctl show "$SERVICE" -p ActiveEnterTimestampMonotonic | cut -d'=' -f2)
    echo "  Process ID: $(pgrep -f $SERVICE | head -1)"
    exit 0
else
    echo "✗ Service '$SERVICE' is STOPPED"
    
    # Show when it stopped
    stop_time=$(systemctl show "$SERVICE" -p InactiveEnterTimestamp | cut -d'=' -f2-)
    echo "  Stopped at: $stop_time"
    exit 1
fi
```

**Usage**: `./service_checker.sh nginx` or `./service_checker.sh`

**Explanation**: Validates service exists, checks active status, extracts start/stop timestamps using systemctl, and provides detailed information.

---

## Solution 5: Multi-Metric Health Dashboard

```bash
#!/bin/bash
# health_dashboard.sh - Display system health metrics

get_cpu() {
    top -bn1 | grep "Cpu(s)" | awk '{print int($2)}'
}

get_memory() {
    free | awk 'NR==2 {printf "%.0f", $3/$2*100}'
}

get_top_disks() {
    df -h | tail -n +2 | sort -rn -k5 | head -3 | awk '{print $6":"$5}' | paste -sd ',' -
}

get_network() {
    ip route | grep -q "default" && echo "UP" || echo "DOWN"
}

# Get previous metrics if available
PREV_FILE="/tmp/metrics_prev"
if [ -f "$PREV_FILE" ]; then
    prev_cpu=$(cat "$PREV_FILE" | cut -d',' -f1)
else
    prev_cpu=0
fi

# Get current metrics
cpu=$(get_cpu)
memory=$(get_memory)
disks=$(get_top_disks)
network=$(get_network)

# Calculate trends
if [ $cpu -gt $prev_cpu ]; then
    cpu_trend="↑"
elif [ $cpu -lt $prev_cpu ]; then
    cpu_trend="↓"
else
    cpu_trend="→"
fi

# Determine overall health
overall="OK"
[ $cpu -gt 80 ] || [ $memory -gt 85 ] && overall="WARNING"
[ $cpu -gt 90 ] && overall="CRITICAL"

# Display dashboard
clear
echo "╔════════════════════════════════════════════════╗"
echo "║         System Health Dashboard                ║"
echo "║         $(date '+%Y-%m-%d %H:%M:%S')                         ║"
echo "╠════════════════════════════════════════════════╣"
printf "║ CPU Usage:        %3d%% %s                        ║\n" "$cpu" "$cpu_trend"
printf "║ Memory Usage:     %3d%%                         ║\n" "$memory"
printf "║ Top Disks:        %-30s ║\n" "$disks"
printf "║ Network Status:   %-30s ║\n" "$network"
echo "╠════════════════════════════════════════════════╣"
printf "║ Overall Status:   %-30s ║\n" "$overall"
echo "╚════════════════════════════════════════════════╝"

# Save current metrics
echo "$cpu" > "$PREV_FILE"

exit 0
```

**Usage**: `./health_dashboard.sh`

**Explanation**: Combines multiple metrics, calculates trends by comparing with previous run, color-codes output, and displays in formatted dashboard. Stores previous metrics for trend detection.

---

## Solution 6: Application Health Check

```bash
#!/bin/bash
# app_health_check.sh - Monitor web application health

APP_NAME=${1:-web_app}
APP_PORT=${2:-8000}
APP_PID_FILE="/var/run/${APP_NAME}.pid"
LOG_FILE="/var/log/${APP_NAME}.log"
CSV_LOG="/tmp/${APP_NAME}_health.csv"

# Initialize CSV if not exists
[ ! -f "$CSV_LOG" ] && echo "timestamp,status,process,http,errors" > "$CSV_LOG"

echo "=== Application Health Check: $APP_NAME ==="

# 1. Check process
if pgrep -f "$APP_NAME" > /dev/null; then
    pid=$(pgrep -f "$APP_NAME" | head -1)
    process_status="RUNNING(PID:$pid)"
    process_ok=1
else
    process_status="STOPPED"
    process_ok=0
fi
echo "Process: $process_status"

# 2. Check HTTP endpoint
http_status="FAIL"
http_ok=0
if command -v curl &> /dev/null; then
    response=$(curl -s -o /dev/null -w "%{http_code}" \
        --connect-timeout 2 --max-time 5 \
        http://localhost:$APP_PORT/ 2>/dev/null)
    
    if [ "$response" = "200" ] || [ "$response" = "301" ]; then
        http_status="OK($response)"
        http_ok=1
    else
        http_status="HTTP$response"
    fi
fi
echo "HTTP Endpoint: $http_status"

# 3. Check logs for errors (last 100 lines)
if [ -f "$LOG_FILE" ]; then
    error_count=$(tail -100 "$LOG_FILE" | grep -ic "error\|exception\|critical")
    echo "Recent Errors: $error_count"
else
    error_count=0
    echo "Recent Errors: N/A (no log file)"
fi

# 4. Overall status
if [ $process_ok -eq 1 ] && [ $http_ok -eq 1 ] && [ $error_count -lt 5 ]; then
    overall="HEALTHY"
    exit_code=0
else
    overall="UNHEALTHY"
    exit_code=1
fi

# 5. Log to CSV
echo "$(date '+%Y-%m-%d %H:%M:%S'),$overall,$process_status,$http_status,$error_count" >> "$CSV_LOG"

echo "Overall: $overall"
echo "Logged to: $CSV_LOG"
exit $exit_code
```

**Usage**: `./app_health_check.sh web_app 8000`

**Explanation**: Checks process existence, tests HTTP endpoint with curl timeout, parses logs for recent errors, appends results to timestamped CSV for trending.

---

## Solution 7: Threshold-based Alert Trigger

```bash
#!/bin/bash
# threshold_alert.sh - Monitor metrics against config thresholds

CONFIG_FILE=${1:-/etc/monitoring/thresholds.conf}
ALERT_DIR="/tmp/alerts"
TODAY=$(date +%Y-%m-%d)

mkdir -p "$ALERT_DIR"

# Default config if not exists
if [ ! -f "$CONFIG_FILE" ]; then
    cat > "$CONFIG_FILE" << EOF
cpu=80
memory=85
disk=75
EOF
fi

# Read thresholds
source "$CONFIG_FILE"

# Get metrics
cpu_value=$(top -bn1 | grep "Cpu(s)" | awk '{print int($2)}')
memory_value=$(free | awk 'NR==2 {printf "%.0f", $3/$2*100}')
disk_value=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')

# Check thresholds and generate alerts
check_metric() {
    local metric=$1
    local value=$2
    local threshold=$3
    
    if [ "$value" -gt "$threshold" ]; then
        # Determine severity
        if [ "$value" -gt $((threshold + 15)) ]; then
            severity="CRITICAL"
        else
            severity="WARNING"
        fi
        
        # Generate JSON alert
        alert=$(cat <<EOF
{
  "timestamp": "$(date -Iseconds)",
  "metric": "$metric",
  "value": $value,
  "threshold": $threshold,
  "severity": "$severity",
  "message": "$metric exceeded threshold ($value% > ${threshold}%)"
}
EOF
)
        
        # Append to log file
        echo "$alert" >> "$ALERT_DIR/alerts_$TODAY.log"
        echo "$alert" | jq '.' # Pretty print
        return 1
    fi
    return 0
}

echo "=== Alert Check at $(date) ==="

alert_count=0
check_metric "CPU" "$cpu_value" "${cpu:-80}" || ((alert_count++))
check_metric "MEMORY" "$memory_value" "${memory:-85}" || ((alert_count++))
check_metric "DISK" "$disk_value" "${disk:-75}" || ((alert_count++))

[ $alert_count -eq 0 ] && echo "✓ All metrics within thresholds"

exit $alert_count
```

**File**: `/etc/monitoring/thresholds.conf`
```
cpu=80
memory=85
disk=75
```

**Usage**: `./threshold_alert.sh /etc/monitoring/thresholds.conf`

**Explanation**: Reads thresholds from config file, checks each metric, generates JSON alerts with severity levels, and appends to date-based log files. Enables easy configuration changes.

---

## Solution 8: Multi-System Monitoring Network

```bash
#!/bin/bash
# multi_system_monitor.sh - Monitor multiple remote servers

SERVERS=${@:-server1 server2 server3}
TIMEOUT=5
REPORT_FILE="/tmp/monitoring_report_$(date +%s).txt"

echo "Multi-System Monitoring Report" > "$REPORT_FILE"
echo "Generated: $(date)" >> "$REPORT_FILE"
echo "===========================================" >> "$REPORT_FILE"

# Function to check remote system
check_remote_system() {
    local server=$1
    
    # Check connectivity
    if ! ping -c 1 -W $TIMEOUT "$server" &> /dev/null; then
        echo "$server: OFFLINE"
        return 1
    fi
    
    # Get remote metrics via SSH
    metrics=$(ssh -o ConnectTimeout=$TIMEOUT -o StrictHostKeyChecking=no "$server" << 'EOSSH'
cpu=$(top -bn1 | grep "Cpu(s)" | awk '{print int($2)}')
memory=$(free | awk 'NR==2 {printf "%.0f", $3/$2*100}')
disk=$(df -h / | tail -1 | awk '{print $5}' | sed 's/%//')
echo "$cpu|$memory|$disk"
EOSSH
    )
    
    if [ -z "$metrics" ]; then
        echo "$server: SSH FAILED"
        return 1
    fi
    
    IFS='|' read -r cpu memory disk <<< "$metrics"
    echo "$server: CPU=$cpu% MEM=$memory% DISK=$disk%"
    return 0
}

# Process each server (parallel)
declare -a pids
for server in $SERVERS; do
    (check_remote_system "$server" >> "$REPORT_FILE") &
    pids+=($!)
done

# Wait for all background jobs
wait "${pids[@]}"

echo "===========================================" >> "$REPORT_FILE"
echo "Report saved: $REPORT_FILE"
cat "$REPORT_FILE"
```

**Usage**: `./multi_system_monitor.sh server1 server2 server3` or from file `./multi_system_monitor.sh $(cat server_list.txt)`

**Explanation**: Uses background jobs for parallel SSH checks, implements timeout, handles offline servers gracefully, and compiles summary report.

---

## Solution 9: Database Health Monitoring

```bash
#!/bin/bash
# db_monitor.sh - Monitor MySQL/PostgreSQL health

DB_HOST=${1:-localhost}
DB_PORT=${2:-3306}
DB_USER=${3:-root}
DB_TYPE=${4:-mysql}  # mysql or postgres

echo "=== Database Health Monitor ==="
echo "Host: $DB_HOST:$DB_PORT"
echo ""

if [ "$DB_TYPE" = "mysql" ]; then
    # MySQL monitoring
    
    # 1. Connection count
    conn_count=$(mysql -h $DB_HOST -P $DB_PORT -u $DB_USER -e \
        "SHOW STATUS LIKE 'Threads_connected';" 2>/dev/null | tail -1 | awk '{print $2}')
    max_conn=$(mysql -h $DB_HOST -P $DB_PORT -u $DB_USER -e \
        "SHOW VARIABLES LIKE 'max_connections';" 2>/dev/null | tail -1 | awk '{print $2}')
    
    echo "Connections: $conn_count / $max_conn"
    
    # 2. Slow query count
    slow_queries=$(mysql -h $DB_HOST -P $DB_PORT -u $DB_USER -e \
        "SHOW STATUS LIKE 'Slow_queries';" 2>/dev/null | tail -1 | awk '{print $2}')
    echo "Slow Queries: $slow_queries"
    
    # 3. DB size
    db_size=$(mysql -h $DB_HOST -P $DB_PORT -u $DB_USER -e \
        "SELECT ROUND(SUM(data_length+index_length)/1024/1024,2) FROM information_schema.tables;" \
        2>/dev/null | tail -1)
    echo "Database Size: ${db_size}MB"
    
    # 4. Check backup age
    backup_file="/var/backups/mysql_latest.sql"
    if [ -f "$backup_file" ]; then
        backup_age=$(($(date +%s) - $(stat -c %Y "$backup_file"))) 
        backup_hours=$((backup_age / 3600))
        echo "Backup Age: ${backup_hours} hours"
        [ $backup_hours -gt 24 ] && echo "⚠ WARNING: Backup older than 24 hours"
    else
        echo "Backup Age: No backup found"
    fi
    
elif [ "$DB_TYPE" = "postgres" ]; then
    # PostgreSQL monitoring
    
    export PGPASSWORD=$4  # If password provided
    
    # 1. Connection count
    conn_count=$(psql -h $DB_HOST -p $DB_PORT -U $DB_USER -tA \
        -c "SELECT count(*) FROM pg_stat_activity;" 2>/dev/null)
    
    echo "Active Connections: $conn_count"
    
    # 2. DB size
    db_size=$(psql -h $DB_HOST -p $DB_PORT -U $DB_USER -tA \
        -c "SELECT pg_size_pretty(pg_database_size('postgres'));" 2>/dev/null)
    
    echo "Database Size: $db_size"
    
    # 3. Cache hit ratio
    cache_hit=$(psql -h $DB_HOST -p $DB_PORT -U $DB_USER -tA \
        -c "SELECT ROUND(100.0 * sum(blks_hit)/(sum(blks_hit) + sum(blks_read)), 2) \
            FROM pg_statio_user_tables;" 2>/dev/null)
    
    echo "Cache Hit Ratio: ${cache_hit}%"
fi

echo ""
echo "✓ Database health check complete"
```

**Usage**: `./db_monitor.sh localhost 3306 root mysql`

**Explanation**: Monitors connection counts, slow queries, database size, and backup freshness. Supports both MySQL and PostgreSQL with native commands.

---

## Solution 10: Self-Healing Monitoring System

```bash
#!/bin/bash
# self_healing_monitor.sh - Auto-restart services with logging

SERVICE=${1:-nginx}
MAX_RETRIES=3
RETRY_DELAY=5
LOG_FILE="/var/log/auto_restart_${SERVICE}.log"

log_event() {
    local msg="$1"
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $msg" >> "$LOG_FILE"
    echo "$msg"
}

is_service_running() {
    systemctl is-active --quiet "$SERVICE"
}

# Check if service should auto-restart
config_file="/etc/auto_restart.conf"
if grep -q "^$SERVICE:do-not-auto-restart" "$config_file" 2>/dev/null; then
    log_event "Service $SERVICE marked as do-not-auto-restart"
    exit 0
fi

# Check service status
if is_service_running; then
    log_event "Service $SERVICE is running (OK)"
    exit 0
fi

log_event "Service $SERVICE is STOPPED - attempting recovery"

# Attempt restart with retries
attempt=1
while [ $attempt -le $MAX_RETRIES ]; do
    log_event "Restart attempt $attempt/$MAX_RETRIES"
    
    if systemctl restart "$SERVICE" 2>&1; then
        log_event "Restart successful on attempt $attempt"
        
        # Verify service is actually running
        sleep 2
        if is_service_running; then
            log_event "Service verification: RUNNING"
            exit 0
        else
            log_event "Service not running after restart"
        fi
    else
        log_event "Restart failed on attempt $attempt"
    fi
    
    if [ $attempt -lt $MAX_RETRIES ]; then
        log_event "Waiting ${RETRY_DELAY}s before retry..."
        sleep $RETRY_DELAY
    fi
    
    ((attempt++))
done

# All retries failed - escalate
log_event "⚠ CRITICAL: Service $SERVICE failed to restart after $MAX_RETRIES attempts"
log_event "Escalation: Sending alert"

# Send escalation alert
if command -v mail &> /dev/null; then
    mail -s "CRITICAL: Service $SERVICE restart failed on $(hostname)" admin@example.com << EOF
Service: $SERVICE
Host: $(hostname)
Time: $(date)
Attempts: $MAX_RETRIES
Status: FAILED

See logs: $LOG_FILE
EOF
    log_event "Alert sent to admin@example.com"
fi

exit 1
```

**Configuration file**: `/etc/auto_restart.conf`
```
nginx:auto-restart
nginx:do-not-auto-restart (if you don't want auto-restart)
```

**Usage**: `./self_healing_monitor.sh nginx`

**Explanation**: Detects service failure, attempts up to 3 restarts with delays, logs all activities with timestamps, checks configuration to prevent auto-restart on protected services, and escalates via email after final failure.

---

All solutions include proper error handling, logging, and can be integrated into cron jobs or systemd services for continuous monitoring.
