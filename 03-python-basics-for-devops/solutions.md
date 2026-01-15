# Python Basics for DevOps: Solutions

## Solution 1: Print Server Information

```python
#!/usr/bin/env python3

server_name = "web-server-01"
server_ip = "192.168.1.10"
server_port = 8080

print(f"Server Name: {server_name}")
print(f"IP Address: {server_ip}")
print(f"Port: {server_port}")

# Output:
# Server Name: web-server-01
# IP Address: 192.168.1.10
# Port: 8080
```

**Explanation**: F-strings (f"...") allow variable interpolation. Variables store different values that are printed with proper formatting.

---

## Solution 2: List Operations

```python
#!/usr/bin/env python3

# Create list of tools
tools = ["git", "docker", "kubernetes"]
print("Initial tools:", tools)

# Add new tool
tools.append("jenkins")
print("Updated tools:", tools)

# Output:
# Initial tools: ['git', 'docker', 'kubernetes']
# Updated tools: ['git', 'docker', 'kubernetes', 'jenkins']
```

**Explanation**: Lists are mutable sequences. `.append()` adds element at end. Square brackets `[]` create lists.

---

## Solution 3: Dictionary Access

```python
#!/usr/bin/env python3

server_config = {
    "host": "localhost",
    "port": 5432,
    "timeout": 30
}

print(f"Host: {server_config['host']}")
print(f"Port: {server_config['port']}")
print(f"Timeout: {server_config['timeout']}")

# Output:
# Host: localhost
# Port: 5432
# Timeout: 30
```

**Explanation**: Dictionaries use key-value pairs. Access values with `dict[key]` syntax. Keys are typically strings.

---

## Solution 4: Conditional Logic

```python
#!/usr/bin/env python3

port = int(input("Enter port number: "))

if 1 <= port <= 65535:
    print(f"✓ Port {port} is valid")
else:
    print(f"✗ Port {port} is invalid (must be 1-65535)")

# Output (example with port 8080):
# Enter port number: 8080
# ✓ Port 8080 is valid
```

**Explanation**: `if/else` branches execute based on condition. Range check uses comparison operators `<=` and `>=`.

---

## Solution 5: Loop Through Dictionary

```python
#!/usr/bin/env python3

services = {
    "api": 8080,
    "database": 5432,
    "cache": 6379
}

print("=== Services Configuration ===")
for service_name, port in services.items():
    print(f"- {service_name}: port {port}")

# Output:
# === Services Configuration ===
# - api: port 8080
# - database: port 5432
# - cache: port 6379
```

**Explanation**: `.items()` returns key-value pairs. Loop unpacks each pair into separate variables.

---

## Solution 6: Function with Return Value

```python
#!/usr/bin/env python3

def calculate_uptime(up_seconds, total_seconds):
    """Calculate uptime percentage"""
    if total_seconds == 0:
        return 0
    uptime_percent = (up_seconds / total_seconds) * 100
    return uptime_percent

# Test function
uptime = calculate_uptime(345600, 360000)
print(f"System uptime: {uptime:.2f}%")

uptime2 = calculate_uptime(720000, 720000)
print(f"Perfect uptime: {uptime2:.2f}%")

# Output:
# System uptime: 96.00%
# Perfect uptime: 100.00%
```

**Explanation**: Function `def` defines reusable code. `return` sends result back to caller. `:.2f` formats to 2 decimal places.

---

## Solution 7: Error Handling

```python
#!/usr/bin/env python3

try:
    port_input = input("Enter port number: ")
    port = int(port_input)
    
    if 1 <= port <= 65535:
        print(f"✓ Valid port: {port}")
    else:
        print(f"✗ Port must be between 1-65535")
        
except ValueError:
    print(f"✗ Error: '{port_input}' is not a valid number")

# Output (example with invalid input):
# Enter port number: abc
# ✗ Error: 'abc' is not a valid number
```

**Explanation**: `try` block contains code that might fail. `except` catches specific errors. `ValueError` occurs when conversion fails.

---

## Solution 8: File Operations

```python
#!/usr/bin/env python3

# Write to file
with open("servers.txt", "w") as f:
    f.write("server01: 192.168.1.10\n")
    f.write("server02: 192.168.1.11\n")
    f.write("server03: 192.168.1.12\n")

print("✓ Config written to servers.txt")

# Read from file
print("\n=== Server Configuration ===")
with open("servers.txt", "r") as f:
    content = f.read()
    print(content)

# Output:
# ✓ Config written to servers.txt
# === Server Configuration ===
# server01: 192.168.1.10
# server02: 192.168.1.11
# server03: 192.168.1.12
```

**Explanation**: `with` statement auto-closes file. `"w"` mode writes (overwrites existing). `"r"` mode reads. `.read()` gets entire content.

---

## Solution 9: List Comprehension

```python
#!/usr/bin/env python3

# Create list of numbers 1-10
all_numbers = list(range(1, 11))
print(f"All numbers: {all_numbers}")

# Filter even numbers using list comprehension
even_numbers = [x for x in range(1, 11) if x % 2 == 0]
print(f"Even numbers: {even_numbers}")

# Output:
# All numbers: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
# Even numbers: [2, 4, 6, 8, 10]
```

**Explanation**: List comprehension `[expr for x in iterable if condition]` creates filtered lists concisely. `x % 2 == 0` checks if even.

---

## Solution 10: JSON Handling

```python
#!/usr/bin/env python3
import json

# Create server configuration dictionary
server_config = {
    "name": "web-server-01",
    "ip": "192.168.1.10",
    "port": 8080,
    "environment": "production",
    "services": ["api", "cache", "database"]
}

# Convert to JSON and save
with open("config.json", "w") as f:
    json.dump(server_config, f, indent=2)

print("✓ Configuration saved to config.json")

# Read and parse JSON
with open("config.json", "r") as f:
    loaded_config = json.load(f)

print("\n=== Loaded Configuration ===")
print(f"Server: {loaded_config['name']}")
print(f"IP: {loaded_config['ip']}")
print(f"Port: {loaded_config['port']}")
print(f"Services: {loaded_config['services']}")

# Output:
# ✓ Configuration saved to config.json
# === Loaded Configuration ===
# Server: web-server-01
# IP: 192.168.1.10
# Port: 8080
# Services: ['api', 'cache', 'database']
```

**Explanation**: `json.dump()` writes to file. `json.load()` reads from file. `indent=2` makes JSON readable. Works with dicts, lists, strings, numbers.
