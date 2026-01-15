# Real-World DevOps Projects

## What You'll Learn

- Implement end-to-end DevOps pipelines
- Deploy multi-tier applications with automation
- Set up monitoring, logging, and alerting
- Implement infrastructure as code (IaC)
- Build disaster recovery and backup strategies
- Manage secrets and security in production
- Optimize deployments and rollbacks
- Create scalable, maintainable infrastructure

## Prerequisites

- Modules 00-14 (foundational knowledge)
- Understanding of CI/CD pipelines (module 12)
- Kubernetes basics (module 11)
- AWS fundamentals (module 10)
- Security concepts (module 13)

## Key Concepts

### Real-World Scenarios
- Multi-environment deployments (dev, staging, prod)
- Blue-green deployments
- Canary releases
- Rolling updates with monitoring
- Automated rollbacks on failures

### Infrastructure Patterns
- Infrastructure as Code (Terraform, CloudFormation)
- Container orchestration
- Database migration automation
- Load balancing and scaling
- High availability setup

### Operational Excellence
- Automated testing and validation
- Centralized logging (ELK, Splunk)
- Performance monitoring (Prometheus, Grafana)
- Incident response automation
- Change management and approvals

## Hands-on Lab: Deploy a Web Application

### Step 1: Build Application Infrastructure
```bash
# Create basic infrastructure template
cat > deploy_infra.sh << 'EOF'
#!/bin/bash
APP_NAME="my-app"
ENVIRONMENT="staging"
REGION="us-east-1"

echo "Deploying infrastructure for $APP_NAME in $ENVIRONMENT"
# Would use Terraform/CloudFormation
echo "Infrastructure deployment completed"
EOF
bash deploy_infra.sh
```

**Expected Output:**
```
Deploying infrastructure for my-app in staging
Infrastructure deployment completed
```

### Step 2: Build and Push Docker Image
```bash
docker build -t my-app:v1.0 .
docker tag my-app:v1.0 registry.com/my-app:v1.0
docker push registry.com/my-app:v1.0
```

### Step 3: Deploy to Kubernetes
```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/my-app -n staging
```

### Step 4: Configure Monitoring
```bash
# Deploy Prometheus scrape config
kubectl apply -f prometheus-config.yaml
# Verify metrics collection
curl http://prometheus:9090/api/v1/targets
```

### Step 5: Validate Deployment
```bash
# Health checks
kubectl get pods -n staging
curl http://app-endpoint/health
# Check logs
kubectl logs -f deployment/my-app -n staging
```

## Validation

- [ ] Infrastructure provisioned successfully
- [ ] Application deployed to all environments
- [ ] Monitoring and logging active
- [ ] Health checks passing
- [ ] Rollback mechanism tested
- [ ] Alerts configured and working

## Cleanup

```bash
kubectl delete -f deployment.yaml
terraform destroy  # or appropriate cleanup
```

## Common Mistakes

- Not testing failover/rollback procedures
- Insufficient monitoring before production
- Manual steps in "automated" pipelines
- No change management approval process
- Secrets in code or logs
- Inconsistent configs across environments

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Deployment fails | Check image availability, resource limits |
| Pod crashes | Review logs: `kubectl logs <pod>` |
| Metrics not appearing | Verify scrape config, check port exposure |
| Rollback stuck | Check resource quotas, replica limits |
| High latency | Check network policies, resource contention |

## Next Steps

- Implement GitOps (ArgoCD, Flux)
- Add advanced observability (distributed tracing)
- Setup automatic scaling based on metrics
- Implement chaos engineering for resilience
- Create runbooks for incident response
