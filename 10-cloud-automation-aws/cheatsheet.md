# AWS Cloud Automation Cheatsheet

## AWS CLI Configuration

| Command | Purpose | Example |
|---------|---------|---------|
| `aws configure` | Interactive credential setup | `aws configure` |
| `aws configure --profile <name>` | Create named profile | `aws configure --profile prod` |
| `aws sts get-caller-identity` | Verify credentials | `aws sts get-caller-identity` |
| `aws configure list` | Show current config | `aws configure list` |
| `export AWS_PROFILE=<name>` | Switch profile | `export AWS_PROFILE=prod` |

## EC2 Instance Management

| Command | Purpose | Example |
|---------|---------|---------|
| `aws ec2 describe-instances` | List instances | `aws ec2 describe-instances --output table` |
| `aws ec2 run-instances` | Launch instance | `aws ec2 run-instances --image-id ami-xxx --instance-type t2.micro` |
| `aws ec2 start-instances` | Start instance | `aws ec2 start-instances --instance-ids i-xxx` |
| `aws ec2 stop-instances` | Stop instance | `aws ec2 stop-instances --instance-ids i-xxx` |
| `aws ec2 terminate-instances` | Delete instance | `aws ec2 terminate-instances --instance-ids i-xxx` |
| `aws ec2 create-tags` | Add tags | `aws ec2 create-tags --resources i-xxx --tags Key=Name,Value=MyInstance` |
| `aws ec2 describe-security-groups` | List security groups | `aws ec2 describe-security-groups --output table` |

## S3 Bucket Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `aws s3 ls` | List buckets | `aws s3 ls` |
| `aws s3 mb` | Create bucket | `aws s3 mb s3://my-bucket` |
| `aws s3 rb` | Delete bucket | `aws s3 rb s3://my-bucket --force` |
| `aws s3 cp` | Copy file | `aws s3 cp file.txt s3://bucket/` |
| `aws s3 sync` | Sync directory | `aws s3 sync ./local s3://bucket/` |
| `aws s3 rm` | Delete object | `aws s3 rm s3://bucket/file.txt` |
| `aws s3 ls s3://bucket/` | List bucket contents | `aws s3 ls s3://bucket/ --recursive` |

## S3 Advanced Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `aws s3api list-buckets` | Detailed bucket list | `aws s3api list-buckets --query 'Buckets[].Name'` |
| `aws s3api head-object` | Object metadata | `aws s3api head-object --bucket bucket --key file.txt` |
| `aws s3api put-bucket-versioning` | Enable versioning | `aws s3api put-bucket-versioning --bucket bucket --versioning-configuration Status=Enabled` |
| `aws s3api put-bucket-lifecycle` | Set lifecycle policy | Manage object expiration |
| `aws s3api get-bucket-acl` | Get bucket permissions | `aws s3api get-bucket-acl --bucket bucket` |

## RDS Database Management

| Command | Purpose | Example |
|---------|---------|---------|
| `aws rds describe-db-instances` | List databases | `aws rds describe-db-instances --output table` |
| `aws rds create-db-instance` | Create database | Complex command with many options |
| `aws rds delete-db-instance` | Delete database | `aws rds delete-db-instance --db-instance-identifier mydb --skip-final-snapshot` |
| `aws rds create-db-snapshot` | Create backup | `aws rds create-db-snapshot --db-instance-identifier mydb --db-snapshot-identifier backup-01` |
| `aws rds describe-db-snapshots` | List backups | `aws rds describe-db-snapshots --output table` |
| `aws rds restore-db-instance-from-db-snapshot` | Restore from backup | Restore from snapshot ID |

## CloudWatch Monitoring

| Command | Purpose | Example |
|---------|---------|---------|
| `aws cloudwatch describe-alarms` | List alarms | `aws cloudwatch describe-alarms --output table` |
| `aws cloudwatch put-metric-alarm` | Create alarm | `aws cloudwatch put-metric-alarm --alarm-name cpu-high ...` |
| `aws cloudwatch get-metric-statistics` | Get metrics | Get historical metric data |
| `aws cloudwatch list-metrics` | List available metrics | `aws cloudwatch list-metrics --namespace AWS/EC2` |
| `aws cloudwatch delete-alarms` | Delete alarm | `aws cloudwatch delete-alarms --alarm-names MyAlarm` |

## VPC and Security

