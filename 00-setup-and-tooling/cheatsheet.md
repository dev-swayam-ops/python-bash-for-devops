# Setup and Tooling: Cheatsheet

## System Information Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `uname -s` | Detect OS type | `uname -s` → Linux/Darwin |
| `uname -m` | Check system architecture | `uname -m` → x86_64 |
| `whoami` | Display current user | `whoami` → username |
| `pwd` | Print working directory | `pwd` → /home/user |

## Python Setup

| Command | Purpose | Example |
|---------|---------|---------|
| `python --version` | Check Python version | `python --version` → Python 3.9.0 |
| `which python` | Locate Python executable | `which python` → /usr/bin/python3 |
| `python -m pip --version` | Check pip version | `python -m pip --version` → pip 21.0 |
| `pip install package` | Install Python package | `pip install requests` |

## Git Configuration

| Command | Purpose | Example |
|---------|---------|---------|
| `git config --global user.name` | Set global Git username | `git config --global user.name "John"` |
| `git config --global user.email` | Set global Git email | `git config --global user.email "john@example.com"` |
| `git config --global --list` | Display all global config | `git config --global --list` |
| `git --version` | Check Git version | `git --version` → git version 2.x.x |

## Package Management (Linux)

| Command | Purpose | Example |
|---------|---------|---------|
| `sudo apt update` | Update package list | `sudo apt update` |
| `sudo apt install package` | Install software | `sudo apt install git` |
| `sudo apt remove package` | Uninstall software | `sudo apt remove git` |
| `apt list --installed` | List installed packages | `apt list --installed \| grep git` |

## Package Management (macOS)

| Command | Purpose | Example |
|---------|---------|---------|
| `brew install package` | Install via Homebrew | `brew install git` |
| `brew list` | List installed packages | `brew list` |
| `brew update` | Update Homebrew | `brew update` |
| `brew uninstall package` | Remove package | `brew uninstall git` |

## Environment Variables

| Command | Purpose | Example |
|---------|---------|---------|
| `echo $PATH` | Display PATH variable | `echo $PATH` |
| `export VAR=value` | Set environment variable | `export MYVAR="hello"` |
| `env` | Display all variables | `env` |
| `env \| grep PATH` | Filter specific variable | `env \| grep PATH` |

## File and Directory Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `mkdir -p dir/subdir` | Create nested directories | `mkdir -p ~/devops/scripts` |
| `ls -la` | List files with details | `ls -la` |
| `cp source dest` | Copy file | `cp file.txt backup.txt` |
| `mv source dest` | Move/rename file | `mv old.txt new.txt` |
| `chmod +x script.sh` | Make script executable | `chmod +x setup.sh` |

## Verification Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `command -v tool` | Check if tool exists | `command -v docker` |
| `which tool` | Find tool location | `which python` |
| `type command` | Show command type | `type cd` |
| `curl --version` | Check curl version | `curl --version` |

## Network Diagnostics

| Command | Purpose | Example |
|---------|---------|---------|
| `ping host` | Test connectivity | `ping 8.8.8.8` |
| `curl -I url` | Test HTTP response | `curl -I https://example.com` |
| `wget url` | Download file | `wget https://example.com/file.zip` |
| `netstat -tuln` | View network connections | `netstat -tuln \| grep LISTEN` |

## Common Shortcuts

| Shortcut | Meaning |
|----------|---------|
| `~` | Home directory |
| `.` | Current directory |
| `..` | Parent directory |
| `-` | Previous directory |
| `*` | Wildcard (any characters) |
| `?` | Single character wildcard |
| `&>` | Redirect both stdout and stderr |
| `>>` | Append to file |
