# Monitoring and Alerting Cheatsheet

## System Metrics Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `top -bn1` | Single-shot CPU/memory snapshot | `top -bn1 \| grep "Cpu(s)"` |
| `free -h` | Memory usage (human readable) | `free -h \| awk 'NR==2 {print $3}'` |
| `df -h` | Disk usage all partitions | `df -h \| tail -n +2` |
| `du -sh <dir>` | Directory size | `du -sh /var/log` |
| `ps aux` | All running processes | `ps aux \| grep nginx` |
| `systemctl status <svc>` | Service status | `systemctl status nginx` |
| `uptime` | System uptime & load avg | `uptime` |
| `iostat -x 1 5` | Disk I/O stats | `iostat -x 1 5` |
| `vmstat 1 5` | Virtual memory stats | `vmstat 1 5` |
| `netstat -an` | Network connections | `netstat -an \| grep ESTABLISHED` |

## CPU Monitoring Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Extract CPU % | Get CPU usage | `top -bn1 \| grep "Cpu(s)" \| awk '{print $2}'` |
| CPU threshold check | Alert if high | `[ $cpu -gt 80 ] && echo "Alert"` |
| Per-core usage | All CPU cores | `mpstat -P ALL 1 1` |
| Top CPU processes | 5 highest CPU procs | `ps aux --sort=-%cpu \| head -6` |
| Load average | Current load | `cat /proc/loadavg` |

## Memory Monitoring Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Total memory % | Overall memory use | `free \| awk 'NR==2 {printf "%.0f", $3/$2*100}'` |
| Available memory | Free memory | `free -h \| awk 'NR==2 {print $7}'` |
| Top memory processes | 5 highest mem procs | `ps aux --sort=-%mem \| head -6` |
| Memory trend | Compare 2 samples | `mem1=$(free \| awk 'NR==2 {print $3}'); sleep 60; mem2=...` |
| Swap usage | Swap space in use | `free \| awk 'NR==3 {print $3/$2*100}'` |

## Disk Monitoring Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Partition usage % | Disk fullness | `df -h / \| tail -1 \| awk '{print $5}' \| sed 's/%//'` |
| Find large files | Files consuming space | `find / -type f -size +100M 2>/dev/null` |
| Inode usage | Inode exhaustion | `df -i / \| tail -1` |
| Mount point check | All filesystems | `df -h \| awk 'NR>1 {print $6":"$5}'` |
| Disk free space | Available space | `df -h / \| tail -1 \| awk '{print $4}'` |

## Service Monitoring Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Service running | Check if active | `systemctl is-active nginx` |
| Service enabled | Starts on boot | `systemctl is-enabled nginx` |
| Service restart count | Recent restarts | `journalctl -u nginx \| grep -c "Started"` |
| Service uptime | How long running | `systemctl show nginx -p ExecMainStartTimestamp` |
| Failed units | Services down | `systemctl list-units --failed` |
| Service logs | Recent errors | `journalctl -u nginx -n 50 -p err` |

## Alerting Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Email alert | Send notification | `echo "Alert" \| mail -s "Subject" admin@example.com` |
| Slack webhook | Post to Slack | `curl -X POST -d '{"text":"Alert"}' $SLACK_WEBHOOK` |
| JSON alert | Structured format | `echo "{\"metric\":\"cpu\",\"value\":85}" \| tee alert.json` |
| Threshold check | Conditional alert | `[ $value -gt $threshold ] && send_alert` |
| Escalation | Multi-level alerts | `[ $attempts -gt 3 ] && escalate_alert` |
| Alert dedup | Prevent duplicates | `last_alert=$(cat /tmp/last_alert); [ $now -lt $((last_alert+300)) ] && exit` |

## Monitoring Frequencies

| Frequency | Use Case | Command |
|-----------|----------|---------|
| Every 30s | CPU/memory spikes | `while true; do check; sleep 30; done` |
| Every 5 min | Disk usage | `*/5 * * * * /usr/local/bin/disk_monitor.sh` |
| Every 1 hour | Backup check | `0 * * * * /usr/local/bin/backup_check.sh` |
| Every 1 day | Log cleanup | `0 0 * * * /usr/local/bin/log_cleanup.sh` |
| Real-time | Critical service | `systemctl enable monitoring.service` |

## Monitoring Tool Comparison

