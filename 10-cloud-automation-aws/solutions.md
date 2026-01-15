# Cloud Automation Solutions

## Solution 1: AWS CLI Configuration and Verification

```bash
#!/bin/bash
# verify_aws_credentials.sh - Verify AWS CLI and credentials

echo "=== AWS Credentials Verification ==="

# Check if AWS CLI installed
if ! command -v aws &> /dev/null; then
    echo "✗ AWS CLI not installed"
    echo "  Install with: pip install awscli"
    exit 1
fi

echo "✓ AWS CLI found: $(aws --version)"

# Verify credentials
if aws sts get-caller-identity > /tmp/aws_identity.json 2>&1; then
    USER_ID=$(jq -r '.UserId' /tmp/aws_identity.json)
    ACCOUNT=$(jq -r '.Account' /tmp/aws_identity.json)
    ARN=$(jq -r '.Arn' /tmp/aws_identity.json)
    
    echo "✓ AWS Credentials Valid"
    echo "  Account: $ACCOUNT"
    echo "  User ID: $USER_ID"
    echo "  ARN: $ARN"
    exit 0
else
    echo "✗ AWS Credentials Invalid"
    echo "  Run: aws configure"
    exit 1
fi
```

**Explanation**: Checks AWS CLI installation, verifies credentials with `aws sts get-caller-identity`, parses JSON response to display account information.

---

## Solution 2: S3 Bucket List and Info

```bash
#!/bin/bash
# s3_bucket_info.sh - List S3 buckets with details

echo "=== S3 Bucket Information ==="
echo ""

# Get bucket list
buckets=$(aws s3 ls | awk '{print $NF}')
bucket_count=$(echo "$buckets" | wc -l)

if [ -z "$buckets" ]; then
    echo "No S3 buckets found"
    exit 0
fi

echo "Total Buckets: $bucket_count"
echo ""

total_size=0

# Get details for each bucket
for bucket in $buckets; do
    creation_date=$(aws s3api list-buckets \
        --query "Buckets[?Name=='$bucket'].CreationDate" \
        --output text)
    
    # Get bucket size
    size=$(aws s3 ls "s3://$bucket" --summarize --human-readable \
        --recursive 2>/dev/null | grep "Total Size" | awk '{print $3}')
    
    [ -z "$size" ] && size="N/A"
    
    echo "Bucket: $bucket"
    echo "  Created: $creation_date"
    echo "  Size: $size"
    echo ""
done

echo "✓ Bucket list complete"
```

**Explanation**: Uses `aws s3 ls` to list buckets, queries creation dates with `aws s3api`, calculates bucket sizes with recursive summarize.

---

## Solution 3: EC2 Instance Lister

```bash
#!/bin/bash
# ec2_instance_list.sh - List EC2 instances with details

echo "=== EC2 Instances ==="
echo ""

# Get current region
REGION=$(aws configure get region)
echo "Region: $REGION"
echo ""

# Query instances with details
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,LaunchTime,PublicIpAddress]' \
    --output table

echo ""

# Count instances by state
echo "Instance Count by State:"
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].State.Name' \
    --output text | tr '\t' '\n' | sort | uniq -c

echo ""
echo "✓ Instance list complete"
```

**Explanation**: Uses JMESPath queries to format output as table, counts instances by state using sort/uniq.

---

## Solution 4: Create and Delete S3 Bucket

```bash
#!/bin/bash
# s3_bucket_manager.sh - Create/configure/delete S3 bucket

BUCKET_NAME=$1

if [ -z "$BUCKET_NAME" ]; then
    echo "Usage: $0 <bucket_name>"
    exit 1
fi

# Validate bucket name (lowercase, 3-63 chars)
if [[ ! $BUCKET_NAME =~ ^[a-z0-9.-]{3,63}$ ]]; then
    echo "✗ Invalid bucket name (must be lowercase, 3-63 chars)"
    exit 1
fi

echo "Creating bucket: $BUCKET_NAME"

# Create bucket
if aws s3 mb "s3://$BUCKET_NAME" 2>/dev/null; then
    echo "✓ Bucket created"
    
    # Enable versioning
    aws s3api put-bucket-versioning \
        --bucket "$BUCKET_NAME" \
        --versioning-configuration Status=Enabled
    echo "✓ Versioning enabled"
    
    # Get configuration
    echo ""
    echo "Bucket Configuration:"
    aws s3api get-bucket-versioning --bucket "$BUCKET_NAME"
    
else
    echo "✗ Bucket creation failed (may already exist)"
    exit 1
fi

# Delete prompt
echo ""
read -p "Delete bucket? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    aws s3 rb "s3://$BUCKET_NAME" --force
    echo "✓ Bucket deleted"
fi
```

**Explanation**: Validates bucket name format, creates bucket, enables versioning, displays configuration, prompts for deletion.

---

## Solution 5: Tag EC2 Instances by Name

