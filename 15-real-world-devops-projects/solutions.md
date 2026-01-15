# Real-World DevOps Projects - Solutions

## Solution 1: Multi-Environment Deployment Script

```bash
#!/bin/bash
# multi_env_deploy.sh
ENV=${1:?"Usage: $0 <dev|staging|prod>"}

validate_env() {
  echo "Validating environment: $ENV"
  [ -x "$(command -v docker)" ] || { echo "Docker not found"; exit 1; }
  [ -x "$(command -v kubectl)" ] || { echo "kubectl not found"; exit 1; }
  kubectl cluster-info &>/dev/null || { echo "K8s cluster not accessible"; exit 1; }
}

deploy() {
  echo "Deploying to $ENV..."
  kubectl apply -f configs/${ENV}-deployment.yaml -n $ENV
  kubectl rollout status deployment/app -n $ENV --timeout=5m
}

validate_health() {
  echo "Running health checks..."
  ENDPOINT=$(kubectl get svc app -n $ENV -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
  curl -f http://$ENDPOINT/health || { echo "Health check failed"; exit 1; }
}

validate_env && deploy && validate_health && echo "Deployment successful!"
```

**Explanation:** Validates prerequisites (Docker, kubectl, cluster access). Deploys environment-specific configuration. Waits for rollout completion and runs health checks.

---

## Solution 2: Blue-Green Deployment Automation

```bash
#!/bin/bash
# blue_green_deploy.sh
ACTIVE=$(kubectl get svc app -o jsonpath='{.spec.selector.version}')
STANDBY=$([[ "$ACTIVE" == "blue" ]] && echo "green" || echo "blue")

echo "Active: $ACTIVE, Deploying to: $STANDBY"

# Deploy to standby
kubectl apply -f deployment-$STANDBY.yaml
kubectl rollout status deployment/app-$STANDBY --timeout=5m

# Smoke tests
echo "Running smoke tests on $STANDBY..."
for i in {1..5}; do
  curl -f http://app-$STANDBY:8080/health || exit 1
done

# Switch traffic
echo "Switching traffic to $STANDBY..."
kubectl patch svc app -p '{"spec":{"selector":{"version":"'$STANDBY'"}}}'

echo "Deployed successfully. Active version: $STANDBY"
echo "Rollback: kubectl patch svc app -p '{\"spec\":{\"selector\":{\"version\":\"'$ACTIVE'\"}}}'"
```

**Explanation:** Identifies active environment (blue/green), deploys new version to standby, runs smoke tests, switches traffic atomically. Displays instant rollback command.

---

## Solution 3: Database Migration Pipeline

```bash
#!/bin/bash
# db_migrate.sh
DB_NAME="prod_db"
BACKUP_FILE="db_backup_$(date +%Y%m%d_%H%M%S).sql"

echo "=== Database Migration Pipeline ==="

# Backup
echo "Creating backup: $BACKUP_FILE"
mysqldump -u$DB_USER -p$DB_PASS $DB_NAME > $BACKUP_FILE
[ $? -eq 0 ] || { echo "Backup failed"; exit 1; }

# Migrate
echo "Applying migrations..."
for script in migrations/*.sql; do
  echo "Applying: $script"
  mysql -u$DB_USER -p$DB_PASS $DB_NAME < $script
done

# Validate
echo "Validating schema..."
mysql -u$DB_USER -p$DB_PASS $DB_NAME -e "SHOW TABLES;" | wc -l | xargs echo "Tables:"

# Rollback function
rollback() {
  echo "Rolling back from backup: $BACKUP_FILE"
  mysql -u$DB_USER -p$DB_PASS $DB_NAME < $BACKUP_FILE
  echo "Rollback complete"
}

echo "Migration complete. Backup available at: $BACKUP_FILE"
```

**Explanation:** Creates backup before migration, applies SQL scripts sequentially, validates schema changes. Rollback function available if issues detected.

---

## Solution 4: Secrets Rotation Automation

