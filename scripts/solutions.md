# Scripts - Solutions

## Solution 1: Logging Utility Module

```bash
#!/bin/bash
# scripts/logger.sh
# Source this file to use logging functions

# Color codes
RED='\033[0;31m'
YELLOW='\033[1;33m'
GREEN='\033[0;32m'
BLUE='\033[0;34m'
NC='\033[0m'

log_info() {
  echo -e "${GREEN}[$(date +'%Y-%m-%d %H:%M:%S')] INFO${NC} $*"
}

log_warn() {
  echo -e "${YELLOW}[$(date +'%Y-%m-%d %H:%M:%S')] WARN${NC} $*"
}

log_error() {
  echo -e "${RED}[$(date +'%Y-%m-%d %H:%M:%S')] ERROR${NC} $*" >&2
}

log_debug() {
  [ "$DEBUG" = "1" ] && echo -e "${BLUE}[$(date +'%Y-%m-%d %H:%M:%S')] DEBUG${NC} $*"
}
```

**Explanation:** Provides colored log functions with timestamps. Logs to stderr for errors. DEBUG flag enables debug output.

---

## Solution 2: Health Check Utility

```bash
#!/bin/bash
# scripts/health_check.sh
source ./scripts/logger.sh

check_http() {
  local url=$1
  local timeout=${2:-5}
  log_info "Checking HTTP: $url"
  curl -f -s -m $timeout "$url" >/dev/null && { log_info "✓ HTTP OK"; return 0; } || { log_error "✗ HTTP Failed"; return 1; }
}

check_port() {
  local host=$1
  local port=$2
  local timeout=${3:-5}
  log_info "Checking port: $host:$port"
  timeout $timeout bash -c "cat </dev/null > /dev/tcp/$host/$port" && { log_info "✓ Port open"; return 0; } || { log_error "✗ Port closed"; return 1; }
}

check_process() {
  local process=$1
  log_info "Checking process: $process"
  pgrep -f "$process" >/dev/null && { log_info "✓ Process running"; return 0; } || { log_error "✗ Process not found"; return 1; }
}

retry_with_backoff() {
  local max_attempts=$1
  shift
  local attempt=1
  local wait=1
  
  while [ $attempt -le $max_attempts ]; do
    "$@" && return 0
    log_warn "Attempt $attempt failed, waiting ${wait}s..."
    sleep $wait
    wait=$((wait * 2))
    ((attempt++))
  done
  return 1
}

# Example usage
[ $# -lt 2 ] && { echo "Usage: $0 <http|port|process> <arg> [timeout]"; exit 1; }
check_${1} $2 $3
```

**Explanation:** Functions for checking HTTP endpoints, TCP ports, and processes. Retry logic with exponential backoff.

---

## Solution 3: Configuration File Parser

```bash
#!/bin/bash
# scripts/config_parser.sh
source ./scripts/logger.sh

load_config() {
  local config_file=$1
  [ ! -f "$config_file" ] && { log_error "Config file not found: $config_file"; return 1; }
  
  log_info "Loading config from: $config_file"
  
  while IFS='=' read -r key value; do
    [[ "$key" =~ ^#.*$ ]] && continue  # Skip comments
    [[ -z "$key" ]] && continue  # Skip empty lines
    
    value="${value//\"/}"  # Remove quotes
    export "$key"="$value"
    log_debug "Loaded: $key=$value"
  done < "$config_file"
}

validate_config() {
  local required_keys="$@"
  for key in $required_keys; do
    [ -z "${!key}" ] && { log_error "Required key missing: $key"; return 1; }
  done
  log_info "✓ Config validation passed"
}

show_config() {
  echo "=== Configuration ==="
  env | grep -E "^[A-Z_]+" | sort
}

# Usage
load_config "$1" || exit 1
validate_config APP_NAME DATABASE_URL || exit 1
show_config
```

**Explanation:** Parses key=value config files, exports variables, validates required keys. Shows loaded configuration.

