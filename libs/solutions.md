# Libs - Solutions

## Solution 1: AWS Operations Library

```bash
#!/bin/bash
# libs/aws.sh - AWS Operations Library

AWS_PROFILE=${AWS_PROFILE:-default}
AWS_REGION=${AWS_REGION:-us-east-1}

aws_init() {
  command -v aws >/dev/null || { echo "ERROR: AWS CLI not found"; return 1; }
  echo "AWS library initialized with profile: $AWS_PROFILE, region: $AWS_REGION"
}

aws_list_instances() {
  local region=${1:-$AWS_REGION}
  aws ec2 describe-instances --region $region --profile $AWS_PROFILE \
    --query 'Reservations[].Instances[].[InstanceId,State.Name,InstanceType,PublicIpAddress]' \
    --output table
}

aws_list_s3_buckets() {
  aws s3 ls --profile $AWS_PROFILE | while read -r date time name; do
    size=$(aws s3 ls s3://$name --recursive --human-readable --summarize --profile $AWS_PROFILE | grep "Total Size:" | awk '{print $3}')
    printf "%-30s %10s\n" "$name" "$size"
  done
}

aws_get_sg_rules() {
  local sg_id=$1
  [ -z "$sg_id" ] && { echo "Usage: aws_get_sg_rules <sg-id>"; return 1; }
  
  aws ec2 describe-security-group-rules --filters "Name=group-id,Values=$sg_id" \
    --profile $AWS_PROFILE --query 'SecurityGroupRules[].{Port:FromPort,Protocol:IpProtocol,CIDR:CidrIpv4}' \
    --output table
}

aws_create_snapshot() {
  local volume_id=$1
  [ -z "$volume_id" ] && { echo "Usage: aws_create_snapshot <volume-id>"; return 1; }
  
  local snap=$(aws ec2 create-snapshot --volume-id $volume_id --profile $AWS_PROFILE --query 'SnapshotId' --output text)
  echo "Snapshot created: $snap"
}
```

**Explanation:** Library with functions to initialize AWS context, list EC2 instances, S3 buckets, manage security groups, and create volume snapshots with profile/region support.

---

## Solution 2: Kubernetes Utilities Library

```bash
#!/bin/bash
# libs/kubernetes.sh - Kubernetes Utilities Library

K8S_NAMESPACE=${K8S_NAMESPACE:-default}
K8S_CONTEXT=${K8S_CONTEXT:-$(kubectl config current-context)}

k8s_init() {
  command -v kubectl >/dev/null || { echo "ERROR: kubectl not found"; return 1; }
  kubectl cluster-info &>/dev/null || { echo "ERROR: Not connected to cluster"; return 1; }
  echo "Kubernetes library initialized. Context: $K8S_CONTEXT"
}

k8s_get_deployments() {
  local ns=${1:-$K8S_NAMESPACE}
  kubectl get deployments -n $ns -o wide
}

k8s_scale_deployment() {
  local deployment=$1
  local replicas=$2
  local ns=${3:-$K8S_NAMESPACE}
  
  [ -z "$deployment" ] || [ -z "$replicas" ] && { echo "Usage: k8s_scale_deployment <name> <replicas> [namespace]"; return 1; }
  
  kubectl scale deployment/$deployment --replicas=$replicas -n $ns
  kubectl rollout status deployment/$deployment -n $ns --timeout=5m
}

k8s_pod_logs() {
  local pod=$1
  local ns=${2:-$K8S_NAMESPACE}
  
  [ -z "$pod" ] && { echo "Usage: k8s_pod_logs <pod> [namespace]"; return 1; }
  
  kubectl logs $pod -n $ns -f --tail=100
}

k8s_get_resource_usage() {
  local ns=${1:-$K8S_NAMESPACE}
  echo "=== Pod Resource Usage in $ns ==="
  kubectl top pods -n $ns
  echo "=== Node Resource Usage ==="
  kubectl top nodes
}

k8s_rollout_status() {
  local deployment=$1
  local ns=${2:-$K8S_NAMESPACE}
  
  kubectl rollout status deployment/$deployment -n $ns
}
```

**Explanation:** Library with functions for Kubernetes cluster management: list deployments, scaling, log retrieval, resource monitoring, rollout status with namespace support.

---

## Solution 3: Database Operations Library

