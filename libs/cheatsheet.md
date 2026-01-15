# Libs - Cheatsheet

## Library Structure and Loading

| Task | Command | Purpose |
|------|---------|---------|
| Create library | `cat > libs/mylib.sh << 'EOF'` | Start new library file |
| Source library | `source ./libs/mylib.sh` | Load functions into shell |
| Alternative source | `. ./libs/mylib.sh` | Same as source |
| Check function exists | `type function_name` | Verify function loaded |
| List functions | `set \| grep "declare -f"` | Show all defined functions |
| Export function | `export -f function_name` | Make available to subshells |

## Library Design Patterns

| Pattern | Purpose | Example |
|---------|---------|---------|
| Namespace prefix | Avoid conflicts | `aws_list_instances` not just `list_instances` |
| Init function | Setup and validate | `aws_init()` checks AWS CLI availability |
| Error checking | Input validation | `[ -z "$var" ] && { echo "Error"; return 1; }` |
| Default values | Set reasonable defaults | `${1:-default_value}` |
| Logging | Provide visibility | Use consistent log format |
| Documentation | Comment functions | `# Purpose, usage, example` |

## AWS Library Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `aws_init()` | Validate AWS CLI | Check command availability |
| `aws_list_instances(region)` | List EC2 instances | `aws_list_instances us-east-1` |
| `aws_list_s3_buckets()` | Show S3 buckets | List with sizes |
| `aws_get_sg_rules(sg-id)` | Security group rules | Show ingress/egress |
| `aws_create_snapshot(vol-id)` | Backup EBS volume | Create snapshot |

## Kubernetes Library Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `k8s_init()` | Validate kubectl | Check cluster access |
| `k8s_get_deployments(ns)` | List deployments | `k8s_get_deployments prod` |
| `k8s_scale_deployment(name,replicas,ns)` | Change pod count | `k8s_scale_deployment app 5 prod` |
| `k8s_pod_logs(pod,ns)` | Stream pod logs | `k8s_pod_logs web-1 prod` |
| `k8s_rollout_status(deploy,ns)` | Monitor deployment | Watch rollout progress |

## Database Library Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `db_init()` | Validate DB client | Check MySQL/PostgreSQL |
| `db_backup(database)` | Backup database | Creates .sql file |
| `db_restore(backup,database)` | Restore from backup | Restore schema and data |
| `db_health_check()` | Test connectivity | SELECT 1 query |
| `db_migrate(scripts)` | Run migration scripts | Execute schema changes |

## Docker Library Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `docker_init()` | Validate Docker | Check daemon running |
| `docker_build_image(app,version)` | Build image | Create and tag image |
| `docker_push_image(app,version)` | Push to registry | Upload to registry |
| `docker_run_container(name,image)` | Start container | Run with options |
| `docker_health_check(container)` | Check status | Verify container running |
| `docker_cleanup()` | Remove unused | Prune images/containers |

## Configuration Library Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `config_load(file)` | Load config | Parse key=value file |
| `config_validate(keys)` | Check required | `config_validate KEY1 KEY2` |
| `config_interpolate(text)` | Replace vars | Convert `${VAR}` to values |
| `config_export()` | Show config | Display all env vars |

## Library Best Practices

| Practice | Reason | Example |
|----------|--------|---------|
| Consistent naming | Easy to remember | `lib_action_object` format |
| Single responsibility | Easier to test | One thing per function |
| Return codes | Integration friendly | Return 0=success, 1=failure |
| Clear documentation | Self-explanatory | Comments above function |
| Input validation | Prevent errors | Check args, file existence |
| Error messages | Debugging help | Meaningful error text |
| Default parameters | Flexibility | `${1:-default}` syntax |

## Common Library Patterns

### Library with Init and Cleanup
```bash
lib_init() {
  echo "Initializing library..."
}

lib_cleanup() {
  echo "Cleaning up..."
}

trap lib_cleanup EXIT
```

### Library with Logging
```bash
lib_log() {
  echo "[$(date +'%H:%M:%S')] $*" >&2
}

lib_log "Starting operation"
```

### Library with Configuration
```bash
lib_load_config() {
  [ -f ~/.lib.conf ] && source ~/.lib.conf
}

lib_init() {
  lib_load_config
  [ -z "$VAR" ] && export VAR="default"
}
```

### Library with Fallbacks
```bash
lib_cmd() {
  command -v $1 >/dev/null 2>&1 && { $@; return 0; }
  echo "ERROR: $1 not found"
  return 1
}

lib_cmd docker ps || lib_cmd podman ps
```

## Testing Library Functions

| Command | Purpose | Example |
|---------|---------|---------|
| `source lib.sh && function_name` | Test function | Run after sourcing |
| `bash -n lib.sh` | Syntax check | Validate syntax |
| `type function_name` | Show function | Display definition |
| `declare -f function_name` | List function | Show code |
| `unset function_name` | Remove function | Test reloading |

## Library Organization

```
libs/
├── aws.sh          # AWS operations
├── kubernetes.sh   # K8s utilities
├── database.sh     # DB operations
├── docker.sh       # Container functions
├── monitoring.sh   # Monitoring/alerting
├── config.sh       # Configuration mgmt
└── devops.sh       # Master integration
```

## Quick Library Template

```bash
#!/bin/bash
# libs/template.sh - Description

# Configuration
DEBUG=${DEBUG:-0}

# Logging
lib_log() { [ "$DEBUG" = "1" ] && echo "[DEBUG] $*"; }

# Init
lib_init() {
  echo "Initializing library..."
  command -v required_tool >/dev/null || { echo "ERROR: required_tool not found"; return 1; }
}

# Utility functions
lib_function() {
  local arg=$1
  [ -z "$arg" ] && { echo "Usage: lib_function <arg>"; return 1; }
  lib_log "Running lib_function with $arg"
  # Implementation
}

# Init on source
lib_init
```

## Library Integration Checklist

- [ ] All functions prefixed with library name
- [ ] init() function validates dependencies
- [ ] Error handling for all inputs
- [ ] Logging for debugging
- [ ] Documentation in comments
- [ ] Return proper exit codes
- [ ] No hardcoded paths/values
- [ ] Works when sourced
- [ ] No unexpected side effects
- [ ] Can be sourced multiple times
