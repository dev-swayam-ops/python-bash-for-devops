# Security and Compliance Scripts - Cheatsheet

## File Permissions

| Command | Purpose | Example |
|---------|---------|---------|
| `find / -perm -4000` | Find SUID files | `find / -perm -4000 2>/dev/null` |
| `find / -perm -2000` | Find SGID files | `find / -perm -2000 2>/dev/null` |
| `find / -perm -002` | Find world-writable | `find / -type f -perm -002 2>/dev/null` |
| `ls -l` | Show permissions | `ls -ld /etc/passwd` |
| `chmod 600` | Remove permissions | `chmod 600 /etc/shadow` |
| `chown root:root` | Change ownership | `chown root:root /etc/passwd` |

## User and Access Control

| Command | Purpose | Example |
|---------|---------|---------|
| `grep -v nologin /etc/passwd` | List enabled users | Show accounts with shell access |
| `awk -F: '$3==0'` /etc/passwd | Find root users | Identify UID 0 accounts |
| `visudo -c` | Validate sudoers | `visudo -c -f /etc/sudoers` |
| `sudo -l` | List sudo permissions | Show current user privileges |
| `getent group sudo` | List sudo group | Show group members |
| `w` | Logged-in users | Show active sessions |
| `last` | Login history | `last -n 5` for recent logins |
| `lastlog` | Last login per user | Show per-user history |

## SSH Security

| Command | Purpose | Example |
|---------|---------|---------|
| `grep Protocol /etc/ssh/sshd_config` | Check SSH version | Should be 2 |
| `grep PermitRootLogin` | Root login check | Should be no |
| `grep PasswordAuthentication` | Password auth | Should be no (key only) |
| `grep PubkeyAuthentication` | Key auth | Should be yes |
| `ssh-keygen -t ed25519` | Generate key | ED25519 is modern |
| `ssh-copy-id user@host` | Copy public key | Setup passwordless auth |
| `ssh -v` | SSH verbose | Debug connection issues |

## SSL/TLS Certificates

| Command | Purpose | Example |
|---------|---------|---------|
| `openssl x509 -in cert.pem -noout -text` | View certificate | Show all details |
| `openssl x509 -in cert.pem -noout -dates` | Check expiry | notBefore and notAfter |
| `openssl x509 -in cert.pem -noout -enddate` | Expiry only | Get notAfter field |
| `openssl req -x509 -newkey rsa:4096` | Create self-signed | Generate test cert |
| `openssl s_client -connect host:443` | Test TLS connection | Verify remote cert |
| `find /etc -name "*.crt"` | Find certs | Locate cert files |
| `cert-check domain.com` | Domain cert check | Verify web cert |

## Password Policy

| File/Command | Setting | Example |
|--------------|---------|---------|
| `/etc/login.defs` | PASS_MAX_DAYS | Should be ≤ 90 |
| `/etc/login.defs` | PASS_MIN_DAYS | Should be ≥ 1 |
| `/etc/login.defs` | PASS_MIN_LEN | Should be ≥ 8 |
| `/etc/login.defs` | PASS_WARN_AGE | Warn days before expire |
| `chage -l user` | User password aging | Show individual settings |
| `chage -M 90 user` | Set max age | Enforce policy |
| `grep ^LOGIN_DEFS /etc/login.defs` | View all policies | Show configuration |

## File Integrity and Hashing

| Command | Purpose | Example |
|---------|---------|---------|
| `md5sum file` | MD5 checksum | `md5sum /etc/passwd` |
| `sha256sum file` | SHA256 checksum | More secure than MD5 |
| `md5sum -c baseline.txt` | Verify against baseline | Check multiple files |
| `find /path -type f -exec md5sum {} \;` | Hash all files | Baseline creation |
| `debsums -c` | Debian package verification | Check package files |
| `rpm -V package` | RPM package verification | Verify package integrity |

## Audit and Logging

| Command | Purpose | Example |
|---------|---------|---------|
| `ausearch` | Search audit logs | `ausearch -m USER_LOGIN` |
| `tail -f /var/log/auth.log` | Watch auth logs | Monitor login attempts |
| `grep FAILED /var/log/auth.log` | Failed logins | Find attack patterns |
| `grep ACCEPTED /var/log/auth.log` | Successful logins | Show valid access |
| `auditctl -l` | List audit rules | Show monitoring config |
| `auditctl -w /etc/passwd -p wa` | Monitor file | Watch for changes |
| `journalctl -u service` | Service logs | Check systemd logs |

## Network and Ports

| Command | Purpose | Example |
|---------|---------|---------|
| `ss -tlnp` | List listening ports | Show services |
| `netstat -tlnp` | Legacy port listing | Alternative to ss |
| `lsof -i` | Open connections | Show all network usage |
| `nmap -sV localhost` | Port scanning | Identify services |
| `firewall-cmd --list-all` | Firewall rules | Show open ports |
| `sudo ufw status` | UFW status | Ubuntu firewall |
| `nc -zv host port` | Port test | Check if port open |

## Processes and Services

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | All processes | Show full details |
| `ps -ef --forest` | Process tree | Show parent-child |
| `pgrep -u user` | User processes | Find by user |
| `ps -eo uid,pid,cmd` | UID and command | Show process ownership |
| `systemctl list-units --type=service` | System services | Show all services |
| `systemctl status service` | Service status | Check service health |
| `journalctl -xe` | Recent errors | Show systemd failures |

## Compliance and Standards

| Standard | Focus | Check Method |
|----------|-------|--------------|
| CIS Benchmark | Security hardening | cis-audit tools |
| NIST | Federal standards | manual checklist |
| PCI-DSS | Payment security | openscap/lynis |
| SOC2 | Service compliance | audit framework |
| HIPAA | Healthcare data | specific requirements |

## Common Findings and Fixes

| Issue | Finding | Fix |
|-------|---------|-----|
| World-writable files | `/tmp` world-writable | Normal, ok for /tmp |
| Root logins | SSH root allowed | Set PermitRootLogin no |
| Weak ciphers | Old SSH ciphers | Disable in sshd_config |
| Expiring certs | Cert expires soon | Renew before expiry |
| SUID binaries | Excessive SUID files | Review necessity |
| Sudo without password | NOPASSWD entries | Remove risky entries |

## Quick Audit Script Template

```bash
#!/bin/bash
echo "=== Quick Security Audit ==="
echo "1. File permissions:"
find / -perm -002 -type f 2>/dev/null | wc -l
echo "2. Enabled users:"
grep -v 'nologin\|false' /etc/passwd | wc -l
echo "3. SSH root login:"
grep PermitRootLogin /etc/ssh/sshd_config
echo "4. Listening ports:"
ss -tln | grep LISTEN | wc -l
echo "5. Recent logins:"
lastlog | head -3
```
