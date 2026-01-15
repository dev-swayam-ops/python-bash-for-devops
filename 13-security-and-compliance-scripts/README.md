# Security and Compliance Scripts

## What You'll Learn

- Implement file and system security audits
- Scan for vulnerabilities and weak configurations
- Generate compliance reports (CIS, NIST, PCI-DSS)
- Automate certificate and credential management
- Perform security baseline checks
- Detect suspicious activities and anomalies

## Prerequisites

- Basic bash scripting (modules 01-02)
- File permissions and ownership understanding
- Linux security concepts (users, groups, sudoers)
- Network basics (ports, services)
- Optional: OpenSSL, GPG, auditd experience

## Key Concepts

### File Security
- SGID, SUID bits detection
- World-writable file scanning
- File permission audits
- Ownership verification

### Access Control
- User privilege reviews
- Sudo configuration audits
- SSH key management
- Password policy enforcement

### Compliance Checks
- CIS Benchmark compliance
- Security baseline verification
- Service hardening validation
- Network security audits

### Credential Management
- SSL/TLS certificate expiry
- Secret rotation automation
- Encrypted credential storage
- API key vulnerability detection

## Hands-on Lab

### Step 1: Create File Security Audit Script
```bash
#!/bin/bash
echo "=== File Security Audit ===" 
echo "SUID/SGID files:"
find / -type f \( -perm -4000 -o -perm -2000 \) 2>/dev/null | head -3
echo "World-writable files:"
find / -type f -perm -002 2>/dev/null | head -3
```

**Expected Output:**
```
=== File Security Audit === 
SUID/SGID files:
/usr/bin/sudo
/usr/bin/passwd
World-writable files:
/tmp
```

### Step 2: Check SSH Configuration
```bash
echo "=== SSH Security Audit ==="
grep "^PermitRootLogin\|^PasswordAuthentication" /etc/ssh/sshd_config || echo "Using defaults"
```

### Step 3: Monitor User Access
```bash
echo "=== Logged-in Users ==="
w | tail -n +3
```

### Step 4: Check Certificate Expiry
```bash
openssl x509 -in /etc/ssl/certs/ca-certificates.crt -noout -dates 2>/dev/null || echo "No certificate found"
```

### Step 5: Generate Compliance Report
```bash
echo "=== Security Report ===" > report.txt
echo "Generated: $(date)" >> report.txt
echo "SUID files: $(find / -type f -perm -4000 2>/dev/null | wc -l)" >> report.txt
cat report.txt
```

## Validation

- [ ] File security checks execute
- [ ] SSH config readable
- [ ] User access monitored
- [ ] Certificate details extracted
- [ ] Compliance report generated

## Cleanup

```bash
rm -f report.txt
```

## Common Mistakes

- Not running with sudo for restricted file access
- Using wrong certificate paths
- Ignoring symlinks in file searches
- Not handling permission denied errors

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Permission denied | Use `sudo` |
| find slow | Add `-maxdepth 3` |
| Cert not found | Check `/etc/pki/` on RHEL |

## Next Steps

- Integrate with continuous monitoring
- Set up automated compliance scanning
- Create security baselines
- Implement alerting on violations