```bash
#!/bin/bash
# tag_ec2_instances.sh - Add Name tags to instances

echo "=== EC2 Tagging Tool ==="
echo ""

# Find instances without Name tag
untagged=$(aws ec2 describe-instances \
    --filters "Name=instance-state-name,Values=running" \
    --query 'Reservations[*].Instances[?!Tags[?Key==`Name`]].[InstanceId,InstanceType]' \
    --output text)

if [ -z "$untagged" ]; then
    echo "✓ All running instances have Name tags"
    exit 0
fi

echo "Untagged Instances:"
echo "$untagged" | while read instance_id instance_type; do
    echo ""
    echo "Instance: $instance_id (Type: $instance_type)"
    read -p "Enter name tag: " name_tag
    
    if [ -n "$name_tag" ]; then
        aws ec2 create-tags \
            --resources "$instance_id" \
            --tags Key=Name,Value="$name_tag"
        echo "✓ Tagged: $name_tag"
    fi
done

# Verify tags
echo ""
echo "Current tags:"
aws ec2 describe-instances \
    --query 'Reservations[*].Instances[*].[InstanceId,Tags[?Key==`Name`].Value|[0]]' \
    --output table
```

**Explanation**: Finds instances without Name tag, prompts for names interactively, applies tags, verifies with describe-instances.

---

## Solution 6: S3 File Upload and Download Manager

```bash
#!/bin/bash
# s3_file_manager.sh - Upload/download files with verification

SOURCE_FILE=$1
BUCKET_NAME=$2
ACTION=${3:-upload}

if [ -z "$SOURCE_FILE" ] || [ -z "$BUCKET_NAME" ]; then
    echo "Usage: $0 <file> <bucket> [upload|download]"
    exit 1
fi

if [ "$ACTION" = "upload" ]; then
    if [ ! -f "$SOURCE_FILE" ]; then
        echo "✗ File not found: $SOURCE_FILE"
        exit 1
    fi
    
    SIZE=$(stat -f%z "$SOURCE_FILE" 2>/dev/null || stat -c%s "$SOURCE_FILE")
    echo "Uploading: $SOURCE_FILE ($SIZE bytes)"
    
    # Upload with progress
    aws s3 cp "$SOURCE_FILE" "s3://$BUCKET_NAME/" \
        --metadata "uploaded=$(date +%Y-%m-%d)"
    
    if [ $? -eq 0 ]; then
        ETAG=$(aws s3api head-object \
            --bucket "$BUCKET_NAME" \
            --key "$(basename $SOURCE_FILE)" \
            --query 'ETag' --output text)
        
        echo "✓ Upload complete"
        echo "  Object: $(basename $SOURCE_FILE)"
        echo "  ETag: $ETAG"
    fi
    
elif [ "$ACTION" = "download" ]; then
    echo "Downloading: s3://$BUCKET_NAME/$SOURCE_FILE"
    
    aws s3 cp "s3://$BUCKET_NAME/$SOURCE_FILE" "./$SOURCE_FILE"
    
    if [ $? -eq 0 ]; then
        echo "✓ Download complete"
        ls -lh "./$SOURCE_FILE"
    fi
fi
```

**Explanation**: Handles both upload and download, validates file exists, shows file size, verifies with ETag.

---

## Solution 7: RDS Database Management

```bash
#!/bin/bash
# rds_manager.sh - Manage RDS instances

echo "=== RDS Database Management ==="
echo ""

# List RDS instances
echo "RDS Instances:"
aws rds describe-db-instances \
    --query 'DBInstances[*].[DBInstanceIdentifier,Engine,DBInstanceStatus,DBInstanceClass,AllocatedStorage]' \
    --output table

echo ""

# Get backup info
echo "Recent Backups:"
aws rds describe-db-snapshots \
    --query 'DBSnapshots[*].[DBSnapshotIdentifier,DBInstanceIdentifier,SnapshotCreateTime,Status]' \
    --output table | head -10

echo ""

# List backup retention
echo "Backup Retention Policies:"
aws rds describe-db-instances \
    --query 'DBInstances[*].[DBInstanceIdentifier,BackupRetentionPeriod,PreferredBackupWindow]' \
    --output table
```

**Explanation**: Lists RDS instances with relevant attributes, shows recent snapshots, displays backup retention policies.

---

## Solution 8: CloudWatch Alarm Manager

```bash
#!/bin/bash
# cloudwatch_alarm_manager.sh - Manage CloudWatch alarms

echo "=== CloudWatch Alarms ==="
echo ""

# List alarms
aws cloudwatch describe-alarms \
    --query 'MetricAlarms[*].[AlarmName,StateValue,MetricName,Threshold]' \
    --output table

echo ""

# Count by state
echo "Alarms by State:"
aws cloudwatch describe-alarms \
    --query 'MetricAlarms[*].StateValue' \
    --output text | tr '\t' '\n' | sort | uniq -c

echo ""

# Create sample alarm
read -p "Create alarm for instance? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    read -p "Instance ID: " instance_id
    read -p "Alarm name: " alarm_name
    read -p "CPU threshold (default 80): " threshold
    
    threshold=${threshold:-80}
    
    aws cloudwatch put-metric-alarm \
        --alarm-name "$alarm_name" \
        --alarm-description "CPU alarm for $instance_id" \
        --metric-name CPUUtilization \
        --namespace AWS/EC2 \
        --statistic Average \
        --period 300 \
        --threshold "$threshold" \
        --comparison-operator GreaterThanThreshold \
        --dimensions Name=InstanceId,Value="$instance_id"
    
    echo "✓ Alarm created: $alarm_name"
fi
```

