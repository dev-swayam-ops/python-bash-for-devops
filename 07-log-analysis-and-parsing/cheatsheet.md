# Log Analysis and Parsing: Cheatsheet

## Grep - Search Patterns

| Command | Purpose | Example |
|---------|---------|---------|
| `grep pattern file` | Find pattern | `grep "ERROR" app.log` |
| `grep -i pattern file` | Case-insensitive | `grep -i "error" app.log` |
| `grep -c pattern file` | Count matches | `grep -c "ERROR" app.log` |
| `grep -n pattern file` | Show line numbers | `grep -n "ERROR" app.log` |
| `grep -v pattern file` | Invert (exclude) | `grep -v "DEBUG" app.log` |
| `grep -E "pattern1\|pattern2"` | Multiple patterns | `grep -E "ERROR\|WARN" app.log` |
| `grep "^pattern"` | Start of line | `grep "^2024-01" app.log` |
| `grep "pattern$"` | End of line | `grep "failed$" app.log` |
| `grep -l pattern *` | Files matching | Show filenames only |
| `grep -A 2 pattern` | 2 lines after | Context after match |
| `grep -B 2 pattern` | 2 lines before | Context before match |

## Awk - Field Processing

| Command | Purpose | Example |
|---------|---------|---------|
| `awk '{print}' file` | Print all | `awk '{print}' app.log` |
| `awk '{print $1}' file` | First field | `awk '{print $1}' app.log` |
| `awk '{print $1,$3}' file` | Multiple fields | `awk '{print $1,$3}' app.log` |
| `awk -F: '{print $1}' file` | Change separator | Colon separator |
| `awk '$3 == "ERROR"' file` | Filter by field | Exact match |
| `awk '$2 > "10:30"' file` | Range filter | Time comparison |
| `awk 'NR > 100' file` | Line number filter | Skip first 100 |
| `awk '{sum+=$3} END {print sum}' file` | Sum field | Total calculation |
| `awk 'NF > 0' file` | Non-empty lines | Remove blank lines |
| `awk -v var=value '{}'` | Pass variables | Dynamic values |

## Sed - Text Replacement

| Command | Purpose | Example |
|---------|---------|---------|
| `sed 's/old/new/' file` | Replace first | `sed 's/ERROR/ALERT/' log` |
| `sed 's/old/new/g' file` | Replace all | `sed 's/ERROR/ALERT/g' log` |
| `sed '10d' file` | Delete line 10 | Remove specific line |
| `sed '10,20d' file` | Delete lines 10-20 | Range delete |
| `sed -n '10p' file` | Print line 10 | Show only one line |
| `sed 's/^/#/' file` | Add prefix | Comment out lines |
| `sed 's/$/;/' file` | Add suffix | Append to end |

## Cut - Extract Columns

| Command | Purpose | Example |
|---------|---------|---------|
| `cut -d: -f1 file` | Field with delimiter | Extract first colon-field |
| `cut -c1-10 file` | Character range | First 10 characters |
| `cut -f1-3 file` | Tab-separated fields | Fields 1-3 |
| `cut -d' ' -f2 file` | Space delimiter | Second field |

## Sort and Uniq - Deduplicate

| Command | Purpose | Example |
|---------|---------|---------|
| `sort file` | Sort lines | Alphabetical |
| `sort -n file` | Numeric sort | Number order |
| `sort -rn file` | Reverse numeric | Descending numbers |
| `sort -u file` | Sort unique | Remove duplicates |
| `uniq file` | Remove consecutive dupes | Requires pre-sort |
| `uniq -c file` | Count occurrences | Show frequency |
| `sort \| uniq -c \| sort -rn` | Frequency analysis | Most common first |

## Wc - Count

| Command | Purpose | Example |
|---------|---------|---------|
| `wc -l file` | Count lines | Line count |
| `wc -w file` | Count words | Word count |
| `wc -c file` | Count bytes | Byte count |
| `wc -L file` | Longest line | Max line length |

## Head and Tail

| Command | Purpose | Example |
|---------|---------|---------|
| `head file` | First 10 lines | Start of file |
| `head -n 100 file` | First N lines | Custom count |
| `head -c 1024 file` | First bytes | By size |
| `tail file` | Last 10 lines | End of file |
| `tail -n 50 file` | Last N lines | Custom count |
| `tail -f file` | Follow file | Real-time tail |
| `tail -F file` | Follow with retry | Handles rotation |

## Specialized Patterns

### Extract IPv4 Addresses
```bash
grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' file
```

### Extract URLs
```bash
grep -oE 'https?://[^" ]+' file
```

### Extract Email Addresses
```bash
grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' file
```

### Extract HTTP Status Codes
```bash
grep -oE ' [0-9]{3} ' file
```

### Extract Response Times
```bash
grep -oE 'time=[0-9]+ms' file | cut -d= -f2
```

## Time-based Filtering

### Log by Date
```bash
grep "2024-01-15" app.log
```

### Log by Hour
```bash
grep "2024-01-15 10:" app.log
```

### Log by Time Range
```bash
awk '$2 >= "10:30" && $2 <= "10:35"' app.log
```

### Find Recent Logs
```bash
find . -name "*.log" -mtime -1  # Last 24 hours
```

## Common Log Analysis Patterns

### Top Errors
```bash
grep "ERROR" file | sort | uniq -c | sort -rn | head
```

### Error Rate
```bash
total=$(wc -l < file)
errors=$(grep -c "ERROR" file)
echo "scale=2; $errors*100/$total" | bc
```

### Unique IPs
```bash
awk '{print $1}' access.log | sort -u
```

### Response Time Average
```bash
awk '/response_time/ {sum+=$NF; count++} END {print sum/count}' file
```

### Count by Hour
```bash
awk '{print $2}' file | cut -d: -f1-2 | sort | uniq -c
```

## Performance Tips

| Tip | Benefit |
|-----|---------|
| Use `grep` first to filter | Reduces data to other tools |
| Avoid piping to `sed` | Use `grep` then `cut` instead |
| Pre-sort before `uniq` | Required for deduplication |
| Use `tail -f` for live logs | Real-time monitoring |
| Pipe to `less` for large output | Paginate results |
| Redirect stderr separately | Separate errors from output |

## Log Format Examples

### Syslog
```
Jan 15 10:30:45 hostname service[PID]: Message
```
Extract: `awk '{print $4}' /var/log/syslog`

### Apache/Nginx Access
```
192.168.1.1 - - [15/Jan/2024:10:30:45] "GET /path HTTP/1.1" 200 1234
```
Extract: `awk '{print $1, $7, $9}' access.log`

### JSON
```
{"timestamp":"2024-01-15T10:30:45Z","level":"ERROR","message":"Failed"}
```
Parse: Use `jq` or Python `json` module

## Tools Overview

| Tool | Best For | Speed |
|------|----------|-------|
| `grep` | Pattern matching | Fast |
| `awk` | Field extraction | Fast |
| `sed` | Text replacement | Fast |
| `cut` | Simple column extraction | Very fast |
| `sort` | Ordering | Medium |
| `uniq` | Deduplication | Fast |
| `jq` | JSON parsing | Slow for large files |
