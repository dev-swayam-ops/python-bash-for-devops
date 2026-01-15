# Cloud Automation Exercises

## Easy Exercises (1-4)

### Exercise 1: AWS CLI Configuration and Verification
Write a script that:
- Checks if AWS CLI is installed
- Runs `aws sts get-caller-identity` to verify credentials
- Displays the account ID and user ARN
- Returns exit code based on success

**Hint**: Use `command -v aws`, `aws sts get-caller-identity`

**Requirements**:
- Exit code 0 if credentials valid
- Exit code 1 if credentials invalid
- Show clear error messages

### Exercise 2: S3 Bucket List and Info
Create a script that:
- Lists all S3 buckets in the account
- Shows creation date for each bucket
- Counts total number of buckets
- Shows total storage size (if possible)

**Hint**: Use `aws s3 ls`, `aws s3api list-buckets`

**Requirements**:
- Show bucket count
- Format output nicely
- Handle no buckets gracefully

### Exercise 3: EC2 Instance Lister
Write a script that:
- Lists all EC2 instances in current region
- Shows Instance ID, State, Instance Type, Launch Time
- Counts running, stopped, and terminated instances
- Shows public IP if available

**Hint**: Use `aws ec2 describe-instances`, JMESPath queries

**Requirements**:
- Use table or formatted output
- Summary of instance states
- Handle no instances gracefully

### Exercise 4: Create and Delete S3 Bucket
Create a script that:
- Takes a bucket name as argument
- Creates S3 bucket with specified name
- Adds versioning to bucket
- Lists bucket configuration
- Deletes bucket (with confirmation)

**Hint**: Use `aws s3 mb`, `aws s3api put-bucket-versioning`

**Requirements**:
- Validate bucket name format
- Confirm before deletion
- Show bucket details

## Medium Exercises (5-10)

### Exercise 5: Tag EC2 Instances by Name
Build a script that:
- Lists all EC2 instances without a "Name" tag
- Prompts user to add Name tag
- Applies tag to instance
- Verifies tag was applied
- Lists updated instances with tags

**Hint**: Use `aws ec2 create-tags`, `aws ec2 describe-tags`

**Requirements**:
- Identify untagged instances
- Add custom naming
- Verify application

### Exercise 6: S3 File Upload and Download Manager
Create a script that:
- Accepts source file and S3 bucket as arguments
- Uploads file with metadata (Content-Type, tags)
- Lists uploaded objects
- Downloads file from bucket to local
- Validates checksum matches

**Hint**: Use `aws s3 cp`, `aws s3api head-object`

**Requirements**:
- Handle large files
- Show progress
- Verify integrity

### Exercise 7: RDS Database Management
Write a script that:
- Lists all RDS instances and their status
- Shows database size and storage
- Gets RDS cluster information
- Creates automated backup
- Shows backup retention policy

**Hint**: Use `aws rds describe-db-instances`, `aws rds create-db-snapshot`

**Requirements**:
- Display engine type
- Show status clearly
- List backups

### Exercise 8: CloudWatch Alarm Manager
Build a script that:
- Lists all CloudWatch alarms
- Shows alarm status (OK, ALARM, INSUFFICIENT_DATA)
- Creates custom alarm for CPU/Memory/Disk
- Configures SNS notification
- Tests alarm trigger

**Hint**: Use `aws cloudwatch describe-alarms`, `aws cloudwatch put-metric-alarm`

**Requirements**:
- Show alarm summary
- Support multiple metrics
- Configure notifications

### Exercise 9: Security Group and VPC Inspector
Create a script that:
- Lists all VPCs and security groups
- Shows inbound/outbound rules
- Identifies overly permissive rules (0.0.0.0/0)
- Generates security report
- Suggests remediation

**Hint**: Use `aws ec2 describe-security-groups`, `aws ec2 describe-vpcs`

**Requirements**:
- Identify risky rules
- Show rule details
- Generate report file

### Exercise 10: Complete Cloud Infrastructure Dashboard
Build comprehensive script that:
- Aggregates data from EC2, S3, RDS, CloudWatch
- Shows current resource count and state
- Calculates estimated costs
- Lists recent API activity (CloudTrail)
- Generates HTML/markdown dashboard
- Sends report via email
- Compares current vs previous month

**Hint**: Combine all previous scripts

**Requirements**:
- Multiple service integration
- Cost estimation
- Report generation
- Automated email delivery
- Historical comparison