**Explanation**: Lists alarms with state and metrics, counts by state, interactively creates new alarm for EC2 instance.

---

## Solution 9: Security Group and VPC Inspector

```bash
#!/bin/bash
# security_inspector.sh - Analyze security groups

echo "=== Security Group Inspector ==="
echo ""

OUTPUT_FILE="/tmp/security_report_$(date +%s).txt"

# List security groups
echo "Security Groups:" > "$OUTPUT_FILE"
aws ec2 describe-security-groups \
    --query 'SecurityGroups[*].[GroupId,GroupName,VpcId]' \
    --output table >> "$OUTPUT_FILE"

echo "" >> "$OUTPUT_FILE"
echo "=== Risky Rules (0.0.0.0/0) ===" >> "$OUTPUT_FILE"

# Find overly permissive rules
aws ec2 describe-security-groups \
    --query "SecurityGroups[].{Name:GroupName,Rules:IpPermissions[?IpRanges[?CidrIp=='0.0.0.0/0']||Ipv6Ranges[?CidrIpv6=='::/0']]}" \
    --output json | jq '.[] | select(.Rules | length > 0)' >> "$OUTPUT_FILE"

echo "" >> "$OUTPUT_FILE"
echo "=== VPCs ===" >> "$OUTPUT_FILE"

aws ec2 describe-vpcs \
    --query 'Vpcs[*].[VpcId,CidrBlock,State]' \
    --output table >> "$OUTPUT_FILE"

echo "✓ Report saved: $OUTPUT_FILE"
cat "$OUTPUT_FILE"
```

**Explanation**: Lists security groups and VPCs, identifies overly permissive rules (0.0.0.0/0), generates and saves report.

---

## Solution 10: Complete Cloud Infrastructure Dashboard

```bash
#!/bin/bash
# cloud_dashboard.sh - Complete AWS infrastructure dashboard

REPORT_FILE="/tmp/aws_dashboard_$(date +%Y%m%d_%H%M%S).html"

cat > "$REPORT_FILE" << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>AWS Infrastructure Dashboard</title>
    <style>
        body { font-family: Arial; margin: 20px; background: #f5f5f5; }
        .container { max-width: 1200px; margin: 0 auto; background: white; padding: 20px; border-radius: 5px; }
        .section { margin: 20px 0; padding: 15px; border-left: 4px solid #0066cc; }
        .metric { display: inline-block; margin: 10px 20px; }
        .number { font-size: 24px; color: #0066cc; font-weight: bold; }
        table { border-collapse: collapse; width: 100%; margin-top: 10px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background: #f2f2f2; }
        .status-ok { color: green; }
        .status-alert { color: red; }
    </style>
</head>
<body>
<div class="container">
    <h1>AWS Infrastructure Dashboard</h1>
    <p>Generated: $(date)</p>
EOF

# EC2 Summary
ec2_count=$(aws ec2 describe-instances --query 'length(Reservations[*].Instances[])' --output text)
running=$(aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query 'length(Reservations[*].Instances[])' --output text)

cat >> "$REPORT_FILE" << EOF
    <div class="section">
        <h2>EC2 Instances</h2>
        <div class="metric">
            <div class="number">$ec2_count</div>
            <div>Total Instances</div>
        </div>
        <div class="metric">
            <div class="number status-ok">$running</div>
            <div>Running</div>
        </div>
    </div>
EOF

# S3 Summary
s3_count=$(aws s3 ls | wc -l)

cat >> "$REPORT_FILE" << EOF
    <div class="section">
        <h2>S3 Buckets</h2>
        <div class="metric">
            <div class="number">$s3_count</div>
            <div>Total Buckets</div>
        </div>
    </div>
EOF

# RDS Summary
rds_count=$(aws rds describe-db-instances --query 'length(DBInstances)' --output text)

cat >> "$REPORT_FILE" << EOF
    <div class="section">
        <h2>RDS Databases</h2>
        <div class="metric">
            <div class="number">$rds_count</div>
            <div>Total Databases</div>
        </div>
    </div>
    </div>
</body>
</html>
EOF

echo "✓ Dashboard generated: $REPORT_FILE"
```

**Explanation**: Generates HTML dashboard aggregating EC2, S3, RDS metrics with counts and status indicators. Saves as timestamped HTML file.

---

**Pro Tip**: Always use `--no-cli-pager` flag to disable pager in scripts: `aws ec2 describe-instances --no-cli-pager`