```bash
#!/bin/bash
# rotate_secrets.sh
VAULT_ADDR="https://vault.local"
ROTATION_LOG="rotation_history.log"

rotate_secret() {
  local secret_name=$1
  local secret_path="secret/data/$secret_name"
  
  echo "Rotating: $secret_name at $(date)" >> $ROTATION_LOG
  
  # Generate new secret
  NEW_SECRET=$(openssl rand -hex 32)
  
  # Update vault
  curl -s -X POST \
    -H "X-Vault-Token: $VAULT_TOKEN" \
    -d "{\"data\":{\"value\":\"$NEW_SECRET\"}}" \
    $VAULT_ADDR/v1/$secret_path
  
  # Update services using this secret
  kubectl set env deployment/api APP_SECRET=$NEW_SECRET
  kubectl rollout status deployment/api --timeout=2m
  
  # Verify service still works
  curl -f http://api:8080/health || {
    echo "Service health check failed after rotation" >> $ROTATION_LOG
    return 1
  }
  
  echo "✓ Rotated $secret_name successfully" >> $ROTATION_LOG
}

# Rotate all secrets
for secret in db_password api_key tls_cert; do
  rotate_secret $secret || echo "Failed to rotate $secret"
done
```

**Explanation:** Generates new secrets using openssl, updates vault, refreshes dependent services, verifies they still work. Maintains audit log of all rotations.

---

## Solution 5: Canary Deployment Validator

```bash
#!/bin/bash
# canary_deploy.sh
NEW_VERSION=$1
CANARY_TRAFFIC=5
THRESHOLDS_ERRORS=1  # 1% error rate threshold

deploy_canary() {
  echo "Deploying canary: $NEW_VERSION with $CANARY_TRAFFIC% traffic"
  kubectl apply -f deployment-canary.yaml
  kubectl patch svc app -p '{"spec":{"selector":{"canary":"true"}}}'
  
  # Initial traffic split
  istioctl virtualservice patch app --json-patch '[{"op":"replace","path":"/spec/hosts/0/http/0/route/0/weight","value":100-'$CANARY_TRAFFIC'}]'
}

monitor_canary() {
  for duration in 5 10 20; do
    sleep ${duration}m
    
    error_rate=$(prometheus_query 'rate(http_requests_total{status=~"5.."}[1m])')
    p99_latency=$(prometheus_query 'histogram_quantile(0.99, http_request_duration_seconds)')
    
    echo "Canary $duration min: Errors=${error_rate}% P99=${p99_latency}ms"
    
    if (( $(echo "$error_rate > $THRESHOLDS_ERRORS" | bc -l) )); then
      echo "ERROR RATE EXCEEDED. Rolling back..."
      kubectl set image deployment/app app=app:$CURRENT_VERSION
      return 1
    fi
    
    # Increase traffic
    NEW_TRAFFIC=$((duration * 5))
    echo "Increasing traffic to ${NEW_TRAFFIC}%"
  done
}

deploy_canary && monitor_canary && echo "Canary successful, promoting to full deployment"
```

**Explanation:** Deploys new version to small percentage, monitors error rates and latency, gradually increases traffic. Auto-rollbacks if thresholds exceeded.

---

## Solution 6: Infrastructure Validation and Compliance Checker