---

## Solution 4: Backup and Restore Utilities

```bash
#!/bin/bash
# scripts/backup.sh
source ./scripts/logger.sh

backup_directory() {
  local source=$1
  local backup_dir=${2:-.}
  local backup_file="$backup_dir/backup_$(basename $source)_$(date +%Y%m%d_%H%M%S).tar.gz"
  
  log_info "Backing up: $source"
  tar czf "$backup_file" -C "$(dirname $source)" "$(basename $source)" || { log_error "Backup failed"; return 1; }
  log_info "✓ Backup saved to: $backup_file"
  echo "$backup_file"
}

backup_database() {
  local db_name=$1
  local backup_file="backup_${db_name}_$(date +%Y%m%d_%H%M%S).sql"
  
  log_info "Backing up database: $db_name"
  mysqldump -u$DB_USER -p$DB_PASS $db_name > "$backup_file" || { log_error "DB backup failed"; return 1; }
  log_info "✓ DB backup saved to: $backup_file"
}

restore_from_backup() {
  local backup_file=$1
  local dest=$2
  
  [ ! -f "$backup_file" ] && { log_error "Backup file not found"; return 1; }
  
  log_info "Restoring from: $backup_file to $dest"
  tar xzf "$backup_file" -C "$dest" || { log_error "Restore failed"; return 1; }
  log_info "✓ Restore complete"
}

cleanup_old_backups() {
  local backup_dir=$1
  local days=${2:-7}
  
  log_info "Cleaning backups older than $days days in $backup_dir"
  find "$backup_dir" -name "backup_*" -mtime +$days -delete
  log_info "✓ Cleanup complete"
}
```

**Explanation:** Functions for backing up directories and databases, restoring from backups, cleaning old backups.

---

## Solution 5: Process Management Utilities

```bash
#!/bin/bash
# scripts/process_manager.sh
source ./scripts/logger.sh

start_process() {
  local process_name=$1
  local command=$2
  local pidfile="${3:-./$process_name.pid}"
  
  [ -f "$pidfile" ] && { log_warn "Process already running (PID: $(cat $pidfile))"; return 1; }
  
  log_info "Starting $process_name: $command"
  $command > "${process_name}.log" 2>&1 &
  echo $! > "$pidfile"
  log_info "✓ Started with PID: $(cat $pidfile)"
}

stop_process() {
  local pidfile=$1
  [ ! -f "$pidfile" ] && { log_error "PID file not found"; return 1; }
  
  local pid=$(cat "$pidfile")
  log_info "Stopping process PID: $pid"
  
  kill -TERM $pid 2>/dev/null
  sleep 2
  if kill -0 $pid 2>/dev/null; then
    log_warn "Process didn't stop gracefully, killing..."
    kill -KILL $pid
  fi
  rm -f "$pidfile"
  log_info "✓ Process stopped"
}

is_running() {
  local pidfile=$1
  [ -f "$pidfile" ] && kill -0 $(cat "$pidfile") 2>/dev/null && return 0 || return 1
}

auto_restart() {
  local pidfile=$1
  local command=$2
  local check_interval=${3:-10}
  
  while true; do
    if ! is_running "$pidfile"; then
      log_warn "Process died, restarting..."
      start_process "auto_restart" "$command" "$pidfile"
    fi
    sleep $check_interval
  done
}
```

**Explanation:** Start/stop processes with PID files, check status, auto-restart crashed processes with signal handling.

---

## Solution 6: Git Operations Wrapper

