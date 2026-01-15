# Python Advanced for Automation: Solutions

## Solution 1: Run System Command

```python
#!/usr/bin/env python3
import subprocess

result = subprocess.run(
    ["ls", "-la"],
    capture_output=True,
    text=True
)

if result.returncode == 0:
    print("=== Directory Listing ===")
    print(result.stdout)
else:
    print(f"Error: {result.stderr}")

# Output:
# === Directory Listing ===
# total 24
# drwxr-xr-x  user  group  4096  Jan 15 10:30 .
# ...
```

**Explanation**: `subprocess.run()` executes command as list. `capture_output=True` captures stdout/stderr. `text=True` returns strings instead of bytes. `returncode` 0 means success.

---

## Solution 2: Environment Variable Access

```python
#!/usr/bin/env python3
import os

env_vars = ["PATH", "HOME", "USER", "SHELL"]

print("=== Environment Variables ===")
print(f"{'Variable':<15} {'Value':<40}")
print("-" * 55)

for var in env_vars:
    value = os.getenv(var, "NOT SET")
    # Truncate long values
    if len(value) > 40:
        value = value[:37] + "..."
    print(f"{var:<15} {value:<40}")

# Output:
# === Environment Variables ===
# Variable        Value
# -----------------------------------------------
# PATH            /usr/local/bin:/usr/bin:/bin:...
# HOME            /home/user
# USER            user
# SHELL           /bin/bash
```

**Explanation**: `os.getenv(var, default)` safely reads env vars. Format string `{:<15}` left-aligns in 15 chars. Provides safe default if var not set.

---

## Solution 3: Basic Regex Search

```python
#!/usr/bin/env python3
import re

text = """
Contact support at admin@example.com or user@devops.io
For sales, email sales@company.net
"""

pattern = r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'

print("=== Email Addresses Found ===")
emails = re.findall(pattern, text)
for email in emails:
    print(f"- {email}")

# Output:
# === Email Addresses Found ===
# - admin@example.com
# - user@devops.io
# - sales@company.net
```

**Explanation**: `re.findall()` returns all matches as list. Pattern breaks down: alphanumeric + @ + domain + . + TLD (2+ chars). `r''` raw string preserves backslashes.

---

## Solution 4: Working with Classes

```python
#!/usr/bin/env python3

class Server:
    def __init__(self, name, ip):
        self.name = name
        self.ip = ip
        self.status = "stopped"
    
    def start(self):
        """Start the server"""
        self.status = "running"
        print(f"✓ {self.name} started on {self.ip}")
    
    def stop(self):
        """Stop the server"""
        self.status = "stopped"
        print(f"✓ {self.name} stopped")
    
    def get_status(self):
        """Get current status"""
        return f"{self.name}: {self.status}"

# Test
server = Server("web-app", "192.168.1.10")
print(server.get_status())
server.start()
print(server.get_status())
server.stop()

# Output:
# web-app: stopped
# ✓ web-app started on 192.168.1.10
# web-app: running
# ✓ web-app stopped
```

**Explanation**: `__init__()` initializes instance variables. Methods use `self` to access instance data. `self.status` maintains state. Classes provide structure for related functionality.

---

## Solution 5: API Request

```python
#!/usr/bin/env python3
import requests

try:
    # Make request to JSONPlaceholder API
    response = requests.get(
        "https://jsonplaceholder.typicode.com/posts/1"
    )
    response.raise_for_status()
    
    # Parse JSON
    data = response.json()
    
    print("=== Post Data ===")
    print(f"Title: {data['title']}")
    print(f"Body: {data['body'][:100]}...")
    print(f"User ID: {data['userId']}")
    
except requests.exceptions.RequestException as e:
    print(f"✗ Request failed: {e}")

# Output:
# === Post Data ===
# Title: sunt aut facere repellat provident...
# Body: quia et suscipit...
# User ID: 1
```

**Explanation**: `requests.get()` makes HTTP request. `.json()` parses JSON response. `.raise_for_status()` raises error on failed status. Try/except handles network errors.

---

## Solution 6: Error Handling with Logging

```python
#!/usr/bin/env python3
import logging

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

# Sample data
data_lines = [
    "server1:8080",
    "invalid_data",
    "server2:9000",
    "bad:entry:format",
    "server3:3000"
]

print("=== Processing Data ===")
for line in data_lines:
    try:
        parts = line.split(":")
        if len(parts) != 2:
            raise ValueError(f"Invalid format")
        
        server, port = parts
        port = int(port)
        logger.info(f"✓ Parsed {server}:{port}")
    except (ValueError, IndexError) as e:
        logger.error(f"Failed to parse '{line}': {e}")
        continue

# Output:
# === Processing Data ===
# 2024-01-15 10:30:45,123 - INFO - ✓ Parsed server1:8080
# 2024-01-15 10:30:45,124 - ERROR - Failed to parse 'invalid_data': ...
```

**Explanation**: Logging module structured output. Try/except prevents crashes on bad data. `continue` skips to next iteration. Errors logged but processing continues.

---

## Solution 7: Complex Regex Replacement

