# Security and Compliance Scripts - Solutions

## Solution 1: File Permission Auditor

```bash
#!/bin/bash
# audit_file_permissions.sh
echo "=== File Permission Audit ==="
find / -type f \( -perm -002 -o -perm 777 -o -perm 666 \) 2>/dev/null | while read file; do
  ls -lh "$file" | awk '{print $1, $3, $9}' | head -10
done
echo "Total world-writable files: $(find / -type f -perm -002 2>/dev/null | wc -l)"
```

**Explanation:** Searches for files with overly permissive permissions (777, 666, world-writable). Uses `find -perm` with logical operators and displays owner/permissions with `ls -l`.

---

## Solution 2: User Account Review

```bash
#!/bin/bash
# audit_user_accounts.sh
echo "=== Enabled User Accounts ==="
awk -F: '$7 !~ /nologin|false/ {print $1, "UID:" $3, "GID:" $4, "HOME:" $6}' /etc/passwd
echo ""
echo "=== Root Privilege Accounts (UID=0) ==="
awk -F: '$3 == 0 {print $1}' /etc/passwd
echo ""
echo "Total enabled accounts: $(grep -v 'nologin\|false' /etc/passwd | wc -l)"
```

**Explanation:** Parses `/etc/passwd` to extract enabled accounts (shell not nologin/false), displays UID/GID/home. Identifies all UID 0 accounts (including root).

---

## Solution 3: SSH Security Audit

```bash
#!/bin/bash
# audit_ssh_security.sh
echo "=== SSH Security Audit ==="
SSHD_CONFIG="/etc/ssh/sshd_config"

check_setting() {
  local setting=$1
  local expected=$2
  local current=$(grep "^$setting" $SSHD_CONFIG | awk '{print $2}')
  [ -z "$current" ] && current="default"
  [ "$current" = "$expected" ] && echo "✓ $setting = $current" || echo "✗ $setting = $current (expected: $expected)"
}

check_setting "Protocol" "2"
check_setting "PermitRootLogin" "no"
check_setting "PasswordAuthentication" "no"
check_setting "PubkeyAuthentication" "yes"
```

**Explanation:** Checks critical SSH settings using grep and awk. Reports pass/fail for each security configuration. Shows defaults if not explicitly set.

---

## Solution 4: SSL Certificate Expiry Monitor

```bash
#!/bin/bash
# monitor_cert_expiry.sh
echo "=== Certificate Expiry Check ==="
WARNING_DAYS=30

find /etc/ssl /etc/pki -name "*.crt" -o -name "*.pem" 2>/dev/null | while read cert; do
  expiry=$(openssl x509 -in "$cert" -noout -enddate 2>/dev/null | cut -d= -f2)
  [ -n "$expiry" ] && {
    days=$(($(date -d "$expiry" +%s 2>/dev/null || echo 0) - $(date +%s)) / 86400)
    if [ $days -lt 0 ]; then
      echo "EXPIRED: $cert ($(date -d "$expiry" '+%Y-%m-%d'))"
    elif [ $days -lt $WARNING_DAYS ]; then
      echo "WARNING: $cert expires in $days days"
    fi
  }
done
```

**Explanation:** Searches for certificate files, uses openssl to extract expiry dates, calculates days remaining, alerts on expiring/expired certs within warning threshold.

---

## Solution 5: Sudo Configuration Auditor

```bash
#!/bin/bash
# audit_sudoers.sh
echo "=== Sudoers Configuration Audit ==="
visudo -c -f /etc/sudoers 2>&1 | head -1

echo "=== Users with NOPASSWD ==="
grep -E "NOPASSWD|ALL=\(ALL\)" /etc/sudoers 2>/dev/null | grep -v '^#'

echo "=== Sudo-enabled Users ==="
awk -F: '{print $1}' /etc/sudoers.d/* 2>/dev/null | sort -u

echo "=== User Sudo Privileges ==="
sudo -l 2>/dev/null || echo "Run as root to see actual permissions"
```

**Explanation:** Uses visudo to validate sudoers syntax. Searches for risky entries (NOPASSWD, ALL privileges). Lists sudo-enabled users from sudoers.d directory.

---

## Solution 6: Password Policy Enforcer

```bash
#!/bin/bash
# check_password_policy.sh
echo "=== Password Policy Audit ==="

extract_policy() {
  local key=$1
  grep "^$key" /etc/login.defs | awk '{print $2}'
}

PASS_MAX=$(extract_policy "PASS_MAX_DAYS")
PASS_MIN=$(extract_policy "PASS_MIN_DAYS")
PASS_LEN=$(extract_policy "PASS_MIN_LEN")

echo "Password max age: $PASS_MAX days (should be <= 90)"
echo "Password min age: $PASS_MIN days (should be >= 1)"
echo "Password min length: $PASS_LEN chars (should be >= 8)"

[ $PASS_MAX -gt 90 ] && echo "ACTION: sudo sed -i 's/^PASS_MAX_DAYS.*/PASS_MAX_DAYS   90/' /etc/login.defs"
[ $PASS_MIN -lt 1 ] && echo "ACTION: sudo sed -i 's/^PASS_MIN_DAYS.*/PASS_MIN_DAYS    1/' /etc/login.defs"
[ $PASS_LEN -lt 8 ] && echo "ACTION: sudo sed -i 's/^PASS_MIN_LEN.*/PASS_MIN_LEN     8/' /etc/login.defs"
```

