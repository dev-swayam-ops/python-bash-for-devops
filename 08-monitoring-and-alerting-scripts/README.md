# Monitoring and Alerting Scripts for DevOps

## What You'll Learn
- System resource monitoring (CPU, memory, disk, network)
- Application health checks
- Creating alert conditions and thresholds
- Sending alerts via email, Slack, PagerDuty
- Building custom monitoring dashboards
- Log-based monitoring and anomaly detection
- Service availability checks
- Performance metric tracking

## Prerequisites
- Completed: [07-log-analysis-and-parsing](../07-log-analysis-and-parsing)
- Understanding of system metrics and thresholds
- Experience with shell scripting
- Basic networking knowledge

## Key Concepts
- **Metrics**: Quantifiable measurements (CPU%, memory, latency)
- **Thresholds**: Trigger points for alerts (e.g., CPU > 80%)
- **Alerting**: Actions taken when thresholds exceeded
- **Health Checks**: Verifying service availability
- **Anomaly Detection**: Finding unexpected patterns
- **SLA Monitoring**: Tracking service level agreements
- **Alerting Channels**: Methods to notify (email, chat, SMS)

## Hands-on Lab: Build a Monitoring System

### Step 1: CPU Usage Monitor
```bash
cat > cpu_monitor.sh << 'EOF'
#!/bin/bash

THRESHOLD=${1:-80}
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print int($2)}')

echo "CPU Usage: ${CPU_USAGE}%"

if [ "$CPU_USAGE" -gt "$THRESHOLD" ]; then
    echo "⚠ ALERT: CPU usage exceeds ${THRESHOLD}%"
    echo "Alert timestamp: $(date)"
    exit 1
else
    echo "✓ CPU usage normal"
    exit 0
fi
EOF

chmod +x cpu_monitor.sh
./cpu_monitor.sh 80

# Expected output:
# CPU Usage: 45%
# ✓ CPU usage normal
```

### Step 2: Memory Check
```bash
cat > memory_monitor.sh << 'EOF'
#!/bin/bash

THRESHOLD=${1:-85}
MEM_USAGE=$(free | awk 'NR==2 {printf "%.0f", $3/$2*100}')

echo "Memory Usage: ${MEM_USAGE}%"

if [ "$MEM_USAGE" -gt "$THRESHOLD" ]; then
    echo "⚠ ALERT: Memory usage at ${MEM_USAGE}%"
    exit 1
else
    echo "✓ Memory usage normal"
    exit 0
fi
EOF

chmod +x memory_monitor.sh
./memory_monitor.sh 85

# Expected output:
# Memory Usage: 62%
# ✓ Memory usage normal
```

### Step 3: Service Health Check
```bash
cat > service_health_check.sh << 'EOF'
#!/bin/bash

check_service() {
    local service=$1
    if systemctl is-active --quiet "$service"; then
        echo "✓ $service: running"
        return 0
    else
        echo "✗ $service: STOPPED"
        return 1
    fi
}

echo "=== Service Health Check ==="
failed=0

for service in ssh nginx postgres; do
    if ! check_service "$service"; then
        ((failed++))
    fi
done

if [ $failed -gt 0 ]; then
    echo "⚠ $failed service(s) down"
    exit 1
else
    echo "✓ All services running"
    exit 0
fi
EOF

chmod +x service_health_check.sh
./service_health_check.sh

# Expected output:
# === Service Health Check ===
# ✓ ssh: running
# ✗ nginx: STOPPED
# ⚠ 1 service(s) down
```

### Step 4: Disk Space Alert
```bash
cat > disk_monitor.sh << 'EOF'
#!/bin/bash

THRESHOLD=${1:-80}
echo "=== Disk Space Monitor ==="

alert_count=0
df -h | tail -n +2 | while read line; do
    usage=$(echo "$line" | awk '{print $5}' | sed 's/%//')
    mount=$(echo "$line" | awk '{print $6}')
    
    if [ "$usage" -gt "$THRESHOLD" ]; then
        echo "⚠ ALERT: $mount is ${usage}% full"
        ((alert_count++))
    else
        echo "✓ $mount: ${usage}%"
    fi
done
EOF

chmod +x disk_monitor.sh
./disk_monitor.sh 80

# Expected output:
# === Disk Space Monitor ===
# ✓ /: 45%
# ✓ /home: 32%
# ⚠ ALERT: /var is 82% full
```

### Step 5: Send Email Alert
```bash
cat > send_alert.sh << 'EOF'
#!/bin/bash

send_email_alert() {
    local subject=$1
    local body=$2
    local to=${3:-admin@example.com}
    
    # Using mail command (requires mailutils)
    echo "$body" | mail -s "$subject" "$to"
    echo "✓ Alert sent to: $to"
}

# Test alert
send_email_alert "System Alert" "CPU usage exceeded threshold" "test@example.com"

# Or use simple echo for demo
echo "Alert: System CPU exceeds 80%" > /tmp/alert.txt
echo "Alert logged to /tmp/alert.txt"
EOF

chmod +x send_alert.sh
./send_alert.sh

# Expected output:
# Alert logged to /tmp/alert.txt
```

## Validation Checklist
- [ ] CPU monitor detects high usage correctly
- [ ] Memory check calculates percentage accurately
- [ ] Service health check identifies stopped services
- [ ] Disk monitor shows capacity correctly
- [ ] Alerts trigger at correct thresholds
- [ ] Email alert mechanism works (or simulated)

## Cleanup
```bash
rm -f cpu_monitor.sh memory_monitor.sh service_health_check.sh disk_monitor.sh send_alert.sh
```

## Common Mistakes
1. **Hardcoded thresholds**: Always make thresholds configurable
2. **No alert history**: Don't repeat identical alerts too quickly
3. **Ignoring false positives**: Add cooldown periods between alerts
4. **No retry logic**: Transient failures shouldn't trigger alerts
5. **Missing context**: Include relevant details in alert messages

## Troubleshooting

### Alert not sending via email
- **Cause**: Mail not configured or mailutils not installed
- **Solution**: Install mailutils: `sudo apt install mailutils`

### Memory calculation incorrect
- **Cause**: Wrong calculation or parsing
- **Solution**: Test with `free -h` then verify math

### Service check fails with permission denied
- **Cause**: Sudo required or wrong service name
- **Solution**: Check service exists: `systemctl list-units --type=service`

### False positives on transient metrics
- **Cause**: Single spike triggers alert
- **Solution**: Add averaging: check 3 times before alerting

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [monitoring cheatsheet](cheatsheet.md)
3. Integrate with Prometheus, Grafana, or ELK stack
4. Set up PagerDuty or Slack notifications
