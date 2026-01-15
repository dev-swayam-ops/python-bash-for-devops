# Libs - Exercises

## Exercise 1: AWS Operations Library
Create a library with functions for common AWS operations: list instances, manage security groups, get costs, manage S3 buckets.

**Requirements:**
- Function: aws_init (validate AWS CLI)
- Function: aws_list_instances (by region)
- Function: aws_get_sg_rules (show security group rules)
- Function: aws_list_s3_buckets (list with sizes)
- Function: aws_create_snapshot (backup EC2 volume)
- Error handling for invalid regions
- Support for AWS profiles
- Consistent output formatting

## Exercise 2: Kubernetes Utilities Library
Build library for Kubernetes operations: cluster info, deployment management, scaling, log retrieval.

**Requirements:**
- Function: k8s_init (check kubectl, context)
- Function: k8s_get_deployments (list by namespace)
- Function: k8s_scale_deployment (change replicas)
- Function: k8s_rollout_status (monitor rollout)
- Function: k8s_pod_logs (collect logs)
- Function: k8s_get_resource_usage (CPU/memory)
- Support multiple namespaces
- Context switching support

## Exercise 3: Database Operations Library
Create library for database management: backups, restores, schema migrations, query execution.

**Requirements:**
- Function: db_backup (MySQL, PostgreSQL)
- Function: db_restore (from backup file)
- Function: db_migrate (execute migration scripts)
- Function: db_query (execute queries)
- Function: db_health_check (verify connectivity)
- Support multiple database types
- Connection pooling/caching
- Transaction support

## Exercise 4: Docker and Container Library
Build library for container operations: image building, pushing, running containers, network management.

**Requirements:**
- Function: docker_build_image (with tagging)
- Function: docker_push_image (to registry)
- Function: docker_run_container (with options)
- Function: docker_health_check (container status)
- Function: docker_cleanup (remove unused images)
- Support multiple registries
- Network configuration
- Volume mounting

## Exercise 5: System Administration Library
Create library for system tasks: user management, file permissions, system info, process management.

**Requirements:**
- Function: sysadmin_create_user (with groups)
- Function: sysadmin_set_permissions (recursively)
- Function: sysadmin_get_system_info (hardware details)
- Function: sysadmin_find_large_files (by size)
- Function: sysadmin_check_disk_space (per mount)
- Function: sysadmin_manage_services (start/stop/restart)
- Support different Linux distributions

## Exercise 6: Monitoring and Alerting Library
Build library for monitoring operations: collect metrics, query monitoring systems, create alerts.

**Requirements:**
- Function: monitor_collect_metrics (system stats)
- Function: monitor_query_prometheus (metrics queries)
- Function: monitor_get_alerts (from alertmanager)
- Function: monitor_create_alert (define new alert)
- Function: monitor_send_notification (Slack, email)
- Function: monitor_check_thresholds (trigger actions)
- Support multiple monitoring backends

## Exercise 7: Configuration Management Library
Create library for config handling: load configs, merge configs, template rendering, validation.

**Requirements:**
- Function: config_load (from file, env, vault)
- Function: config_merge (combine multiple configs)
- Function: config_validate (against schema)
- Function: config_interpolate (variable substitution)
- Function: config_export (as env vars)
- Support YAML, JSON, INI formats
- Template variable replacement
- Environment-specific overrides

## Exercise 8: Networking Library
Build library for network operations: DNS queries, port testing, SSL cert checks, network diagnostics.

**Requirements:**
- Function: net_test_connectivity (TCP/ICMP)
- Function: net_check_port (open/closed)
- Function: net_dns_resolve (forward/reverse)
- Function: net_get_ssl_cert_info (expiry, issuer)
- Function: net_test_latency (ping, traceroute)
- Function: net_get_route_info (trace path)
- Support IPv4 and IPv6

## Exercise 9: Security Library
Create library for security operations: encrypt/decrypt, secret management, audit logging, vulnerability scanning.

**Requirements:**
- Function: sec_encrypt_file (GPG)
- Function: sec_decrypt_file (GPG)
- Function: sec_store_secret (vault integration)
- Function: sec_retrieve_secret (from vault)
- Function: sec_audit_log (security events)
- Function: sec_scan_vulnerabilities (check)
- Function: sec_verify_signature (code signing)

## Exercise 10: Complete Integration Library
Build comprehensive library combining multiple domain libraries with unified interface and dependency management.

**Requirements:**
- Import all previous libraries
- Provide unified namespace (lib_*)
- Auto-detect available tools
- Fallback to alternatives
- Unified logging and error handling
- Health check for dependencies
- Version tracking
- Plugin system for extensions
