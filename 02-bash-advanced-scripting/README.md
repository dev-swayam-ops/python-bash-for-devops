# Bash Advanced Scripting for DevOps

## What You'll Learn
- Error handling and exit codes
- Regular expressions and pattern matching
- Text processing with sed, awk, and grep
- Working with configuration files
- Script optimization and best practices
- Logging and debugging techniques
- Parallel execution and background jobs

## Prerequisites
- Completed: [01-bash-basics](../01-bash-basics)
- Familiar with bash variables, loops, and functions
- Understanding of pipes and redirection
- Text editor or IDE

## Key Concepts
- **Exit Codes**: Numeric values (0=success, 1-255=error) indicating command status
- **Regular Expressions (Regex)**: Pattern matching for text search and validation
- **Text Processing**: Tools like `sed`, `awk`, `grep` for data manipulation
- **Error Handling**: Using `set -e`, `trap`, conditional checks
- **Process Management**: Running commands in background, managing multiple processes
- **Logging**: Recording script execution and errors to files

## Hands-on Lab: Building a Robust Admin Script

### Step 1: Create Directory Structure
```bash
mkdir -p devops-lab/{logs,configs,scripts}
cd devops-lab

# Expected output: You're in devops-lab directory
```

### Step 2: Create a Configuration File
```bash
cat > configs/app.conf << 'EOF'
APP_NAME="MyApp"
LOG_DIR="./logs"
LOG_LEVEL="INFO"
MAX_RETRIES=3
TIMEOUT=30
EOF

cat configs/app.conf
```

### Step 3: Create a Script with Error Handling
```bash
cat > scripts/app_checker.sh << 'EOF'
#!/bin/bash
set -euo pipefail

# Source configuration
source "../configs/app.conf"

# Setup logging
LOG_FILE="$LOG_DIR/app_$(date +%Y%m%d_%H%M%S).log"
mkdir -p "$LOG_DIR"

log_message() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Trap errors
trap 'log_message "ERROR: Script failed at line $LINENO"' ERR

log_message "Starting application check"
log_message "App name: $APP_NAME"
log_message "Log level: $LOG_LEVEL"
log_message "Application check completed"
EOF

chmod +x scripts/app_checker.sh
./scripts/app_checker.sh
```

### Step 4: Test Text Processing with awk
```bash
# Create sample data
cat > configs/servers.txt << 'EOF'
server01 192.168.1.10 active
server02 192.168.1.11 inactive
server03 192.168.1.12 active
EOF

# Process with awk
echo "=== Active Servers ==="
awk '$3 == "active" {print $1, $2}' configs/servers.txt

# Expected output:
# server01 192.168.1.10
# server03 192.168.1.12
```

### Step 5: Use grep for Validation
```bash
# Create log file
echo "ERROR: Connection failed
INFO: Server started
ERROR: Timeout occurred
INFO: Data synced" > logs/app.log

# Count errors
echo "=== Error Count ==="
grep -c "ERROR" logs/app.log

# Expected output:
# 2
```

## Validation Checklist
- [ ] Directory structure created successfully
- [ ] Configuration file sourced correctly in script
- [ ] Error handling trap activated on script failure
- [ ] Logging writes to file with timestamps
- [ ] awk processes data correctly
- [ ] grep finds patterns as expected

## Cleanup
```bash
cd ..
rm -rf devops-lab
```

## Common Mistakes
1. **Not checking exit codes**: Always validate command success before proceeding
2. **Unquoted variables in loops**: Use `"$var"` to handle spaces correctly
3. **Ignoring regex escaping**: Special regex chars need escaping with `\`
4. **Overwriting log files**: Use `>>` append instead of `>` redirect
5. **Missing error trap**: Don't skip `trap` for production scripts

## Troubleshooting

### "No such file or directory" when sourcing config
- **Cause**: Wrong file path or source command in wrong directory
- **Solution**: Use absolute paths: `source "$(dirname "$0")/../configs/app.conf"`

### awk/sed commands not matching patterns
- **Cause**: Regex pattern not escaped or wrong syntax
- **Solution**: Test regex in isolation: `echo "test" | grep -E "pattern"`

### Script continues after error
- **Cause**: Missing `set -e` or error handling
- **Solution**: Add `set -euo pipefail` at script start

### Background jobs not completing
- **Cause**: Script exits before background processes finish
- **Solution**: Use `wait` command: `wait` (all) or `wait $PID` (specific)

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [advanced cheatsheet](cheatsheet.md) for patterns
3. Move to [03-python-basics-for-devops](../03-python-basics-for-devops)