```bash
#!/bin/bash
# libs/database.sh - Database Operations Library

DB_TYPE=${DB_TYPE:-mysql}  # mysql, postgres
DB_HOST=${DB_HOST:-localhost}
DB_USER=${DB_USER:-root}
DB_PASS=${DB_PASS:-}

db_init() {
  case $DB_TYPE in
    mysql)
      command -v mysql >/dev/null || { echo "ERROR: mysql client not found"; return 1; }
      ;;
    postgres)
      command -v psql >/dev/null || { echo "ERROR: psql not found"; return 1; }
      ;;
    *)
      echo "ERROR: Unknown DB type: $DB_TYPE"
      return 1
      ;;
  esac
  echo "Database library initialized: $DB_TYPE"
}

db_backup() {
  local database=$1
  local backup_file="${database}_$(date +%Y%m%d_%H%M%S).sql"
  
  case $DB_TYPE in
    mysql)
      mysqldump -h $DB_HOST -u $DB_USER -p"$DB_PASS" $database > $backup_file
      ;;
    postgres)
      pg_dump -h $DB_HOST -U $DB_USER $database > $backup_file
      ;;
  esac
  
  [ $? -eq 0 ] && echo "Backup created: $backup_file" || return 1
}

db_restore() {
  local backup_file=$1
  local database=$2
  
  [ ! -f "$backup_file" ] && { echo "ERROR: Backup file not found"; return 1; }
  
  case $DB_TYPE in
    mysql)
      mysql -h $DB_HOST -u $DB_USER -p"$DB_PASS" $database < $backup_file
      ;;
    postgres)
      psql -h $DB_HOST -U $DB_USER -d $database -f $backup_file
      ;;
  esac
  
  [ $? -eq 0 ] && echo "Restore completed" || return 1
}

db_health_check() {
  case $DB_TYPE in
    mysql)
      mysql -h $DB_HOST -u $DB_USER -p"$DB_PASS" -e "SELECT 1;" &>/dev/null && echo "✓ MySQL healthy" || return 1
      ;;
    postgres)
      psql -h $DB_HOST -U $DB_USER -c "SELECT 1;" &>/dev/null && echo "✓ PostgreSQL healthy" || return 1
      ;;
  esac
}
```

**Explanation:** Multi-database library supporting MySQL and PostgreSQL for backup, restore, and health checks with configurable connection parameters.

---

## Solution 4: Docker and Container Library

```bash
#!/bin/bash
# libs/docker.sh - Docker and Container Library

REGISTRY=${REGISTRY:-localhost:5000}

docker_init() {
  command -v docker >/dev/null || { echo "ERROR: Docker not found"; return 1; }
  docker ps &>/dev/null || { echo "ERROR: Docker daemon not accessible"; return 1; }
  echo "Docker library initialized. Registry: $REGISTRY"
}

docker_build_image() {
  local app=$1
  local version=$2
  
  [ -z "$app" ] || [ -z "$version" ] && { echo "Usage: docker_build_image <app> <version>"; return 1; }
  
  echo "Building image: $app:$version"
  docker build -t ${REGISTRY}/${app}:${version} -t ${REGISTRY}/${app}:latest .
  [ $? -eq 0 ] && echo "✓ Build successful" || return 1
}

docker_push_image() {
  local app=$1
  local version=$2
  
  [ -z "$app" ] || [ -z "$version" ] && { echo "Usage: docker_push_image <app> <version>"; return 1; }
  
  docker push ${REGISTRY}/${app}:${version} && docker push ${REGISTRY}/${app}:latest
}

docker_run_container() {
  local name=$1
  local image=$2
  shift 2
  
  docker run -d --name $name $@ $image
}

docker_health_check() {
  local container=$1
  
  [ -z "$container" ] && { echo "Usage: docker_health_check <container>"; return 1; }
  
  local status=$(docker inspect --format='{{.State.Status}}' $container 2>/dev/null)
  echo "Container $container status: $status"
  [ "$status" = "running" ] && return 0 || return 1
}

docker_cleanup() {
  echo "Removing stopped containers..."
  docker container prune -f
  echo "Removing unused images..."
  docker image prune -f
}
```

**Explanation:** Docker library for building, pushing, running containers, checking health, and cleaning up resources with registry support.

---

## Solution 5: System Administration Library

