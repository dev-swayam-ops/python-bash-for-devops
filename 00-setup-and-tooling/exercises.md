# Setup and Tooling: Exercises

## Exercise 1 (Easy): Verify Python Installation
Check your Python version and installation path. Create a simple script that prints "Python is ready for DevOps!".

**Hint**: Use `python --version` and `which python`

---

## Exercise 2 (Easy): Git Configuration Validation
Configure Git with your username and email globally, then verify the configuration is applied.

**Hint**: Use `git config --global` commands

---

## Exercise 3 (Easy): Create Project Directory Structure
Create a directory structure for DevOps projects:
```
devops-projects/
├── bash-scripts/
├── python-scripts/
└── configs/
```

**Hint**: Use `mkdir -p` with nested paths

---

## Exercise 4 (Easy): Environment Variable Check
Print all environment variables related to Python and system paths. Save output to a file.

**Hint**: Use `echo $PATH`, `env | grep PYTHON`

---

## Exercise 5 (Medium): Install and Verify Multiple Tools
Install three tools (curl, wget, git) and create a verification script that checks all are installed and display their versions.

**Hint**: Use conditional statements and version flags

---

## Exercise 6 (Medium): PATH Troubleshooting
Create a simple bash script that adds a custom directory to your PATH and verifies the addition persists in a new shell session.

**Hint**: Modify `.bashrc` or `.zshrc` and test with a new terminal window

---

## Exercise 7 (Medium): Validate System Requirements
Write a script that checks if your system meets minimum DevOps requirements (Python 3.8+, Git, Curl, 4GB RAM).

**Hint**: Use version comparisons and `free` or `vm_stat` commands

---

## Exercise 8 (Medium): Create a Setup Automation Script
Write a bash script that automates tool installation and configuration for a new DevOps environment.

**Hint**: Detect OS type and run appropriate package manager commands

---

## Exercise 9 (Medium): Diagnose Configuration Issues
Create a diagnostic script that tests Git credentials, Python imports, and network connectivity (ping to a public DNS).

**Hint**: Use `git config`, `python -c`, and `ping`

---

## Exercise 10 (Medium): Backup and Restore Configurations
Write a script that backs up critical configuration files (.gitconfig, .bashrc) to a backup directory and restores them.

**Hint**: Use `cp`, `tar`, or `rsync` for backup operations
