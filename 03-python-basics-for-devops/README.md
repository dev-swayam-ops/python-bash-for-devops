# Python Basics for DevOps

## What You'll Learn
- Python syntax and data types (strings, lists, dictionaries, tuples)
- Control flow (if/else, loops, functions)
- Working with files and directories
- Error handling with try/except
- Using built-in modules (os, sys, json, subprocess)
- Writing your first automation script

## Prerequisites
- Python 3.8+ installed
- Text editor or IDE (VS Code, PyCharm)
- Completed: [02-bash-advanced-scripting](../02-bash-advanced-scripting)
- Basic programming knowledge

## Key Concepts
- **Data Types**: Variables store different types (int, str, list, dict, bool)
- **Functions**: Reusable code blocks that accept parameters
- **Modules**: Code libraries that provide pre-built functionality
- **File I/O**: Reading and writing files
- **Error Handling**: Using try/except for graceful error management
- **Scripting**: Automating tasks with Python scripts

## Hands-on Lab: Your First DevOps Script

### Step 1: Check Python Installation
```bash
python3 --version
which python3

# Expected output:
# Python 3.9.0 (or higher)
# /usr/bin/python3
```

### Step 2: Create Your First Script
```bash
cat > hello_devops.py << 'EOF'
#!/usr/bin/env python3
"""
My first DevOps automation script
"""

print("Hello, DevOps World!")
print("Python is ready for automation!")
EOF

chmod +x hello_devops.py
python3 hello_devops.py

# Expected output:
# Hello, DevOps World!
# Python is ready for automation!
```

### Step 3: Working with Variables and Data Types
```bash
cat > data_types_demo.py << 'EOF'
#!/usr/bin/env python3

# String
app_name = "MyApp"
version = "1.0.0"

# Numbers
port = 8080
timeout = 30.5

# List
servers = ["server1", "server2", "server3"]

# Dictionary
config = {
    "environment": "production",
    "replicas": 3,
    "enabled": True
}

print(f"App: {app_name} v{version}")
print(f"Server list: {servers}")
print(f"Config: {config}")
EOF

python3 data_types_demo.py

# Expected output:
# App: MyApp v1.0.0
# Server list: ['server1', 'server2', 'server3']
# Config: {'environment': 'production', 'replicas': 3, 'enabled': True}
```

### Step 4: Control Flow with Loops
```bash
cat > loop_demo.py << 'EOF'
#!/usr/bin/env python3

servers = ["web1", "web2", "web3"]

print("=== Checking Servers ===")
for server in servers:
    print(f"- {server}")

print("\n=== Counting ===")
for i in range(1, 4):
    print(f"Count: {i}")
EOF

python3 loop_demo.py

# Expected output:
# === Checking Servers ===
# - web1
# - web2
# - web3
# === Counting ===
# Count: 1
# Count: 2
# Count: 3
```

### Step 5: Functions and Error Handling
```bash
cat > functions_demo.py << 'EOF'
#!/usr/bin/env python3

def validate_port(port):
    """Check if port is valid"""
    if isinstance(port, int) and 1 <= port <= 65535:
        return True
    return False

def start_service(name, port):
    """Simulate starting a service"""
    if validate_port(port):
        print(f"✓ Starting {name} on port {port}")
        return True
    else:
        print(f"✗ Invalid port: {port}")
        return False

# Usage
result = start_service("API", 8080)
result = start_service("DB", 99999)
EOF

python3 functions_demo.py

# Expected output:
# ✓ Starting API on port 8080
# ✗ Invalid port: 99999
```

## Validation Checklist
- [ ] Python 3.8+ installed and accessible
- [ ] Created and executed hello_devops.py
- [ ] Understood basic data types and variables
- [ ] Used loops to iterate over lists
- [ ] Created and called functions
- [ ] Implemented error checking logic

## Cleanup
```bash
rm -f hello_devops.py data_types_demo.py loop_demo.py functions_demo.py
```

## Common Mistakes
1. **Indentation errors**: Python requires consistent indentation (4 spaces)
2. **Mixing Python 2 and 3**: Always use `python3`, not `python`
3. **String vs variable**: Use `f"{var}"` for string interpolation, not `"{var}"`
4. **Missing colons**: Conditional blocks need `:` at end of line
5. **Off-by-one errors**: Remember `range(1, 4)` produces 1,2,3 (not 4)

## Troubleshooting

### "ModuleNotFoundError: No module named 'module'"
- **Cause**: Required module not installed
- **Solution**: Use `pip3 install module-name`

### "IndentationError: expected an indented block"
- **Cause**: Missing indentation after `:` line
- **Solution**: Add 4 spaces (or 1 tab) for code block

### "NameError: name 'variable' is not defined"
- **Cause**: Variable used before assignment
- **Solution**: Define variable before using it

### "TypeError: unsupported operand type(s)"
- **Cause**: Operation on incompatible types (e.g., string + number)
- **Solution**: Convert types with `str()`, `int()`, `float()`

## Next Steps
1. Practice with [exercises](exercises.md)
2. Study [Python cheatsheet](cheatsheet.md)
3. Move to [04-python-advanced-for-automation](../04-python-advanced-for-automation)