**Explanation:** Extracts password policy from `/etc/login.defs`, compares against security standards, provides sed commands for remediation.

---

## Solution 7: Running Processes Security Review

```bash
#!/bin/bash
# audit_processes.sh
echo "=== Processes Running as Root ==="
ps aux | awk '$1 == "root" && $NF !~ /init|systemd|kernel|sshd|cron/ {print $2, $NF}'

echo "=== Service Accounts (UID < 1000, not root) ==="
awk -F: '$3 < 1000 && $3 != 0 {print $1, "UID:" $3}' /etc/passwd

echo "=== Total root processes: $(ps aux | awk '$1 == "root"' | wc -l) ==="
echo "=== Total user processes: $(ps aux | awk '$1 != "root"' | wc -l) ==="
```

**Explanation:** Uses ps to list processes, filters by UID. Identifies root-running processes for review. Shows service account distribution.

---

## Solution 8: File Integrity Monitor

```bash
#!/bin/bash
# file_integrity_monitor.sh
BASELINE="/var/lib/integrity_baseline.txt"
CRITICAL_FILES=("/etc/passwd" "/etc/shadow" "/etc/sudoers")

if [ "$1" = "baseline" ]; then
  echo "=== Creating baseline ==="
  for f in "${CRITICAL_FILES[@]}"; do
    [ -f "$f" ] && md5sum "$f"
  done | tee $BASELINE
  echo "Baseline saved to $BASELINE"
else
  echo "=== Checking integrity ==="
  md5sum -c $BASELINE 2>/dev/null || echo "ALERT: Files have changed!"
fi
```

**Explanation:** Creates baseline MD5 checksums of critical files. Compare mode detects any changes. Useful for detecting unauthorized modifications.

---

## Solution 9: Network Security Audit

```bash
#!/bin/bash
# audit_network.sh
echo "=== Listening Services ==="
ss -tlnp 2>/dev/null | awk 'NR>1 {print $4, $7}' | column -t

echo "=== Public Interface Services ==="
ss -tlnp 2>/dev/null | grep "0.0.0.0\|::" | awk '{print $4, $7}'

echo "=== Open Ports Count: $(ss -tln | grep LISTEN | wc -l) ==="

echo "=== Non-standard Services (investigating further) ==="
netstat -tln 2>/dev/null | grep "LISTEN" | grep -v "22\|80\|443"
```

**Explanation:** Uses ss/netstat to list listening ports and associated processes. Identifies services on public interfaces. Flags non-standard exposed ports.

---

## Solution 10: Compliance Report Generator

```bash
#!/bin/bash
# generate_compliance_report.sh
REPORT="compliance_report_$(date +%Y%m%d_%H%M%S).html"

cat > $REPORT << 'EOF'
<html>
<head><title>Security Compliance Report</title></head>
<body style="font-family: Arial; margin: 20px;">
<h1>Security Compliance Report</h1>
<p>Generated: $(date)</p>
<h2>Audit Results</h2>
<table border="1" cellpadding="5">
<tr><th>Check</th><th>Status</th><th>Details</th></tr>
EOF

# File permissions
WRITABLE=$(find / -type f -perm -002 2>/dev/null | wc -l)
[ $WRITABLE -eq 0 ] && STATUS="PASS" || STATUS="FAIL"
echo "<tr><td>World-writable files</td><td>$STATUS</td><td>Count: $WRITABLE</td></tr>" >> $REPORT

# User accounts
ROOT_ONLY=$(awk -F: '$3 == 0' /etc/passwd | wc -l)
[ $ROOT_ONLY -eq 1 ] && STATUS="PASS" || STATUS="FAIL"
echo "<tr><td>Root UID=0 accounts</td><td>$STATUS</td><td>Count: $ROOT_ONLY</td></tr>" >> $REPORT

# SSH security
SSHROOT=$(grep "PermitRootLogin no" /etc/ssh/sshd_config | wc -l)
[ $SSHROOT -gt 0 ] && STATUS="PASS" || STATUS="FAIL"
echo "<tr><td>SSH root login disabled</td><td>$STATUS</td><td>Config check</td></tr>" >> $REPORT

echo "</table></body></html>" >> $REPORT
echo "Report saved to $REPORT"
```

**Explanation:** Combines all audits into HTML report. Generates pass/fail status for each check. Creates timestamped reports for historical tracking and compliance documentation.