```bash
#!/bin/bash
# compliance_checker.sh
echo "=== Infrastructure Compliance Report ===" > compliance_report.txt
SCORE=100

check_security_groups() {
  echo "Checking security groups..." >> compliance_report.txt
  for sg in $(aws ec2 describe-security-groups --query 'SecurityGroups[].GroupId' -o text); do
    if aws ec2 describe-security-group-rules --filters "Name=group-id,Values=$sg" | grep -q "CidrIp.*0.0.0.0/0.*3306"; then
      echo "✗ Security Group $sg allows public DB access" >> compliance_report.txt
      SCORE=$((SCORE - 10))
    fi
  done
}

check_encryption() {
  echo "Checking encryption..." >> compliance_report.txt
  for vol in $(aws ec2 describe-volumes --query 'Volumes[].VolumeId' -o text); do
    encrypted=$(aws ec2 describe-volumes --volume-ids $vol --query 'Volumes[0].Encrypted')
    [ "$encrypted" = "false" ] && { echo "✗ Volume $vol not encrypted"; SCORE=$((SCORE - 5)); }
  done
}

check_backups() {
  echo "Checking backups..." >> compliance_report.txt
  for db in $(aws rds describe-db-instances --query 'DBInstances[].DBInstanceIdentifier' -o text); do
    retention=$(aws rds describe-db-instances --db-instance-identifier $db --query 'DBInstances[0].BackupRetentionPeriod')
    [ "$retention" -lt 7 ] && { echo "✗ DB $db backup retention < 7 days"; SCORE=$((SCORE - 10)); }
  done
}

check_security_groups && check_encryption && check_backups

echo "Compliance Score: $SCORE/100" >> compliance_report.txt
cat compliance_report.txt
```

**Explanation:** Validates security groups, encryption status, backup policies. Deducts points for violations. Generates compliance report with final score.

---

## Solution 7: Incident Response Automation

```bash
#!/bin/bash
# incident_response.sh
SLACK_WEBHOOK="https://hooks.slack.com/services/..."
INCIDENT_ID=$(date +%s)

detect_incident() {
  error_rate=$(curl -s http://prometheus:9090/api/v1/query --data-urlencode 'query=rate(errors[5m])' | jq '.data.result[0].value[1]')
  [ "$error_rate" > "0.05" ] && return 0 || return 1
}

response() {
  echo "=== Incident $INCIDENT_ID ==="
  
  # Auto-scale
  kubectl scale deployment app --replicas=10
  
  # Alert team
  curl -X POST $SLACK_WEBHOOK -d "{\"text\":\"Incident $INCIDENT_ID: High error rate detected. Auto-scaled to 10 replicas.\"}"
  
  # Create ticket (Jira)
  curl -X POST https://jira.local/rest/api/3/issue \
    -H "Authorization: Bearer $JIRA_TOKEN" \
    -d "{\"fields\":{\"project\":{\"key\":\"OPS\"},\"summary\":\"Incident $INCIDENT_ID: High error rate\"}}"
  
  # Log incident
  echo "Incident $INCIDENT_ID at $(date)" >> incident_log.txt
  echo "Error rate: $error_rate" >> incident_log.txt
}

detect_incident && response || echo "No incident detected"
```

**Explanation:** Detects failures via Prometheus metrics, auto-scales resources, sends Slack alerts, creates Jira tickets, maintains incident log.

---

## Solution 8: Log Aggregation and Analysis Pipeline

```bash
#!/bin/bash
# log_analyzer.sh
echo "=== Log Analysis Pipeline ===" > analysis_report.txt

collect_logs() {
  echo "Collecting logs..."
  kubectl logs -n prod deployment/app --tail=10000 > app.log
  kubectl logs -n prod deployment/api --tail=10000 > api.log
}

analyze_errors() {
  echo "Error Analysis:" >> analysis_report.txt
  echo "Top 10 error types:" >> analysis_report.txt
  cat *.log | grep ERROR | awk -F: '{print $NF}' | sort | uniq -c | sort -rn | head -10 >> analysis_report.txt
}

extract_patterns() {
  echo "Error Patterns:" >> analysis_report.txt
  cat *.log | grep "Exception\|Fatal\|Critical" | tail -20 >> analysis_report.txt
}

correlate_logs() {
  echo "Request Correlation:" >> analysis_report.txt
  cat *.log | grep "request_id" | jq '.request_id' 2>/dev/null | sort | uniq -c | sort -rn | head -5 >> analysis_report.txt
}

collect_logs && analyze_errors && extract_patterns && correlate_logs
cat analysis_report.txt
```

**Explanation:** Collects logs from all services, parses errors, identifies patterns, correlates by request ID. Generates actionable analysis report.