```bash
#!/bin/bash
# scripts/git_safe.sh
source ./scripts/logger.sh

safe_push() {
  local current_branch=$(git rev-parse --abbrev-ref HEAD)
  [ "$current_branch" = "main" ] && { log_error "Cannot push directly to main"; return 1; }
  
  git diff --quiet || { log_warn "Uncommitted changes exist"; return 1; }
  
  log_info "Pushing branch: $current_branch"
  git push origin "$current_branch"
}

check_branch() {
  local expected=$1
  local current=$(git rev-parse --abbrev-ref HEAD)
  [ "$current" != "$expected" ] && { log_error "Wrong branch. Expected: $expected, Current: $current"; return 1; }
}

safe_merge() {
  local source_branch=$1
  local target_branch=${2:-main}
  
  check_branch "$target_branch" || return 1
  
  log_info "Merging $source_branch into $target_branch"
  read -p "Confirm merge? (y/n) " -n 1
  [ "$REPLY" != "y" ] && { log_warn "Merge cancelled"; return 1; }
  
  git merge "$source_branch" || { log_error "Merge conflict"; return 1; }
  log_info "✓ Merge complete"
}

create_branch() {
  local branch_name=$1
  log_info "Creating branch: $branch_name"
  git checkout -b "$branch_name"
}
```

**Explanation:** Git operations with safety checks: prevent accidental main branch pushes, confirm merges, check uncommitted changes.

---

## Solution 7: Environment Management Helper

```bash
#!/bin/bash
# scripts/env_manager.sh
source ./scripts/logger.sh

load_environment() {
  local env=$1
  local env_dir="./config/envs"
  local env_file="$env_dir/$env.conf"
  
  [ ! -f "$env_file" ] && { log_error "Environment not found: $env"; return 1; }
  
  log_info "Loading environment: $env"
  source "$env_file"
  log_info "✓ Environment loaded: $env"
}

validate_environment() {
  log_info "Validating environment setup..."
  
  [ -z "$APP_NAME" ] && { log_error "APP_NAME not set"; return 1; }
  [ -z "$KUBECONFIG" ] && { log_error "KUBECONFIG not set"; return 1; }
  
  kubectl cluster-info &>/dev/null || { log_error "K8s cluster not accessible"; return 1; }
  log_info "✓ Environment validation passed"
}

show_current_env() {
  echo "=== Current Environment ==="
  echo "APP_NAME: $APP_NAME"
  echo "ENVIRONMENT: $ENVIRONMENT"
  echo "KUBECONFIG: $KUBECONFIG"
  echo "Cluster: $(kubectl cluster-info | head -1)"
}

# Usage
[ $# -lt 1 ] && { echo "Usage: $0 <env>"; exit 1; }
load_environment "$1" && validate_environment && show_current_env
```

**Explanation:** Load environment-specific configs, validate setup, show current environment. Support for dev/staging/prod.

---

## Solution 8: Metrics Collection and Reporting

```bash
#!/bin/bash
# scripts/metrics.sh
source ./scripts/logger.sh

METRICS_DIR="./metrics"
[ ! -d "$METRICS_DIR" ] && mkdir -p "$METRICS_DIR"

collect_metrics() {
  local timestamp=$(date +%Y%m%d_%H%M%S)
  local metrics_file="$METRICS_DIR/metrics_$timestamp.txt"
  
  log_info "Collecting system metrics"
  {
    echo "timestamp=$(date +%s)"
    echo "cpu=$(top -bn1 | grep 'Cpu(s)' | awk '{print $2}' | cut -d'%' -f1)"
    echo "memory=$(free | grep Mem | awk '{printf "%.1f", ($3/$2)*100}')"
    echo "disk=$(df / | tail -1 | awk '{print $5}' | cut -d'%' -f1)"
  } > "$metrics_file"
  
  log_info "✓ Metrics saved to: $metrics_file"
}

generate_report() {
  log_info "Generating metrics report"
  
  echo "=== Metrics Report ==="
  for file in $(ls -t $METRICS_DIR/metrics_*.txt | head -5); do
    source "$file"
    echo "Time: $timestamp CPU: ${cpu}% MEM: ${memory}% DISK: ${disk}%"
  done
}

compare_baseline() {
  local current_file=$1
  local baseline_file=${2:-$METRICS_DIR/baseline.txt}
  
  [ ! -f "$baseline_file" ] && { log_error "Baseline not found"; return 1; }
  
  source "$current_file"
  source "$baseline_file"
  
  echo "CPU diff: $(echo "$cpu - $baseline_cpu" | bc)%"
  echo "Memory diff: $(echo "$memory - $baseline_memory" | bc)%"
}
```

