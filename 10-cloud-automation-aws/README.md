# Cloud Automation with AWS

## What You'll Learn
- AWS CLI basics and configuration
- EC2 instance management (create, start, stop, terminate)
- S3 bucket operations and file management
- RDS database automation
- CloudWatch monitoring and alarms
- VPC and security group management
- Infrastructure as Code (IaC) concepts
- Cost optimization and resource tagging

## Prerequisites
- Completed: [09-backup-and-restore-automation](../09-backup-and-restore-automation)
- AWS account with proper credentials
- AWS CLI installed and configured
- Basic understanding of cloud concepts

## Key Concepts
- **IAM**: Identity and Access Management
- **EC2**: Elastic Compute Cloud (virtual servers)
- **S3**: Simple Storage Service (object storage)
- **RDS**: Relational Database Service
- **VPC**: Virtual Private Cloud (network isolation)
- **Tags**: Key-value metadata for resources

## Hands-on Lab: AWS Instance and Storage Automation

### Step 1: Configure AWS CLI
```bash
aws sts get-caller-identity

# Expected output:
# {
#     "UserId": "AIDAI...",
#     "Account": "123456789012",
#     "Arn": "arn:aws:iam::123456789012:user/username"
# }
```

### Step 2: Create S3 Bucket and Upload Files
```bash
BUCKET_NAME="my-app-bucket-$(date +%s)"

# Create bucket
aws s3 mb "s3://$BUCKET_NAME" --region us-east-1

# Upload file
echo "Important data" > document.txt
aws s3 cp document.txt "s3://$BUCKET_NAME/"

# List contents
aws s3 ls "s3://$BUCKET_NAME/"

# Expected: 2024-01-15 10:30:45        14 document.txt
```

### Step 3: Manage EC2 Instances
```bash
INSTANCE_ID=$(aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t2.micro \
    --query 'Instances[0].InstanceId' \
    --output text)

# Tag instance
aws ec2 create-tags \
    --resources "$INSTANCE_ID" \
    --tags Key=Name,Value="MyInstance"

# Stop instance
aws ec2 stop-instances --instance-ids "$INSTANCE_ID"
```

### Step 4: Create CloudWatch Alarm
```bash
aws cloudwatch put-metric-alarm \
    --alarm-name "high-cpu-alert" \
    --alarm-description "Alert when CPU > 80%" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 80 \
    --comparison-operator GreaterThanThreshold
```

### Step 5: List All Resources
```bash
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name]' --output table
aws s3 ls
aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,Engine]' --output table
```

## Validation Checklist
- [ ] AWS CLI configured and accessible
- [ ] S3 bucket created and accessible
- [ ] Files uploaded and downloaded successfully
- [ ] EC2 instance launched and running
- [ ] CloudWatch alarm created
- [ ] Resources tagged properly

## Cleanup
```bash
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
aws s3 rm s3://my-app-bucket --recursive
aws s3 rb s3://my-app-bucket
aws cloudwatch delete-alarms --alarm-names "high-cpu-alert"
```

## Common Mistakes
1. **Wrong region**: Specify region consistently
2. **Missing IAM permissions**: Check IAM policy
3. **Not cleaning up resources**: Leads to unexpected costs
4. **Hardcoding credentials**: Use IAM roles instead
5. **No resource tags**: Can't track ownership/cost

## Troubleshooting

### An error occurred (UnauthorizedOperation)
- **Cause**: AWS credentials invalid or IAM permissions missing
- **Solution**: Run `aws configure` again, check IAM policy

### Unable to locate credentials
- **Cause**: AWS credentials not configured
- **Solution**: `aws configure` or set AWS_ACCESS_KEY_ID env vars

### InvalidKeyPair.NotFound
- **Cause**: Key pair doesn't exist in region
- **Solution**: Create key pair or use existing one

### S3 bucket already exists
- **Cause**: Bucket name globally unique, already taken
- **Solution**: Use different bucket name with timestamp

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [AWS cheatsheet](cheatsheet.md)
3. Explore Terraform for IaC
4. Learn CloudFormation templates
5. Set up billing alerts for cost control
