# Security and Compliance Scripts - Exercises

## Exercise 1: File Permission Auditor
Create a script that scans for files with overly permissive permissions (777, 666, world-writable). List the top 10 results and their owners.

**Requirements:**
- Use find command with permission checking
- Show owner and permissions
- Handle errors gracefully

## Exercise 2: User Account Review
Write a script to list all enabled user accounts, their UID, GID, and home directories. Identify any accounts with UID 0 (root privileges).

**Requirements:**
- Parse /etc/passwd file
- Show account details
- Flag accounts with elevated privileges
- Calculate total enabled accounts

## Exercise 3: SSH Security Audit
Create a script that checks SSH configuration for security best practices: Protocol 2, PermitRootLogin disabled, PasswordAuthentication disabled.

**Requirements:**
- Read /etc/ssh/sshd_config
- Check each security setting
- Report status (pass/fail/default)
- Show recommendations

## Exercise 4: SSL Certificate Expiry Monitor
Write a script to scan certificate files and report expiry dates. Alert if any certificate expires within 30 days.

**Requirements:**
- Find all .crt and .pem files
- Extract expiry dates with openssl
- Calculate days until expiry
- Warn on expiring certs

## Exercise 5: Sudo Configuration Auditor
Create a script that analyzes /etc/sudoers file for security issues: users with NOPASSWD, ALL=(ALL) permissions, and command restrictions.

**Requirements:**
- Parse sudoers file (use visudo -c to validate)
- Identify risky entries
- Show who can run what
- List all sudo-enabled users

## Exercise 6: Password Policy Enforcer
Write a script to check password policy in /etc/login.defs: password expiry (PASS_MAX_DAYS), minimum age (PASS_MIN_DAYS), length (PASS_MIN_LEN).

**Requirements:**
- Extract policy values
- Compare against standards (max 90 days, min 1 day, min 8 chars)
- Show compliance status
- Generate remediation commands

## Exercise 7: Running Processes Security Review
Create a script that lists all running processes with their UID, PID, and command. Flag processes running as root that shouldn't be.

**Requirements:**
- Parse ps output
- Show process ownership
- Identify suspicious root processes
- List service accounts

## Exercise 8: File Integrity Monitor
Write a script that creates MD5 checksums of critical files (/etc/passwd, /etc/shadow, /etc/sudoers) and compares them to detect changes.

**Requirements:**
- Generate initial checksums
- Compare against stored values
- Alert on changes
- Store baseline securely

## Exercise 9: Network Security Audit
Create a script that checks listening ports, their associated processes, and services. Identify exposed services on public interfaces.

**Requirements:**
- List all listening ports (netstat/ss)
- Show service names
- Identify public interfaces
- Flag unnecessary exposed services

## Exercise 10: Compliance Report Generator
Write a comprehensive script that combines all audits (files, users, SSH, certs, sudoers, passwords) and generates an HTML compliance report with pass/fail indicators.

**Requirements:**
- Run multiple security checks
- Aggregate results
- Generate timestamped report
- Include summary scores
- Create HTML dashboard