| Command | Purpose | Example |
|---------|---------|---------|
| `aws ec2 describe-vpcs` | List VPCs | `aws ec2 describe-vpcs --output table` |
| `aws ec2 describe-subnets` | List subnets | `aws ec2 describe-subnets --output table` |
| `aws ec2 authorize-security-group-ingress` | Allow inbound | Add firewall rule |
| `aws ec2 revoke-security-group-ingress` | Deny inbound | Remove firewall rule |
| `aws ec2 describe-network-interfaces` | List ENIs | List network interfaces |

## IAM User and Access Management

| Command | Purpose | Example |
|---------|---------|---------|
| `aws iam list-users` | List IAM users | `aws iam list-users --output table` |
| `aws iam list-access-keys` | List API keys | `aws iam list-access-keys --user-name username` |
| `aws iam attach-user-policy` | Add policy | Grant permissions to user |
| `aws iam create-user` | Create user | `aws iam create-user --user-name newuser` |
| `aws iam delete-user` | Delete user | `aws iam delete-user --user-name username` |

## JMESPath Query Examples

| Query | Purpose | Example |
|-------|---------|---------|
| `--query 'Instances[].InstanceId'` | Extract instance IDs | Get list of IDs only |
| `--query 'Instances[*].[InstanceId,State.Name]'` | Multi-column | Show ID and state |
| `--query "Instances[?State.Name=='running']"` | Filter running | Running instances only |
| `--query 'length(Instances[])'` | Count items | Count total instances |
| `--query 'max_by(Instances[], &LaunchTime)'` | Find max | Most recently launched |

## Output Formats

| Format | Command | Use Case |
|--------|---------|----------|
| table | `--output table` | Human-readable |
| json | `--output json` | Parsing with jq |
| text | `--output text` | Scripts, grep |
| yaml | `--output yaml` | Configuration files |

## Common Query Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Filter by tag | Find resources by tag | `--filters "Name=tag:Environment,Values=prod"` |
| Filter by state | Find by status | `"Name=instance-state-name,Values=running"` |
| Filter by type | Find by resource type | `"Name=instance-type,Values=t2.micro"` |
| Multiple conditions | AND filter | Multiple --filters flags |

## Region and Zone Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `aws configure get region` | Current region | Show active region |
| `aws ec2 describe-regions` | List regions | All AWS regions |
| `aws ec2 describe-availability-zones` | List AZs | For current region |
| `--region <region>` | Override region | Use different region |

## Cost Management Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `aws ce get-cost-and-usage` | Cost analysis | Get spending data |
| `aws pricing get-products` | List prices | Pricing information |
| `aws budgets describe-budgets` | List budgets | Cost alerts |
| `aws ec2 describe-instances --query 'Reservations[].Instances[].[InstanceId,InstanceType]'` | Instance inventory | For cost calculation |

## Tagging Best Practices

| Tag | Purpose | Example Value |
|-----|---------|---|
| Name | Resource name | Production-WebServer |
| Environment | Dev/Test/Prod | production |
| Owner | Responsible team | devops-team |
| CostCenter | Billing | engineering |
| Application | App name | payment-processor |
| ManagedBy | IaC or manual | terraform |
| BackupPolicy | Retention | daily-7days |

## Useful Aliases

```bash
# Add to ~/.bashrc or ~/.bash_profile
alias aws-profile='export AWS_PROFILE'
alias ec2-list='aws ec2 describe-instances --output table'
alias s3-list='aws s3 ls'
alias rds-list='aws rds describe-db-instances --output table'
alias iam-list='aws iam list-users --output table'
alias whoami='aws sts get-caller-identity'
```

## Common Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| UnauthorizedOperation | Invalid credentials | Run `aws configure` |
| AccessDenied | Missing IAM permissions | Check IAM policy |
| InvalidParameterValue | Wrong parameter | Check command syntax |
| ThrottlingException | API rate limit | Retry with exponential backoff |
| ResourceNotFound | Resource doesn't exist | Verify ID/name |

## Performance Tips

1. Use `--no-cli-pager` to disable pager in scripts
2. Use `--query` to filter results early (server-side)
3. Batch operations when possible
4. Use `--output json` for parsing with jq
5. Add `--region` to avoid region mismatches
6. Use profiles for multi-account setup
7. Cache credentials with `aws sts get-caller-identity`
8. Use `aws ec2 wait` for instance state changes

## Debugging

```bash
# Enable debug output
aws ec2 describe-instances --debug

# Validate credentials
aws sts get-caller-identity

# Check current config
aws configure list

# View all profiles
cat ~/.aws/config

# Test IAM permissions
aws iam simulate-principal-policy ...
```

---

**Pro Tip**: Combine `--query` with jq for powerful filtering: `aws ec2 describe-instances --output json | jq '.Reservations[].Instances[] | select(.State.Name=="running")'`