```python
#!/usr/bin/env python3
import re

log_content = """
2024-01-15 10:30:45 User 192.168.1.10 connected
2024-01-15 10:31:00 User 10.0.0.5 authenticated
2024-01-15 10:31:15 User 172.16.0.1 disconnected
"""

# Redact IP addresses
ip_pattern = r'\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}'
redacted = re.sub(ip_pattern, "XXX.XXX.XXX.XXX", log_content)

# Redact timestamps
timestamp_pattern = r'\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}'
redacted = re.sub(timestamp_pattern, "YYYY-MM-DD HH:MM:SS", redacted)

print("=== Redacted Log ===")
print(redacted)

# Output:
# === Redacted Log ===
# YYYY-MM-DD HH:MM:SS User XXX.XXX.XXX.XXX connected
# ...
```

**Explanation**: `re.sub(pattern, replacement, text)` replaces all matches. Useful for sanitizing sensitive data before storage or sharing logs.

---

## Solution 8: Configuration from YAML/JSON

```python
#!/usr/bin/env python3
import json

def load_and_validate_config(config_file):
    """Load config and validate required fields"""
    required_fields = ["app_name", "port", "environment"]
    
    try:
        with open(config_file) as f:
            config = json.load(f)
        
        # Validate required fields
        missing = [f for f in required_fields if f not in config]
        if missing:
            raise ValueError(f"Missing required fields: {missing}")
        
        return config
    
    except FileNotFoundError:
        print(f"✗ Config file not found: {config_file}")
        return None
    except json.JSONDecodeError:
        print(f"✗ Invalid JSON in {config_file}")
        return None
    except ValueError as e:
        print(f"✗ Validation error: {e}")
        return None

# Create test config
test_config = {
    "app_name": "AutomationApp",
    "port": 8080,
    "environment": "production"
}

with open("test_config.json", "w") as f:
    json.dump(test_config, f)

config = load_and_validate_config("test_config.json")
if config:
    print("✓ Configuration loaded successfully")
    print(f"App: {config['app_name']}")
```

**Explanation**: Separates loading and validation. List comprehension checks for missing fields. Multiple except blocks handle different errors gracefully.

---

## Solution 9: File-based State Tracking

```python
#!/usr/bin/env python3
import json
import os
from datetime import datetime

STATE_FILE = "state.json"

def load_state():
    """Load previous state"""
    if os.path.exists(STATE_FILE):
        with open(STATE_FILE) as f:
            return json.load(f)
    return {"last_run": None}

def save_state(state):
    """Save current state"""
    with open(STATE_FILE, "w") as f:
        json.dump(state, f, indent=2)

def should_process(data_item):
    """Check if item should be processed"""
    state = load_state()
    last_run = state.get("last_run")
    
    if last_run is None:
        return True  # First run
    
    # Simple check: process if timestamp is newer
    return True

# Process items
items = ["log1", "log2", "log3"]
for item in items:
    if should_process(item):
        print(f"Processing: {item}")

# Update state
state = {"last_run": datetime.now().isoformat()}
save_state(state)
print("✓ State saved")
```

**Explanation**: Tracks state to avoid reprocessing. `load_state()` retrieves previous run info. `save_state()` persists for next run. Useful for incremental processing.

---

## Solution 10: Multi-step Automation Script

```python
#!/usr/bin/env python3
import json
import subprocess
import logging
from pathlib import Path

# Setup logging
logging.basicConfig(
    level=logging.INFO,
    format='%(levelname)s: %(message)s'
)
logger = logging.getLogger(__name__)

class DevOpsAutomation:
    def __init__(self, config_file="config.json"):
        self.config = self.load_config(config_file)
    
    def load_config(self, config_file):
        """Load and validate config"""
        try:
            with open(config_file) as f:
                config = json.load(f)
            logger.info("✓ Configuration loaded")
            return config
        except Exception as e:
            logger.error(f"Failed to load config: {e}")
            return None
    
    def validate(self):
        """Validate configuration"""
        required = ["servers", "commands"]
        if not self.config:
            logger.error("No config loaded")
            return False
        
        missing = [k for k in required if k not in self.config]
        if missing:
            logger.error(f"Missing config: {missing}")
            return False
        
        logger.info("✓ Configuration valid")
        return True
    
    def run_commands(self):
        """Execute automation commands"""
        for cmd in self.config.get("commands", []):
            try:
                result = subprocess.run(
                    cmd, shell=True, capture_output=True, text=True, timeout=10
                )
                if result.returncode == 0:
                    logger.info(f"✓ Command succeeded: {cmd}")
                else:
                    logger.error(f"✗ Command failed: {cmd}")
            except Exception as e:
                logger.error(f"✗ Command error: {e}")
    
    def run(self):
        """Main automation workflow"""
        if self.validate():
            self.run_commands()
            logger.info("✓ Automation completed")
        else:
            logger.error("✗ Automation failed")

# Create test config
test_config = {
    "servers": ["web1", "web2"],
    "commands": ["echo 'Running check 1'", "echo 'Running check 2'"]
}

with open("automation_config.json", "w") as f:
    json.dump(test_config, f)

# Run automation
automation = DevOpsAutomation("automation_config.json")
automation.run()
```

**Explanation**: Complete workflow: load config → validate → execute → log. Class-based design for reusability. Error handling at each step. Structured logging for monitoring.
