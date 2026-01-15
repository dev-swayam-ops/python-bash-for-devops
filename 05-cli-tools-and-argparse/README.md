# CLI Tools and Argparse for DevOps

## What You'll Learn
- Building command-line tools with Python's argparse module
- Parsing command-line arguments and options
- Creating help messages and documentation
- Building professional CLI applications
- Handling subcommands and nested arguments
- Input validation and error handling
- Creating usable tools for DevOps teams

## Prerequisites
- Completed: [04-python-advanced-for-automation](../04-python-advanced-for-automation)
- Python 3.8+ with argparse module
- Understanding of Python functions and error handling
- Basic Linux/shell command knowledge

## Key Concepts
- **Arguments**: Positional values passed to script
- **Options/Flags**: Named parameters with `--` or `-`
- **Subcommands**: Multiple commands within one tool (like `git commit`, `git push`)
- **Help Messages**: Auto-generated documentation with `-h` or `--help`
- **Type Conversion**: Automatic conversion of string arguments to proper types
- **Validation**: Checking argument values and providing errors

## Hands-on Lab: Building a DevOps CLI Tool

### Step 1: Simple Script with Arguments
```bash
cat > server_info.py << 'EOF'
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(
    description="Display server information"
)
parser.add_argument("hostname", help="Server hostname")
parser.add_argument("--port", type=int, default=22, help="SSH port")
parser.add_argument("--verbose", action="store_true", help="Verbose output")

args = parser.parse_args()

print(f"Server: {args.hostname}")
print(f"Port: {args.port}")
if args.verbose:
    print("Verbose mode enabled")
EOF

chmod +x server_info.py
python3 server_info.py web01 --port 2222 --verbose

# Expected output:
# Server: web01
# Port: 2222
# Verbose mode enabled
```

### Step 2: Multiple Arguments and Choices
```bash
cat > deploy.py << 'EOF'
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="Deploy application")
parser.add_argument("app_name", help="Application name")
parser.add_argument("--environment", 
                   choices=["dev", "staging", "production"],
                   required=True,
                   help="Target environment")
parser.add_argument("--version", type=str, help="App version")
parser.add_argument("--replicas", type=int, default=3, help="Number of replicas")

args = parser.parse_args()

print(f"Deploying {args.app_name} to {args.environment}")
print(f"Replicas: {args.replicas}")
if args.version:
    print(f"Version: {args.version}")
EOF

python3 deploy.py myapp --environment production --version 1.2.0

# Expected output:
# Deploying myapp to production
# Replicas: 3
# Version: 1.2.0
```

### Step 3: Subcommands
```bash
cat > admin_tool.py << 'EOF'
#!/usr/bin/env python3
import argparse

parser = argparse.ArgumentParser(description="Admin tool")
subparsers = parser.add_subparsers(dest="command", help="Available commands")

# Start command
start_parser = subparsers.add_parser("start", help="Start service")
start_parser.add_argument("service", help="Service name")

# Stop command
stop_parser = subparsers.add_parser("stop", help="Stop service")
stop_parser.add_argument("service", help="Service name")

args = parser.parse_args()

if args.command == "start":
    print(f"✓ Starting {args.service}")
elif args.command == "stop":
    print(f"✓ Stopping {args.service}")
else:
    parser.print_help()
EOF

python3 admin_tool.py start nginx
python3 admin_tool.py stop postgres

# Expected output:
# ✓ Starting nginx
# ✓ Stopping postgres
```

### Step 4: Help Messages
```bash
python3 server_info.py --help

# Expected output:
# usage: server_info.py [-h] [--port PORT] [--verbose] hostname
# 
# Display server information
# 
# positional arguments:
#   hostname       Server hostname
# 
# optional arguments:
#   -h, --help     show this help message and exit
#   --port PORT    SSH port (default: 22)
#   --verbose      Verbose output
```

### Step 5: Error Handling
```bash
cat > backup_tool.py << 'EOF'
#!/usr/bin/env python3
import argparse
import sys

parser = argparse.ArgumentParser(description="Backup tool")
parser.add_argument("--source", required=True, help="Source directory")
parser.add_argument("--destination", required=True, help="Destination")
parser.add_argument("--compress", action="store_true", help="Compress backup")

try:
    args = parser.parse_args()
    print(f"✓ Backup: {args.source} → {args.destination}")
    if args.compress:
        print("✓ Compression enabled")
except SystemExit as e:
    if e.code != 0:
        print("Error: Invalid arguments")
        sys.exit(1)
EOF

python3 backup_tool.py --source /data --destination /backup

# Expected output:
# ✓ Backup: /data → /backup
```

## Validation Checklist
- [ ] Created simple script with positional arguments
- [ ] Added optional arguments with defaults
- [ ] Used argument type conversion (int, str)
- [ ] Created subcommands successfully
- [ ] Help messages display correctly with `-h`
- [ ] Script handles invalid arguments gracefully

## Cleanup
```bash
rm -f server_info.py deploy.py admin_tool.py backup_tool.py
```

## Common Mistakes
1. **Forgetting required=True**: Optional args won't error if missing
2. **Wrong type specification**: `type=int` not `type="int"` (string)
3. **Conflicting argument names**: Use different names for different arguments
4. **Missing help text**: Always include help messages for usability
5. **Not handling subcommand None**: Check if `command` exists before using

## Troubleshooting

### "argument X is required"
- **Cause**: Forgot `required=True` or positional arg missing
- **Solution**: Check argument definition and provide required values

### "invalid choice: 'option'"
- **Cause**: Argument not in choices list
- **Solution**: Use `parser.print_help()` to see valid choices

### "expected one argument"
- **Cause**: Positional argument missing value
- **Solution**: Provide required positional arguments

### Help text not showing
- **Cause**: Script exits before showing help
- **Solution**: Use `parser.print_help()` explicitly

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [argparse cheatsheet](cheatsheet.md)
3. Move to [06-linux-automation-scripts](../06-linux-automation-scripts)
