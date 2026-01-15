# Setup and Tooling for DevOps

## What You'll Learn
- Setting up a DevOps development environment
- Installing essential tools (Git, Docker, Python, Bash)
- Configuring your system for automation scripts
- Validating tool installations
- Troubleshooting common setup issues

## Prerequisites
- Windows 10+ or Linux/macOS
- Administrator access (for installations)
- Internet connection
- Text editor or IDE (VS Code recommended)

## Key Concepts
- **Package Managers**: Tools that install and manage software (apt, brew, choco)
- **PATH Environment Variable**: System variable that tells OS where to find executable programs
- **Dependencies**: Software that other programs need to work
- **Version Management**: Keeping tools updated and compatible

## Hands-on Lab: Setting Up Your DevOps Toolkit

### Step 1: Verify System Python
```bash
# Check Python installation
python --version

# Expected output:
# Python 3.9.0 (or higher)
```

### Step 2: Install Essential Tools (Linux/macOS)
```bash
# On Ubuntu/Debian
sudo apt update
sudo apt install -y git curl wget build-essential

# On macOS
brew install git curl wget

# Verify installations
git --version
curl --version
wget --version
```

### Step 3: Verify Git Configuration
```bash
# Set your Git identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Verify configuration
git config --global --list
```

### Step 4: Create Project Directory
```bash
# Create a workspace directory
mkdir -p ~/devops-workspace
cd ~/devops-workspace

# Expected output: You're now in the devops-workspace directory
```

### Step 5: Test Environment Variables
```bash
# Check PATH variable
echo $PATH

# Expected output: Multiple directory paths separated by colons
# Should include /usr/local/bin, /usr/bin, etc.
```

## Validation Checklist
- [ ] Python 3.8+ installed: `python --version`
- [ ] Git installed: `git --version`
- [ ] Curl installed: `curl --version`
- [ ] Workspace directory created: `ls ~/devops-workspace`
- [ ] Git configured: `git config --global user.name`

## Cleanup
```bash
# Remove test workspace (optional)
rm -rf ~/devops-workspace
```

## Common Mistakes
1. **Python not in PATH**: Restart terminal after installation
2. **Wrong Git user configured**: Check with `git config --global user.name`
3. **Mixing Python 2 and 3**: Always use `python3` explicitly if both installed
4. **Forgetting sudo for installations**: Linux package managers often require elevation

## Troubleshooting

### "Command not found: python"
- **Cause**: Python not installed or not in PATH
- **Solution**: Reinstall Python and ensure "Add to PATH" is checked during installation

### "Permission denied" for apt-get
- **Cause**: Missing sudo privileges
- **Solution**: Use `sudo apt-get` or add your user to sudoers

### Git configuration not persisting
- **Cause**: Configured for local project instead of global
- **Solution**: Use `--global` flag: `git config --global user.name "Name"`

## Next Steps
1. Move to [01-bash-basics](../01-bash-basics) to learn shell scripting
2. Set up version control with a Git repository
3. Configure your code editor with extensions for automation
