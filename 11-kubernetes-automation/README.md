# Kubernetes Automation

## What You'll Learn
- Kubernetes cluster setup and configuration
- kubectl basics (nodes, pods, deployments)
- Creating and managing deployments
- Services and networking
- ConfigMaps and Secrets
- StatefulSets and DaemonSets
- Helm package management
- Monitoring and logging in Kubernetes

## Prerequisites
- Completed: [10-cloud-automation-aws](../10-cloud-automation-aws)
- kubectl installed and configured
- Access to Kubernetes cluster (minikube/EKS/GKE)
- Basic Docker knowledge
- Understanding of YAML syntax

## Key Concepts
- **Pod**: Smallest deployable unit (one or more containers)
- **Deployment**: Manages replica sets of pods
- **Service**: Exposes pods internally/externally
- **StatefulSet**: Manages stateful application pods
- **DaemonSet**: Ensures pod runs on all/selected nodes
- **ConfigMap**: Store configuration data
- **Secret**: Store sensitive data (encrypted)
- **Helm**: Package manager for Kubernetes

## Hands-on Lab: Deploy Application to Kubernetes

### Step 1: Check Cluster Status
```bash
kubectl cluster-info
kubectl get nodes

# Expected output:
# NAME       STATUS   ROLES    AGE   VERSION
# minikube   Ready    master   5d    v1.24.0
```

### Step 2: Create Namespace
```bash
kubectl create namespace production

# Verify
kubectl get namespaces

# Expected: production namespace listed
```

### Step 3: Create Deployment
```bash
cat > app-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
EOF

kubectl apply -f app-deployment.yaml

# Expected: deployment.apps/web-app created
```

### Step 4: Expose Service
```bash
kubectl expose deployment web-app \
  --type=LoadBalancer \
  --port=80 \
  --target-port=80 \
  -n production

# Verify
kubectl get svc -n production

# Expected: LoadBalancer with EXTERNAL-IP
```

### Step 5: Monitor Deployment
```bash
kubectl get pods -n production

# Expected: 3 nginx pods running
kubectl logs -f deployment/web-app -n production

# Expected: nginx access logs
```

## Validation Checklist
- [ ] Cluster accessible with kubectl
- [ ] Namespace created successfully
- [ ] Deployment replicas running
- [ ] Service endpoints accessible
- [ ] Pods are in Running state
- [ ] Logs visible and accessible
- [ ] Service has endpoint IPs

## Cleanup
```bash
kubectl delete deployment web-app -n production
kubectl delete svc web-app -n production
kubectl delete namespace production
```

## Common Mistakes
1. **Wrong context**: Using wrong cluster
2. **Resource not found**: Missing namespace specification
3. **Image pull errors**: Image doesn't exist or registry unreachable
4. **Port conflicts**: Service port already in use
5. **Insufficient resources**: Cluster doesn't have enough CPU/memory

## Troubleshooting

### pods stuck in pending
- **Cause**: Insufficient resources or image pull error
- **Solution**: `kubectl describe pod <pod>`, check events

### Connection refused
- **Cause**: Service not bound correctly
- **Solution**: Verify selector labels match pod labels

### ImagePullBackOff error
- **Cause**: Docker image not found
- **Solution**: Check image name, registry credentials

### CrashLoopBackOff
- **Cause**: Application crashing
- **Solution**: `kubectl logs <pod>`, check application

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [Kubernetes cheatsheet](cheatsheet.md)
3. Deploy multi-tier application
4. Implement Ingress controller
5. Set up Helm charts for templating