**Explanation:** Collects CPU, memory, disk metrics with timestamps. Generates reports and compares against baselines.

---

## Solution 9: Deployment Helper Functions

```bash
#!/bin/bash
# scripts/deploy.sh
source ./scripts/logger.sh

build_image() {
  local app=$1
  local version=$2
  local registry=${3:-localhost:5000}
  
  log_info "Building image: $app:$version"
  docker build -t ${registry}/${app}:${version} . || { log_error "Build failed"; return 1; }
  log_info "✓ Image built: ${registry}/${app}:${version}"
}

push_image() {
  local app=$1
  local version=$2
  local registry=${3:-localhost:5000}
  
  log_info "Pushing image: ${registry}/${app}:${version}"
  docker push ${registry}/${app}:${version} || { log_error "Push failed"; return 1; }
}

deploy_to_k8s() {
  local deployment=$1
  local image=$2
  local namespace=${3:-default}
  
  log_info "Deploying: $deployment with image: $image"
  kubectl set image deployment/$deployment app=$image -n $namespace
  kubectl rollout status deployment/$deployment -n $namespace
}

validate_deployment() {
  local deployment=$1
  local namespace=${2:-default}
  
  log_info "Validating deployment: $deployment"
  kubectl get deployment $deployment -n $namespace -o json | jq '.status.replicas == .status.readyReplicas' | grep true >/dev/null || { log_error "Deployment failed"; return 1; }
  log_info "✓ Deployment validated"
}

rollback_deployment() {
  local deployment=$1
  local namespace=${2:-default}
  
  log_error "Rolling back deployment: $deployment"
  kubectl rollout undo deployment/$deployment -n $namespace
  kubectl rollout status deployment/$deployment -n $namespace
}
```

**Explanation:** Build Docker images, push to registry, deploy to K8s, validate, and rollback functions.

---

## Solution 10: Complete DevOps Toolkit Script

```bash
#!/bin/bash
# scripts/devops-toolkit.sh
source ./scripts/logger.sh
source ./scripts/config_parser.sh
source ./scripts/health_check.sh
source ./scripts/backup.sh
source ./scripts/deploy.sh

usage() {
  cat << EOF
DevOps Toolkit
Usage: $0 <command> [args]

Commands:
  health <service> <port>         Check service health
  backup <source> [dest]          Backup directory
  restore <backup> <dest>         Restore from backup
  deploy <app> <version>          Deploy application
  rollback <deployment>           Rollback deployment
  config <file>                   Load configuration
  logs <deployment> [lines]       View deployment logs

Examples:
  $0 health myapp 8080
  $0 backup /data /backups
  $0 deploy myapp v1.2.3
EOF
}

main() {
  [ $# -lt 1 ] && { usage; exit 1; }
  
  case "$1" in
    health)
      check_http "http://${2}:${3}/health"
      ;;
    backup)
      backup_directory "$2" "$3"
      ;;
    restore)
      restore_from_backup "$2" "$3"
      ;;
    deploy)
      build_image "$2" "$3" && push_image "$2" "$3" && deploy_to_k8s "$2" "$2:$3"
      ;;
    rollback)
      rollback_deployment "$2"
      ;;
    config)
      load_config "$2" && validate_config && show_config
      ;;
    logs)
      kubectl logs deployment/${2} --tail=${3:-50} -f
      ;;
    *)
      log_error "Unknown command: $1"
      usage
      exit 1
      ;;
  esac
}

main "$@"
```

**Explanation:** Main toolkit script that provides CLI interface to all utilities. Sources all modules. Provides help and command routing.