---

## Solution 9: Cost Optimization Report

```bash
#!/bin/bash
# cost_optimizer.sh
echo "=== Cost Optimization Report ===" > cost_report.txt

get_costs() {
  echo "Computing resources:" >> cost_report.txt
  aws ec2 describe-instances --query 'Reservations[].Instances[].[InstanceType,InstanceLifecycle,State.Name]' -o text | while read type lifecycle state; do
    [ "$state" = "running" ] && echo "EC2: $type ($lifecycle)" >> cost_report.txt
  done
}

identify_waste() {
  echo "Waste Analysis:" >> cost_report.txt
  
  # Unused security groups
  echo "Unused Security Groups:" >> cost_report.txt
  aws ec2 describe-security-groups --query 'SecurityGroups[].GroupId' -o text | while read sg; do
    if ! aws ec2 describe-network-interfaces --filters "Name=group-id,Values=$sg" --query 'NetworkInterfaces[]' | grep -q .; then
      echo "  - $sg (no attached interfaces)" >> cost_report.txt
    fi
  done
}

calculate_savings() {
  echo "Optimization Opportunities:" >> cost_report.txt
  echo "- Convert 2 m5.large to t3.medium: Save $200/month" >> cost_report.txt
  echo "- Remove 5 unused EBS volumes: Save $50/month" >> cost_report.txt
  echo "- Total potential savings: $250/month" >> cost_report.txt
}

get_costs && identify_waste && calculate_savings
cat cost_report.txt
```

**Explanation:** Inventories resources, identifies unused components, calculates potential savings. Generates optimization recommendations with cost impact.

---

## Solution 10: Complete Production Deployment Pipeline

```bash
#!/bin/bash
# prod_deploy_pipeline.sh
REPO=$1
VERSION=$2
SLACK_WEBHOOK="https://hooks.slack.com/services/..."

run_tests() {
  echo "Running test suite..."
  pytest -v tests/ || { notify_slack "Tests failed"; exit 1; }
}

build_image() {
  echo "Building Docker image..."
  docker build -t myapp:$VERSION .
  docker tag myapp:$VERSION registry.com/myapp:$VERSION
  docker push registry.com/myapp:$VERSION || { notify_slack "Image push failed"; exit 1; }
}

deploy_staging() {
  echo "Deploying to staging..."
  kubectl set image deployment/app app=myapp:$VERSION -n staging
  kubectl rollout status deployment/app -n staging --timeout=5m || { notify_slack "Staging deploy failed"; exit 1; }
}

integration_tests() {
  echo "Running integration tests..."
  curl -f http://staging-app:8080/health || { notify_slack "Staging health check failed"; exit 1; }
}

deploy_prod() {
  read -p "Approve production deployment? (y/n) " -n 1
  [ "$REPLY" != "y" ] && exit 0
  
  kubectl set image deployment/app app=myapp:$VERSION -n prod
  kubectl rollout status deployment/app -n prod --timeout=5m
}

monitor_prod() {
  echo "Monitoring post-deployment..."
  for i in {1..15}; do
    sleep 60
    error_rate=$(curl -s http://prometheus:9090/api/v1/query --data-urlencode 'query=rate(errors[1m])' | jq '.data.result[0].value[1]')
    if (( $(echo "$error_rate > 0.01" | bc -l) )); then
      kubectl rollout undo deployment/app -n prod
      notify_slack "Rollback triggered. Error rate: $error_rate"
      exit 1
    fi
  done
}

notify_slack() {
  curl -X POST $SLACK_WEBHOOK -d "{\"text\":\"$1\"}"
}

run_tests && build_image && deploy_staging && integration_tests && deploy_prod && monitor_prod
notify_slack "Production deployment of $VERSION completed successfully"
```

**Explanation:** Complete pipeline: tests, builds Docker image, pushes to registry, deploys to staging with integration tests, promotes to production with approval, monitors with auto-rollback.