```bash
#!/bin/bash
# libs/sysadmin.sh - System Administration Library

sysadmin_create_user() {
  local username=$1
  local groups=${2:-}
  
  [ -z "$username" ] && { echo "Usage: sysadmin_create_user <username> [groups]"; return 1; }
  
  id "$username" &>/dev/null && { echo "User already exists"; return 1; }
  
  sudo useradd -m -s /bin/bash $username
  
  if [ -n "$groups" ]; then
    sudo usermod -aG $groups $username
  fi
  
  echo "User created: $username"
}

sysadmin_set_permissions() {
  local path=$1
  local mode=$2
  local recursive=${3:-false}
  
  [ -z "$path" ] || [ -z "$mode" ] && { echo "Usage: sysadmin_set_permissions <path> <mode> [recursive]"; return 1; }
  
  if [ "$recursive" = "true" ]; then
    sudo chmod -R $mode $path
  else
    sudo chmod $mode $path
  fi
  
  echo "Permissions set: $path -> $mode"
}

sysadmin_get_system_info() {
  echo "=== System Information ==="
  echo "Hostname: $(hostname)"
  echo "Kernel: $(uname -r)"
  echo "CPU cores: $(nproc)"
  echo "Memory: $(free -h | grep Mem | awk '{print $2}')"
  echo "Disk: $(df -h / | tail -1 | awk '{print $2, "used:", $3}')"
}

sysadmin_find_large_files() {
  local size=${1:-100M}
  
  echo "Finding files larger than $size..."
  find / -size +$size -type f 2>/dev/null | head -20
}

sysadmin_manage_services() {
  local action=$1
  local service=$2
  
  [ -z "$action" ] || [ -z "$service" ] && { echo "Usage: sysadmin_manage_services <start|stop|restart|status> <service>"; return 1; }
  
  sudo systemctl $action $service
}
```

**Explanation:** System administration library for user management, permissions, system info, finding large files, and service management.

---

## Solution 6: Monitoring and Alerting Library

```bash
#!/bin/bash
# libs/monitoring.sh - Monitoring and Alerting Library

PROMETHEUS_URL=${PROMETHEUS_URL:-http://localhost:9090}
SLACK_WEBHOOK=${SLACK_WEBHOOK:-}

monitor_init() {
  echo "Monitoring library initialized. Prometheus: $PROMETHEUS_URL"
}

monitor_query_prometheus() {
  local query=$1
  
  [ -z "$query" ] && { echo "Usage: monitor_query_prometheus '<metric_query>'"; return 1; }
  
  curl -s "${PROMETHEUS_URL}/api/v1/query" --data-urlencode "query=$query" | jq '.data.result'
}

monitor_collect_metrics() {
  {
    echo "timestamp=$(date +%s)"
    echo "cpu=$(top -bn1 | grep 'Cpu(s)' | awk '{print $2}')"
    echo "memory=$(free | grep Mem | awk '{printf "%.1f", ($3/$2)*100}')"
    echo "disk=$(df / | tail -1 | awk '{print $5}')"
  }
}

monitor_send_notification() {
  local message=$1
  local severity=${2:-info}
  
  if [ -n "$SLACK_WEBHOOK" ]; then
    curl -X POST $SLACK_WEBHOOK -d "{\"text\":\"[$severity] $message\"}"
  fi
}

monitor_check_thresholds() {
  local metric=$1
  local threshold=$2
  
  local value=$(monitor_query_prometheus "$metric" | jq -r '.[] | .value[1]' 2>/dev/null || echo "0")
  
  if (( $(echo "$value > $threshold" | bc -l) )); then
    echo "ALERT: $metric=$value (threshold=$threshold)"
    return 1
  fi
}
```

**Explanation:** Monitoring library for querying Prometheus, collecting system metrics, sending notifications, and checking thresholds.

---

## Solution 7: Configuration Management Library

```bash
#!/bin/bash
# libs/config.sh - Configuration Management Library

config_load() {
  local config_file=$1
  
  [ ! -f "$config_file" ] && { echo "ERROR: Config file not found: $config_file"; return 1; }
  
  echo "Loading config: $config_file"
  
  while IFS='=' read -r key value; do
    [[ "$key" =~ ^#.*$ ]] && continue
    [[ -z "$key" ]] && continue
    
    value="${value//\"/}"
    export "$key"="$value"
  done < "$config_file"
  
  echo "✓ Config loaded"
}

config_validate() {
  local required_keys="$@"
  
  for key in $required_keys; do
    [ -z "${!key}" ] && { echo "ERROR: Required key missing: $key"; return 1; }
  done
  
  echo "✓ Config validation passed"
}

config_interpolate() {
  local text=$1
  
  # Replace ${VAR} with variable value
  while [[ $text =~ \$\{([A-Za-z_][A-Za-z_0-9]*)\} ]]; do
    varname="${BASH_REMATCH[1]}"
    text="${text//${BASH_REMATCH[0]}/${!varname}}"
  done
  
  echo "$text"
}

config_export() {
  echo "=== Configuration ==="
  env | grep -E "^[A-Z_]+" | sort
}
```

**Explanation:** Configuration library for loading files, validating keys, variable interpolation, and exporting settings.

---

## Solution 8: Networking Library

