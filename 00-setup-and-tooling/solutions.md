# Setup and Tooling: Solutions

## Solution 1: Verify Python Installation

```bash
python --version
which python

# Create simple script
echo '#!/bin/bash
echo "Python is ready for DevOps!"' > check_python.sh

chmod +x check_python.sh
./check_python.sh
```

**Explanation**: The `which` command locates the Python executable. We create and execute a simple bash script with proper permissions.

---

## Solution 2: Git Configuration Validation

```bash
# Configure globally
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Verify configuration
git config --global user.name
git config --global user.email
git config --global --list
```

**Explanation**: The `--global` flag stores configuration in `~/.gitconfig`. Verification commands display the stored values.

---

## Solution 3: Create Project Directory Structure

```bash
mkdir -p devops-projects/{bash-scripts,python-scripts,configs}

# Verify structure
tree devops-projects
# OR
ls -R devops-projects
```

**Explanation**: The `-p` flag creates parent directories if they don't exist. Brace expansion `{}` creates multiple subdirectories in one command.

---

## Solution 4: Environment Variable Check

```bash
# Print PATH
echo $PATH

# Check Python-related variables
env | grep -i python

# Save to file
env | grep -E "(PATH|PYTHON)" > env_vars.txt
cat env_vars.txt
```

**Explanation**: `env` displays all environment variables. `grep -i` performs case-insensitive search. Redirect `>` saves output to a file.

---

## Solution 5: Install and Verify Multiple Tools

```bash
#!/bin/bash
# verify_tools.sh

tools=("curl" "wget" "git")

echo "Verifying DevOps tools..."
for tool in "${tools[@]}"; do
    if command -v $tool &> /dev/null; then
        version=$($tool --version | head -n1)
        echo "✓ $tool: $version"
    else
        echo "✗ $tool: NOT INSTALLED"
    fi
done
```

**Explanation**: Loop iterates through tool array. `command -v` checks if tool exists. Conditional `if` validates installation status.

---

## Solution 6: PATH Troubleshooting Script

```bash
#!/bin/bash
# add_to_path.sh

# Add custom directory to PATH
CUSTOM_DIR="$HOME/custom-bin"
mkdir -p $CUSTOM_DIR

# Add to .bashrc
if ! grep -q "custom-bin" ~/.bashrc; then
    echo "export PATH=\"\$HOME/custom-bin:\$PATH\"" >> ~/.bashrc
fi

echo "Custom directory added to PATH"
echo "Reload shell: source ~/.bashrc"
```

**Explanation**: Checks if path already exists using `grep -q` (quiet mode). Appends to `.bashrc` to make addition persistent. User must reload shell to apply changes.

---

## Solution 7: System Requirements Validator

```bash
#!/bin/bash
# validate_system.sh

echo "=== DevOps System Requirements Check ==="

# Check Python version
python_version=$(python3 --version 2>&1 | awk '{print $2}')
echo "Python: $python_version"

# Check tools
for cmd in git curl; do
    if command -v $cmd &> /dev/null; then
        echo "✓ $cmd installed"
    else
        echo "✗ $cmd missing"
    fi
done

# Check RAM (Linux)
if [ -f /proc/meminfo ]; then
    ram_gb=$(($(grep MemTotal /proc/meminfo | awk '{print $2}') / 1024 / 1024))
    echo "RAM: ${ram_gb}GB"
    [ $ram_gb -ge 4 ] && echo "✓ RAM sufficient" || echo "✗ RAM low"
fi
```

**Explanation**: Extracts version strings using `awk`. Validates minimum requirements. Checks `/proc/meminfo` for RAM on Linux systems.

---

## Solution 8: Setup Automation Script

```bash
#!/bin/bash
# setup_devops.sh

OS=$(uname -s)
echo "Detected OS: $OS"

if [ "$OS" = "Linux" ]; then
    sudo apt update
    sudo apt install -y python3 git curl wget build-essential
elif [ "$OS" = "Darwin" ]; then
    brew install python3 git curl wget
fi

# Configure Git
git config --global user.name "DevOps Team"
git config --global user.email "devops@example.com"

echo "✓ DevOps environment setup complete!"
```

**Explanation**: `uname -s` detects operating system. Conditional branches execute appropriate package manager. Automates repetitive setup tasks.

---

## Solution 9: Diagnostic Script

```bash
#!/bin/bash
# diagnose.sh

echo "=== DevOps Environment Diagnostics ==="

# Test Git
echo -n "Git user: "
git config --global user.name

# Test Python imports
python3 -c "import sys, json; print('✓ Python core modules OK')" 2>/dev/null || echo "✗ Python issue"

# Test network
ping -c 1 8.8.8.8 &>/dev/null && echo "✓ Network OK" || echo "✗ No internet"

# List installed versions
echo -e "\n=== Installed Versions ==="
python3 --version
git --version
curl --version | head -n1
```

**Explanation**: Runs diagnostic checks sequentially. `-c 1` limits ping to one attempt. `&>/dev/null` suppresses output for cleaner display.

---

## Solution 10: Backup and Restore Configurations

```bash
#!/bin/bash
# backup_restore.sh

BACKUP_DIR="$HOME/config-backup"
CONFIG_FILES=(".gitconfig" ".bashrc")

# Backup function
backup_configs() {
    mkdir -p $BACKUP_DIR
    for file in "${CONFIG_FILES[@]}"; do
        cp ~/$file $BACKUP_DIR/$file.bak 2>/dev/null
        echo "✓ Backed up $file"
    done
}

# Restore function
restore_configs() {
    for file in "${CONFIG_FILES[@]}"; do
        cp $BACKUP_DIR/$file.bak ~/$file 2>/dev/null && echo "✓ Restored $file"
    done
}

# Main logic
case "$1" in
    backup)  backup_configs ;;
    restore) restore_configs ;;
    *)       echo "Usage: $0 [backup|restore]" ;;
esac
```

**Explanation**: Functions encapsulate backup and restore logic. Case statement handles command-line arguments. `.bak` extension indicates backup files.
