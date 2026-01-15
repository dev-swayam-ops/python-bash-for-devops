# Real-World DevOps Projects - Exercises

## Exercise 1: Multi-Environment Deployment Script
Create a deployment script that handles multiple environments (dev, staging, prod). It should validate pre-deployment conditions, deploy the application, and verify health.

**Requirements:**
- Accept environment as parameter
- Validate prerequisites (Docker, K8s access, image availability)
- Deploy with environment-specific configs
- Run health checks post-deployment
- Generate deployment report

## Exercise 2: Blue-Green Deployment Automation
Write a script that implements blue-green deployment: maintain two identical production environments, switch traffic between them, and enable instant rollback.

**Requirements:**
- Create/maintain two environments (blue and green)
- Deploy new version to inactive environment
- Run smoke tests before switch
- Switch traffic atomically
- Quick rollback to previous version
- Monitor both environments

## Exercise 3: Database Migration Pipeline
Create a script that automates database migrations: backup current state, apply schema changes, validate integrity, and rollback if needed.

**Requirements:**
- Backup database before migration
- Execute SQL migration scripts
- Validate schema changes
- Verify data integrity
- Enable rollback to backup
- Log all migration activities
- Send notifications on completion

## Exercise 4: Secrets Rotation Automation
Build a script that rotates secrets (API keys, database passwords, certificates) automatically, updates all services using them, and maintains audit logs.

**Requirements:**
- Generate new secrets
- Update secrets in vault/manager
- Refresh all dependent services
- Validate services still work
- Maintain rotation history
- Alert on failed rotations
- Prevent simultaneous rotations

## Exercise 5: Canary Deployment Validator
Write a script that gradually rolls out new version to percentage of traffic, monitors metrics, and either completes rollout or rolls back based on error rates.

**Requirements:**
- Start with 5% traffic
- Monitor error rates, latency, CPU
- Gradually increase traffic (5% → 25% → 50% → 100%)
- Set threshold for auto-rollback (e.g., > 1% errors)
- Compare canary vs stable version metrics
- Rollback if thresholds exceeded
- Generate analysis report

## Exercise 6: Infrastructure Validation and Compliance Checker
Create a script that validates production infrastructure against compliance requirements: security groups, backup policies, monitoring, encryption, compliance tagging.

**Requirements:**
- Check security group rules
- Verify encryption at rest/transit
- Validate backup configurations
- Ensure monitoring is active
- Check compliance tags present
- Identify unpatched systems
- Generate compliance report with score

## Exercise 7: Incident Response Automation
Build a script that detects failure conditions (high error rate, pod crashes) and automatically initiates response: scale resources, trigger alerts, create incident tickets, initiate communication.

**Requirements:**
- Monitor key metrics in real-time
- Detect failure conditions
- Auto-scale resources on high load
- Send alerts to teams
- Create incident tickets (Jira)
- Trigger communication (Slack)
- Log incident details
- Auto-remediate common issues

## Exercise 8: Log Aggregation and Analysis Pipeline
Create a script that collects logs from all services, parses them, identifies errors/warnings, and generates actionable reports.

**Requirements:**
- Collect logs from multiple sources
- Parse and normalize formats
- Identify error patterns
- Extract stack traces
- Correlate logs by request ID
- Generate error frequency report
- Alert on critical patterns
- Create searchable log database

## Exercise 9: Cost Optimization Report
Write a script that analyzes resource usage and spending, identifies waste (unused resources, oversizing, unnecessary services), and suggests optimizations.

**Requirements:**
- Collect resource inventory
- Get pricing information
- Calculate current costs
- Identify unused resources
- Find oversized instances
- Calculate potential savings
- Generate optimization recommendations
- Track savings over time

## Exercise 10: Complete Production Deployment Pipeline
Create comprehensive end-to-end script that: runs tests, builds image, pushes to registry, manages multiple environments, runs health checks, handles rollback, integrates monitoring, and notifies team.

**Requirements:**
- Run full test suite
- Build and tag Docker image
- Push to registry with verification
- Deploy to staging with validation
- Run integration tests
- Promote to production (with approval)
- Monitor for 15 minutes post-deploy
- Automatic rollback on alert
- Notify Slack with status
- Generate deployment report
- Create audit log