| Tool | Strength | Weakness | Setup |
|------|----------|----------|-------|
| Custom bash | Simple, no deps | Limited analytics | Native |
| Prometheus | Metrics, graphs | Complex | Docker/service |
| Grafana | Beautiful dashboards | Requires data source | Docker |
| New Relic | Cloud, easy setup | Expensive, vendor lock | Agent |
| Datadog | Features, integrations | Very expensive | Agent |
| ELK Stack | Log analysis | Complexity, resources | Docker |
| Zabbix | Enterprise, flexible | Steep learning curve | Service |

## Alert Severity Levels

| Level | Condition | Action | Example |
|-------|-----------|--------|---------|
| INFO | Status change | Log only | "Service started" |
| WARNING | Threshold 80% | Notify ops | CPU at 82% |
| CRITICAL | Threshold 90%+ | Page oncall | Memory at 95% |
| EMERGENCY | Service down | Escalate | No process, restart |

## Performance Monitoring Tips

1. **Baseline First**: Establish normal metrics before alerting
2. **Avoid Alert Fatigue**: Set realistic thresholds
3. **Multi-point Check**: Verify with 2-3 samples before alerting
4. **Cooldown Period**: Don't repeat same alert < 300 seconds
5. **Include Context**: Show metric name, value, threshold, timestamp
6. **Test Alerts**: Verify notification channels monthly
7. **Log Everything**: Keep detailed audit trail for post-mortems
8. **Automate Recovery**: Self-heal where safe (restart, restart container)
9. **Monitor Monitors**: Ensure monitoring system itself is healthy
10. **Rate Limiting**: Cap alert frequency per service/metric

## Quick Alert Script Template

```bash
#!/bin/bash
# Generic alert framework

check_metric() {
    local metric=$1
    local value=$2
    local warn=$3
    local crit=$4
    
    if [ "$value" -gt "$crit" ]; then
        send_alert "CRITICAL: $metric=$value" "critical"
    elif [ "$value" -gt "$warn" ]; then
        send_alert "WARNING: $metric=$value" "warning"
    fi
}

send_alert() {
    local msg=$1
    local severity=$2
    
    # Add to alert log
    echo "[$(date)] [$severity] $msg" >> /var/log/alerts.log
    
    # Send notification
    [ "$severity" = "critical" ] && \
        mail -s "$msg" admin@example.com
}

# Usage
cpu=$(top -bn1 | grep "Cpu(s)" | awk '{print int($2)}')
check_metric "CPU" "$cpu" 75 85
```

## Database Monitoring Quick Check

```bash
# MySQL: active connections
mysql -e "SHOW PROCESSLIST;" | tail -1

# MySQL: slow queries
mysql -e "SHOW STATUS LIKE 'Slow_queries';"

# PostgreSQL: active sessions
psql -c "SELECT count(*) FROM pg_stat_activity;"

# PostgreSQL: replication lag
psql -c "SELECT now() - pg_last_xact_replay_timestamp();"
```

## Cron Integration

```bash
# Monitor every 5 minutes
*/5 * * * * /usr/local/bin/health_check.sh

# Monitor weekday business hours
0 8-17 * * 1-5 /usr/local/bin/intensive_check.sh

# Monitor every minute for critical service
* * * * * /usr/local/bin/critical_check.sh

# Daily summary report
0 9 * * * /usr/local/bin/generate_report.sh
```

## Common Monitoring Commands

```bash
# Top 5 CPU consumers
ps aux --sort=-%cpu | head -6

# Top 5 Memory consumers
ps aux --sort=-%mem | head -6

# Count process by name
pgrep -c nginx

# Check if port in use
netstat -antp | grep :8080

# Watch metric in real-time
watch -n 2 'free -h | grep Mem'

# Get all metrics in one view
glances  # Or install: apt install glances
```

## Alert Channel Setup

### Email Setup
```bash
# Install mailutils
sudo apt install mailutils

# Test send
echo "Test" | mail -s "Test Subject" user@example.com
```

### Slack Integration
```bash
# Send to Slack webhook
curl -X POST -H 'Content-type: application/json' \
  --data '{"text":"Alert: CPU high"}' \
  https://hooks.slack.com/services/YOUR/WEBHOOK/URL
```

### PagerDuty Integration
```bash
# Send event to PagerDuty
curl -X POST https://events.pagerduty.com/v2/enqueue \
  -H 'Content-Type: application/json' \
  -d '{
    "routing_key": "YOUR_KEY",
    "event_action": "trigger",
    "payload": {
      "summary": "High CPU",
      "severity": "critical",
      "source": "monitoring"
    }
  }'
```

---

**Pro Tip**: Combine monitoring scripts with systemd timers for more reliable scheduling than cron on modern Linux systems.
