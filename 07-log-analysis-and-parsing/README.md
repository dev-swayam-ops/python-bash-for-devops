# Log Analysis and Parsing for DevOps

## What You'll Learn
- Parsing and analyzing application and system logs
- Using grep, sed, awk for log extraction
- Filtering logs by patterns, dates, and severity
- Extracting metrics from log files
- Creating log summaries and reports
- Identifying errors and anomalies
- Log aggregation basics
- Real-time log monitoring

## Prerequisites
- Completed: [06-linux-automation-scripts](../06-linux-automation-scripts)
- Familiar with grep, sed, awk
- Basic understanding of log formats (Apache, Nginx, syslog)
- Linux shell scripting knowledge

## Key Concepts
- **Log Formats**: Standardized output from applications (JSON, CSV, syslog)
- **Log Levels**: Severity levels (DEBUG, INFO, WARN, ERROR, CRITICAL)
- **Parsing**: Extracting structured data from unstructured logs
- **Filtering**: Selecting specific log entries based on criteria
- **Aggregation**: Combining logs from multiple sources
- **Metrics**: Extracting statistics (counts, latency, errors)
- **Alerting**: Triggering actions on detected patterns

## Hands-on Lab: Build a Log Analysis Tool

### Step 1: Create Sample Log File
```bash
cat > app.log << 'EOF'
2024-01-15 10:30:45 INFO Starting application v1.0.0
2024-01-15 10:30:46 INFO Connected to database
2024-01-15 10:30:47 ERROR Connection timeout: db-server
2024-01-15 10:31:00 WARN Memory usage high: 85%
2024-01-15 10:31:05 INFO Request processed: 234ms
2024-01-15 10:31:06 ERROR Unexpected error in handler
2024-01-15 10:31:07 INFO Request processed: 156ms
2024-01-15 10:31:08 CRITICAL Service unavailable
EOF

cat app.log
```

### Step 2: Basic Log Filtering
```bash
# Count by severity
echo "=== Error Count ==="
grep -c "ERROR" app.log

# Show only errors
echo "=== Errors ==="
grep "ERROR" app.log

# Show INFO and above
echo "=== INFO and above ==="
grep -E "INFO|WARN|ERROR|CRITICAL" app.log

# Expected output:
# === Error Count ===
# 2
# === Errors ===
# 2024-01-15 10:30:47 ERROR Connection timeout...
# 2024-01-15 10:31:06 ERROR Unexpected error...
```

### Step 3: Extract Specific Fields
```bash
# Get timestamps and messages
echo "=== Timestamps and Messages ==="
awk '{print $1, $2, $NF}' app.log

# Get only errors with time
echo "=== Errors with Time ==="
awk '/ERROR/ {print $1, $2, $0}' app.log

# Extract response times
echo "=== Response Times ==="
grep "processed:" app.log | awk '{print $(NF-1)}'

# Expected output:
# === Response Times ===
# 234ms
# 156ms
```

### Step 4: Create Summary Report
```bash
cat > log_summary.sh << 'EOF'
#!/bin/bash
LOG_FILE=${1:-app.log}

echo "=== Log Summary Report ==="
echo "File: $LOG_FILE"
echo "Total lines: $(wc -l < "$LOG_FILE")"
echo ""

echo "By Severity:"
awk '{print $3}' "$LOG_FILE" | sort | uniq -c

echo ""
echo "Response Time Stats:"
grep "processed:" "$LOG_FILE" | \
    awk '{n+=$NF; total++} END {
        if (total > 0) 
            printf "Average: %.0fms\n", n/total
    }' | sed 's/[^0-9]//g'
EOF

chmod +x log_summary.sh
./log_summary.sh app.log

# Expected output:
# === Log Summary Report ===
# File: app.log
# Total lines: 8
# 
# By Severity:
#   1 CRITICAL
#   2 ERROR
#   3 INFO
#   1 WARN
```

### Step 5: Filter by Date Range
```bash
# Logs from specific time
echo "=== Logs after 10:31 ==="
awk -F: '$2 >= "31"' app.log

# Extract hour for analysis
echo "=== Hour Distribution ==="
awk '{print $2}' app.log | cut -d: -f1-2 | sort | uniq -c

# Expected output:
# === Hour Distribution ===
#   8 10:30
#   1 10:31
```

## Validation Checklist
- [ ] Created and parsed sample log file
- [ ] Filtered logs by severity level
- [ ] Extracted specific fields successfully
- [ ] Generated summary report
- [ ] Filtered by date/time range
- [ ] Calculated metrics from logs

## Cleanup
```bash
rm -f app.log log_summary.sh
```

## Common Mistakes
1. **Not escaping special characters in grep**: Use `\|` for OR in basic grep
2. **Wrong field separators in awk**: Default is space, need `-F` for others
3. **Assuming log format consistency**: Real logs often have variable formats
4. **Not handling large files**: Use `tail`, `head` or `wc` carefully on huge logs
5. **Case-sensitive matching**: Use `grep -i` for case-insensitive search

## Troubleshooting

### "No such file or directory" when parsing
- **Cause**: Wrong log file path
- **Solution**: Use absolute path or check file exists: `test -f file && echo ok`

### awk field extraction returns wrong data
- **Cause**: Wrong field separator or field number
- **Solution**: Test with `head -1 file | awk -F: '{print NF}'` to count fields

### Performance slow on large logs
- **Cause**: Processing entire file repeatedly
- **Solution**: Use `grep` first to filter, then pipe to other tools

### Timestamps not sorting correctly
- **Cause**: String sort instead of numeric
- **Solution**: Use `sort -k3` with proper format or ISO 8601 dates

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [log parsing cheatsheet](cheatsheet.md)
3. Move to [08-monitoring-and-alerting-scripts](../08-monitoring-and-alerting-scripts)
