# Scripts - Exercises

## Exercise 1: Logging Utility Module
Create a reusable logging module with functions for different log levels (INFO, WARN, ERROR, DEBUG), timestamps, and color output. Can be sourced in other scripts.

**Requirements:**
- Log to stdout for INFO/WARN
- Log to stderr for ERROR
- Include timestamps in all logs
- Support color output (optional)
- Function: log_info, log_warn, log_error, log_debug
- Support DEBUG environment variable

## Exercise 2: Health Check Utility
Build a utility script that checks if services are healthy. Tests HTTP endpoints, TCP ports, and process existence with retry logic and timeout support.

**Requirements:**
- Check HTTP endpoints with curl
- Check TCP port connectivity
- Check if process is running by name
- Implement retry with exponential backoff
- Support timeout parameter
- Return proper exit codes
- Provide detailed status output

## Exercise 3: Configuration File Parser
Create a script that reads configuration files (INI, YAML-like format) and exports variables. Support defaults and validation.

**Requirements:**
- Parse key=value format
- Support comments (# prefix)
- Handle empty lines
- Export variables
- Support default values
- Validate required keys present
- Show loaded config

## Exercise 4: Backup and Restore Utilities
Build utility functions for backing up and restoring common resources: databases, files, configs, Docker volumes.

**Requirements:**
- Backup function for files/directories
- Backup function for Docker volumes
- Backup function for databases
- Restore function with validation
- Support incremental backups
- Compression support
- Cleanup old backups

## Exercise 5: Process Management Utilities
Create utilities to manage processes: start/stop with verification, healthcheck loop, automatic restart on failure, graceful shutdown.

**Requirements:**
- Start process with logging
- Stop process gracefully (SIGTERM, then SIGKILL)
- Check if process running
- Restart if crashed
- Background process management
- PID file handling
- Log process output

## Exercise 6: Git Operations Wrapper
Build a script wrapper around git operations with additional safety checks: verify branch, check uncommitted changes, prevent accidents.

**Requirements:**
- Verify current branch before operations
- Check for uncommitted changes
- Confirm before destructive operations
- Support common workflows (push, pull, branch)
- Log all git operations
- Handle merge conflicts
- Prevent force pushes to main

## Exercise 7: Environment Management Helper
Create utilities to manage multiple environments (dev, staging, prod): load env vars, validate setup, switch contexts.

**Requirements:**
- Load environment-specific variables
- Validate environment setup
- Switch between environments
- Show current environment
- Check environment readiness
- Support multiple environments
- Validate service connectivity

## Exercise 8: Metrics Collection and Reporting
Build utilities to collect system metrics (CPU, memory, disk, network), store them, and generate reports/trends.

**Requirements:**
- Collect current metrics
- Store metrics with timestamps
- Generate trend reports
- Compare against baselines
- Alert on threshold violations
- Export to CSV/JSON
- Create graphs/visualizations

## Exercise 9: Deployment Helper Functions
Create reusable functions for common deployment tasks: build artifacts, push to registry, deploy, rollback, validate.

**Requirements:**
- Build Docker image with versioning
- Push to registry with tagging
- Deploy with config management
- Validate deployment
- Rollback previous version
- Health checks post-deploy
- Support multiple registries

## Exercise 10: Complete DevOps Toolkit Script
Create comprehensive main script that provides CLI interface to all utilities: logging, health checks, backups, deployments, monitoring.

**Requirements:**
- Main script with subcommands
- Source all utility modules
- Provide help for each command
- Support argument parsing
- Configuration file integration
- Comprehensive error handling
- Audit logging of all operations
- Exit codes for automation