```bash
#!/bin/bash
# libs/networking.sh - Networking Library

net_test_connectivity() {
  local host=$1
  local port=${2:-80}
  
  [ -z "$host" ] && { echo "Usage: net_test_connectivity <host> [port]"; return 1; }
  
  timeout 3 bash -c "cat </dev/null > /dev/tcp/$host/$port" && echo "✓ Connected to $host:$port" || echo "✗ Cannot reach $host:$port"
}

net_check_port() {
  local host=$1
  local port=$2
  
  timeout 3 bash -c "cat </dev/null > /dev/tcp/$host/$port" 2>/dev/null && echo "✓ Port $port open" || echo "✗ Port $port closed"
}

net_dns_resolve() {
  local domain=$1
  
  [ -z "$domain" ] && { echo "Usage: net_dns_resolve <domain>"; return 1; }
  
  echo "=== DNS Resolution for $domain ==="
  nslookup $domain
}

net_get_ssl_cert_info() {
  local domain=$1
  local port=${2:-443}
  
  [ -z "$domain" ] && { echo "Usage: net_get_ssl_cert_info <domain> [port]"; return 1; }
  
  echo | openssl s_client -servername $domain -connect $domain:$port 2>/dev/null | openssl x509 -noout -dates -subject
}
```

**Explanation:** Networking library for testing connectivity, checking ports, DNS resolution, and SSL certificate information retrieval.

---

## Solution 9: Security Library

```bash
#!/bin/bash
# libs/security.sh - Security Library

VAULT_ADDR=${VAULT_ADDR:-http://localhost:8200}

sec_encrypt_file() {
  local file=$1
  local recipient=$2
  
  [ ! -f "$file" ] && { echo "ERROR: File not found"; return 1; }
  
  gpg --encrypt --armor --recipient "$recipient" "$file"
  echo "✓ Encrypted: ${file}.asc"
}

sec_decrypt_file() {
  local file=$1
  
  [ ! -f "$file" ] && { echo "ERROR: File not found"; return 1; }
  
  gpg --decrypt "$file"
}

sec_store_secret() {
  local secret_name=$1
  local secret_value=$2
  
  [ -z "$secret_name" ] || [ -z "$secret_value" ] && { echo "Usage: sec_store_secret <name> <value>"; return 1; }
  
  curl -s -X POST -H "X-Vault-Token: $VAULT_TOKEN" \
    -d "{\"data\":{\"value\":\"$secret_value\"}}" \
    $VAULT_ADDR/v1/secret/data/$secret_name
}

sec_audit_log() {
  local event=$1
  local user=${USER:-unknown}
  
  echo "[$(date +'%Y-%m-%d %H:%M:%S')] USER=$user EVENT=$event" >> audit.log
}
```

**Explanation:** Security library for encryption, vault integration, and audit logging.

---

## Solution 10: Complete Integration Library

```bash
#!/bin/bash
# libs/devops.sh - Complete Integration Library

# Source all libraries
[ -f ./libs/aws.sh ] && source ./libs/aws.sh
[ -f ./libs/kubernetes.sh ] && source ./libs/kubernetes.sh
[ -f ./libs/database.sh ] && source ./libs/database.sh
[ -f ./libs/docker.sh ] && source ./libs/docker.sh
[ -f ./libs/sysadmin.sh ] && source ./libs/sysadmin.sh
[ -f ./libs/monitoring.sh ] && source ./libs/monitoring.sh
[ -f ./libs/config.sh ] && source ./libs/config.sh
[ -f ./libs/networking.sh ] && source ./libs/networking.sh
[ -f ./libs/security.sh ] && source ./libs/security.sh

# Unified initialization
devops_init() {
  echo "=== DevOps Toolkit Initialization ==="
  
  # Check available tools and init
  command -v aws >/dev/null && aws_init || echo "AWS CLI not available"
  command -v kubectl >/dev/null && k8s_init || echo "kubectl not available"
  command -v docker >/dev/null && docker_init || echo "Docker not available"
  
  echo "✓ DevOps toolkit ready"
}

# Health check
devops_health_check() {
  echo "=== Health Check ==="
  command -v aws >/dev/null && echo "✓ AWS CLI" || echo "✗ AWS CLI"
  command -v kubectl >/dev/null && echo "✓ kubectl" || echo "✗ kubectl"
  command -v docker >/dev/null && echo "✓ Docker" || echo "✗ Docker"
  command -v mysql >/dev/null && echo "✓ MySQL client" || echo "✗ MySQL client"
}
```

**Explanation:** Master integration library that sources all specialized libraries, provides unified initialization, and health checks.
