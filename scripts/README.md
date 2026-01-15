# Scripts - Common DevOps Utilities Library

## What You'll Learn

- Structure reusable DevOps scripts
- Create utility functions for common tasks
- Build command-line tools with argument parsing
- Handle errors and logging effectively
- Create modular, maintainable bash code
- Package scripts for distribution
- Write self-documenting code with help functions

## Prerequisites

- Bash scripting fundamentals (modules 01-02)
- Understanding of functions and libraries (module 02)
- CLI tools basics (module 05)
- Basic understanding of DevOps tasks

## Key Concepts

### Script Organization
- Utility functions (reusable code blocks)
- Source-able libraries (import functions)
- Standalone executables (complete tools)
- Configuration file usage

### Best Practices
- Error handling and exit codes
- Input validation
- Logging and debugging
- Help/usage documentation
- Portable code (avoid distro-specific commands)

### Common Utilities
- Health checks and monitoring
- Deployment helpers
- Backup and restore
- File management
- System utilities

## Hands-on Lab: Create Reusable Script Library

### Step 1: Create Base Utility Functions
```bash
cat > scripts/utils.sh << 'EOF'
#!/bin/bash
# Source this file to use utility functions

log_info() { echo "[INFO] $*"; }
log_error() { echo "[ERROR] $*" >&2; }
log_debug() { [ "$DEBUG" = "1" ] && echo "[DEBUG] $*"; }

check_command() {
  command -v $1 >/dev/null 2>&1 || { log_error "$1 not found"; return 1; }
}

retry() {
  local max_attempts=$1; shift
  local attempt=1
  while [ $attempt -le $max_attempts ]; do
    "$@" && return 0
    log_info "Attempt $attempt failed, retrying..."
    ((attempt++))
    sleep 2
  done
  return 1
}
EOF
chmod +x scripts/utils.sh
```

**Expected Output:**
```
Created reusable utility functions
```

### Step 2: Create Health Check Script
```bash
cat > scripts/health_check.sh << 'EOF'
#!/bin/bash
source ./scripts/utils.sh

SERVICE=$1
PORT=$2

log_info "Checking health of $SERVICE on port $PORT"
curl -f http://localhost:$PORT/health && log_info "✓ Service is healthy" || log_error "✗ Service unhealthy"
EOF
chmod +x scripts/health_check.sh
```

### Step 3: Test Utility Functions
```bash
source ./scripts/utils.sh
log_info "Testing utilities"
check_command curl || echo "curl missing"
retry 3 curl http://localhost:8080/health
```

### Step 4: Source in Main Script
```bash
#!/bin/bash
source ./scripts/utils.sh

main() {
  log_info "Starting deployment"
  check_command docker || exit 1
  log_info "All requirements met"
}

main "$@"
```

### Step 5: Verify Script Works
```bash
bash ./scripts/health_check.sh myapp 8080
```

## Validation

- [ ] Utility functions execute without errors
- [ ] Log functions work correctly
- [ ] Retry mechanism works
- [ ] Scripts source utilities properly
- [ ] Error handling functions as expected
- [ ] Help text displays correctly

## Cleanup

```bash
rm -f scripts/*.sh
```

## Common Mistakes

- Not making scripts executable (`chmod +x`)
- Hardcoding values instead of using arguments
- Missing error handling
- No logging output
- Assuming command availability
- Using absolute paths (breaks on different systems)

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Permission denied" | Run `chmod +x script.sh` |
| Command not found in script | Use full path or check PATH |
| Source file not found | Use absolute or relative paths correctly |
| Variables not exported | Use `export VAR=value` |
| Script hangs | Add timeout checks, use `set -e` |

## Next Steps

- Create shared function library in git
- Implement comprehensive logging with timestamps
- Add configuration file support
- Build testing framework for scripts
- Document all utility functions
