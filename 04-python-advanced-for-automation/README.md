# Python Advanced for Automation

## What You'll Learn
- Working with external commands (subprocess module)
- Advanced string processing and regex
- API interaction with requests library
- Handling configuration and environment variables
- Logging and debugging techniques
- Creating reusable modules and packages
- Testing automation scripts
- Performance optimization

## Prerequisites
- Completed: [03-python-basics-for-devops](../03-python-basics-for-devops)
- Comfortable with Python functions and classes
- Understanding of JSON and APIs (basics)
- Ability to install Python packages with pip

## Key Concepts
- **Subprocess**: Running external commands from Python
- **Regex**: Pattern matching for complex text processing
- **APIs**: Communicating with web services programmatically
- **Environment Variables**: Configuration from system environment
- **Logging**: Structured logging instead of print statements
- **Classes**: Object-oriented programming for complex scripts
- **Testing**: Writing unit tests for automation code

## Hands-on Lab: Building an Admin Automation Script

### Step 1: Install Required Packages
```bash
pip3 install requests pyyaml

# Expected output:
# Successfully installed requests pyyaml
```

### Step 2: Create Configuration Management Script
```bash
cat > config_manager.py << 'EOF'
#!/usr/bin/env python3
import os
import json
from pathlib import Path

class ConfigManager:
    def __init__(self, config_file="config.json"):
        self.config_file = config_file
        self.config = self.load()
    
    def load(self):
        """Load configuration from file"""
        if Path(self.config_file).exists():
            with open(self.config_file) as f:
                return json.load(f)
        return {}
    
    def get(self, key, default=None):
        """Get config value"""
        return self.config.get(key, default)
    
    def save(self):
        """Save configuration to file"""
        with open(self.config_file, "w") as f:
            json.dump(self.config, f, indent=2)

# Test
config = ConfigManager()
config.config["app_name"] = "AutomationEngine"
config.config["version"] = "2.0"
config.save()
print("✓ Configuration saved")
EOF

python3 config_manager.py
```

### Step 3: Create Subprocess Runner Script
```bash
cat > command_runner.py << 'EOF'
#!/usr/bin/env python3
import subprocess
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def run_command(command):
    """Execute command and return output"""
    try:
        result = subprocess.run(
            command,
            shell=True,
            capture_output=True,
            text=True,
            timeout=10
        )
        if result.returncode == 0:
            logger.info(f"✓ Command succeeded: {command}")
            return result.stdout
        else:
            logger.error(f"✗ Command failed: {result.stderr}")
            return None
    except subprocess.TimeoutExpired:
        logger.error(f"✗ Command timeout: {command}")
        return None

# Test
output = run_command("echo 'Hello from automation'")
if output:
    print(output)
EOF

python3 command_runner.py
```

### Step 4: Working with Regex
```bash
cat > log_analyzer.py << 'EOF'
#!/usr/bin/env python3
import re

# Sample log data
log_content = """
2024-01-15 10:30:45 ERROR Connection failed
2024-01-15 10:30:50 INFO Retrying...
2024-01-15 10:31:00 ERROR Timeout
2024-01-15 10:31:05 INFO Success
"""

# Regex patterns
error_pattern = r"ERROR.*"
time_pattern = r"(\d{2}:\d{2}:\d{2})"

print("=== Error Lines ===")
errors = re.findall(error_pattern, log_content)
for error in errors:
    print(error)

print("\n=== Timestamps ===")
times = re.findall(time_pattern, log_content)
for time in times:
    print(time)
EOF

python3 log_analyzer.py
```

### Step 5: Create Reusable Logger
```bash
cat > setup_logging.py << 'EOF'
#!/usr/bin/env python3
import logging

def setup_logger(name, log_file="app.log"):
    """Configure logger for script"""
    logger = logging.getLogger(name)
    logger.setLevel(logging.DEBUG)
    
    # File handler
    fh = logging.FileHandler(log_file)
    fh.setLevel(logging.DEBUG)
    
    # Console handler
    ch = logging.StreamHandler()
    ch.setLevel(logging.INFO)
    
    # Formatter
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )
    fh.setFormatter(formatter)
    ch.setFormatter(formatter)
    
    logger.addHandler(fh)
    logger.addHandler(ch)
    return logger

# Test
logger = setup_logger(__name__)
logger.info("Application started")
logger.debug("Debug information")
logger.warning("Warning message")
logger.error("Error occurred")
EOF

python3 setup_logging.py
```

## Validation Checklist
- [ ] Required packages installed (requests, pyyaml)
- [ ] Configuration manager saves/loads JSON correctly
- [ ] Subprocess runner executes commands and captures output
- [ ] Regex patterns match log lines correctly
- [ ] Logging writes to both file and console
- [ ] Error handling works for failed commands

## Cleanup
```bash
rm -f config_manager.py command_runner.py log_analyzer.py setup_logging.py config.json app.log
```

## Common Mistakes
1. **Not handling subprocess errors**: Always check return codes
2. **Hardcoding credentials**: Use environment variables instead
3. **Poor regex patterns**: Test patterns separately before using
4. **Ignoring timeouts**: Always set timeout for subprocess calls
5. **Silent failures**: Use proper logging instead of print statements

## Troubleshooting

### "ModuleNotFoundError: No module named 'requests'"
- **Cause**: Package not installed
- **Solution**: Run `pip3 install requests`

### Subprocess hangs or times out
- **Cause**: Command takes too long or waits for input
- **Solution**: Set `timeout` parameter and use `capture_output=True`

### Regex pattern not matching
- **Cause**: Pattern syntax error or wrong delimiter
- **Solution**: Test pattern in Python REPL: `re.search(r"pattern", text)`

### Sensitive data in logs
- **Cause**: Logging passwords or tokens
- **Solution**: Filter sensitive values before logging

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [advanced cheatsheet](cheatsheet.md)
3. Explore packages: requests, paramiko, ansible-runner
