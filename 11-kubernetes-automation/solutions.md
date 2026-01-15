# Kubernetes Automation Solutions

## Solution 1: Cluster Information and Nodes

```bash
#!/bin/bash
# cluster_info.sh - Show cluster and node information

echo "=== Kubernetes Cluster Information ==="
echo ""

# Check cluster connectivity
if ! kubectl cluster-info > /dev/null 2>&1; then
    echo "✗ Cluster unreachable"
    exit 1
fi

# Cluster info
echo "Cluster Info:"
kubectl cluster-info | grep -E "Kubernetes|DNS"

echo ""
echo "Kubernetes Version:"
kubectl version --short

echo ""
echo "=== Node Status ==="
kubectl get nodes -o wide

echo ""
echo "Node Capacity:"
kubectl describe nodes | grep -E "Name:|cpu:|memory:" | paste - - -
```

**Explanation**: Verifies connectivity, displays cluster details, shows node status and capacity in formatted output.

---

## Solution 2: Pod Listing and Filtering

```bash
#!/bin/bash
# pod_lister.sh - List and filter pods

NAMESPACE=${1:-all}

echo "=== Pod Listing ==="
echo ""

if [ "$NAMESPACE" = "all" ]; then
    echo "All Namespaces:"
    kubectl get pods --all-namespaces -o wide
    
    # Count by state
    echo ""
    echo "Pod Count by State:"
    kubectl get pods --all-namespaces -o jsonpath='{range .items[*]}{.status.phase}{"\n"}{end}' | sort | uniq -c
    
else
    echo "Namespace: $NAMESPACE"
    kubectl get pods -n "$NAMESPACE" -o wide
fi

echo ""
echo "✓ Pod listing complete"
```

**Explanation**: Lists pods with filtering by namespace, counts by state using jsonpath for detailed status.

---

## Solution 3: Create and Delete Deployment

```bash
#!/bin/bash
# deployment_manager.sh - Create, monitor, and delete deployment

IMAGE=${1:-nginx}
REPLICAS=${2:-3}
DEPLOY_NAME="test-deploy"

echo "Creating deployment: $DEPLOY_NAME"
echo "  Image: $IMAGE"
echo "  Replicas: $REPLICAS"

# Create deployment
kubectl create deployment "$DEPLOY_NAME" --image="$IMAGE" --replicas="$REPLICAS"

if [ $? -ne 0 ]; then
    echo "✗ Deployment creation failed"
    exit 1
fi

echo "✓ Deployment created"

# Wait for rollout
echo "Waiting for deployment to be ready..."
kubectl rollout status deployment/"$DEPLOY_NAME" --timeout=3m

if [ $? -eq 0 ]; then
    echo "✓ Deployment ready"
    kubectl get pods -l app="$DEPLOY_NAME"
else
    echo "✗ Deployment failed to become ready"
    exit 1
fi

# Cleanup
read -p "Delete deployment? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    kubectl delete deployment "$DEPLOY_NAME"
    echo "✓ Deployment deleted"
fi
```

**Explanation**: Creates deployment with parameters, monitors rollout status, waits for pods, optionally deletes with confirmation.

---

## Solution 4: ConfigMap and Secrets Management

```bash
#!/bin/bash
# config_secrets_manager.sh - Manage ConfigMaps and Secrets

echo "=== ConfigMap and Secrets Management ==="
echo ""

# Create ConfigMap from literal
kubectl create configmap app-config \
    --from-literal=database_host=localhost \
    --from-literal=database_port=5432 \
    2>/dev/null || echo "ConfigMap already exists"

echo "✓ ConfigMap created"

# Create Secret
kubectl create secret generic app-secret \
    --from-literal=db_user=admin \
    --from-literal=db_pass=secret123 \
    2>/dev/null || echo "Secret already exists"

echo "✓ Secret created"

# List ConfigMaps
echo ""
echo "ConfigMaps:"
kubectl get configmaps

echo ""
echo "Secrets:"
kubectl get secrets --exclude-defaults

# Update ConfigMap
kubectl patch configmap app-config \
    -p '{"data":{"environment":"production"}}'

echo "✓ ConfigMap updated"

# Cleanup
read -p "Delete ConfigMap and Secret? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    kubectl delete configmap app-config
    kubectl delete secret app-secret
    echo "✓ Deleted"
fi
```

**Explanation**: Creates ConfigMap and Secret from literals, lists resources, updates ConfigMap with patch, handles deletion.

---

## Solution 5: Service Exposure and Port Forwarding

```bash
#!/bin/bash
# expose_service.sh - Expose and port-forward service

DEPLOY_NAME=${1:-test-app}
LOCAL_PORT=${2:-8080}
SVCPORT=${3:-80}

echo "Creating deployment..."
kubectl create deployment "$DEPLOY_NAME" --image=nginx 2>/dev/null

echo "Exposing service..."
kubectl expose deployment "$DEPLOY_NAME" \
    --type=ClusterIP \
    --port="$SVCPORT" \
    --target-port=80 \
    2>/dev/null

echo "✓ Service exposed"

# Wait for service
sleep 3

echo ""
echo "Setting up port forward (localhost:$LOCAL_PORT -> service:$SVCPORT)"

# Port forward in background
kubectl port-forward svc/"$DEPLOY_NAME" "$LOCAL_PORT:$SVCPORT" > /dev/null 2>&1 &
PF_PID=$!

echo "✓ Port forward started (PID: $PF_PID)"

# Test connectivity
sleep 2
echo ""
echo "Testing connectivity:"
curl -s -o /dev/null -w "HTTP Status: %{http_code}\n" http://localhost:"$LOCAL_PORT"

# Cleanup
read -p "Stop port forward? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    kill $PF_PID
    kubectl delete deployment "$DEPLOY_NAME"
    kubectl delete svc "$DEPLOY_NAME"
    echo "✓ Cleaned up"
fi
```

**Explanation**: Creates deployment, exposes service, sets up port forwarding, tests with curl, handles cleanup.

---

## Solution 6: Rolling Deployment and Rollback

```bash
#!/bin/bash
# deployment_rollback.sh - Update and rollback deployment

DEPLOY_NAME=${1:-web-app}

echo "=== Deployment Rolling Update ==="
echo ""

# Create initial deployment
echo "Creating initial deployment..."
kubectl create deployment "$DEPLOY_NAME" --image=nginx:1.16 2>/dev/null

echo "Waiting for deployment..."
kubectl rollout status deployment/"$DEPLOY_NAME" --timeout=2m

echo ""
echo "Deployment History:"
kubectl rollout history deployment/"$DEPLOY_NAME"

# Update image
echo ""
echo "Updating image to nginx:1.20..."
kubectl set image deployment/"$DEPLOY_NAME" \
    "${DEPLOY_NAME}=nginx:1.20" \
    --record

echo ""
echo "Rollout Status:"
kubectl rollout status deployment/"$DEPLOY_NAME" --timeout=2m

echo ""
echo "Deployment History After Update:"
kubectl rollout history deployment/"$DEPLOY_NAME"

# Show running version
echo ""
echo "Current Image:"
kubectl get deployment "$DEPLOY_NAME" -o jsonpath='{.spec.template.spec.containers[0].image}'
echo ""

# Rollback
read -p "Rollback to previous version? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    kubectl rollout undo deployment/"$DEPLOY_NAME"
    kubectl rollout status deployment/"$DEPLOY_NAME" --timeout=2m
    echo "✓ Rollback complete"
fi
```

**Explanation**: Creates deployment, updates image with `kubectl set image`, monitors rollout, shows history, performs rollback.

---

## Solution 7: Namespace and RBAC Setup

```bash
#!/bin/bash
# rbac_setup.sh - Create namespace and RBAC resources

NAMESPACE=${1:-secure-app}
SA_NAME="app-account"

echo "=== RBAC Setup ==="
echo ""

# Create namespace
kubectl create namespace "$NAMESPACE"
echo "✓ Namespace created: $NAMESPACE"

# Create service account
kubectl create serviceaccount "$SA_NAME" -n "$NAMESPACE"
echo "✓ Service account created: $SA_NAME"

# Create role
kubectl create role app-reader \
    --verb=get,list,watch \
    --resource=pods,services \
    -n "$NAMESPACE"
echo "✓ Role created: app-reader"

# Bind role to service account
kubectl create rolebinding app-reader-binding \
    --clusterrole=app-reader \
    --serviceaccount="$NAMESPACE:$SA_NAME" \
    -n "$NAMESPACE"
echo "✓ RoleBinding created"

# Verify
echo ""
echo "Service Accounts:"
kubectl get sa -n "$NAMESPACE"

echo ""
echo "Roles:"
kubectl get roles -n "$NAMESPACE"

echo ""
echo "RoleBindings:"
kubectl get rolebindings -n "$NAMESPACE"

# Cleanup
read -p "Delete namespace and resources? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    kubectl delete namespace "$NAMESPACE"
    echo "✓ Namespace deleted"
fi
```

**Explanation**: Creates namespace, service account, role, and rolebinding; verifies RBAC setup; handles cleanup.

---

## Solution 8: Persistent Volume and StatefulSet

```bash
#!/bin/bash
# statefulset_pv.sh - Create PV and StatefulSet

echo "=== Persistent Volume and StatefulSet ==="
echo ""

# Create PersistentVolume
cat > pv.yaml << 'EOF'
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/app
EOF

kubectl apply -f pv.yaml
echo "✓ PersistentVolume created"

# Create PersistentVolumeClaim
cat > pvc.yaml << 'EOF'
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF

kubectl apply -f pvc.yaml
echo "✓ PersistentVolumeClaim created"

# Verify PV and PVC binding
echo ""
echo "PersistentVolumes:"
kubectl get pv

echo ""
echo "PersistentVolumeClaims:"
kubectl get pvc

# Cleanup
read -p "Delete PV and PVC? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    kubectl delete -f pvc.yaml pv.yaml
    echo "✓ Deleted"
fi
```

**Explanation**: Creates PersistentVolume and PersistentVolumeClaim YAML, applies them, verifies binding, handles cleanup.

---

## Solution 9: Helm Chart Deployment

```bash
#!/bin/bash
# helm_deploy.sh - Add, install, and manage Helm charts

REPO_NAME="myrepo"
CHART_NAME="stable/nginx"

echo "=== Helm Chart Management ==="
echo ""

# Add Helm repository
echo "Adding Helm repository..."
helm repo add bitnami https://charts.bitnami.com/bitnami 2>/dev/null
helm repo update

echo "✓ Repository added"

# Search for chart
echo ""
echo "Searching for charts:"
helm search repo nginx | head -5

# Install chart
echo ""
echo "Installing Helm chart..."
helm install my-nginx bitnami/nginx \
    --set replicaCount=2 \
    2>/dev/null || echo "Release already exists"

echo "✓ Chart installed"

# List releases
echo ""
echo "Helm Releases:"
helm list

# Upgrade release
echo ""
read -p "Upgrade release? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    helm upgrade my-nginx bitnami/nginx --set replicaCount=3
    echo "✓ Release upgraded"
fi

# Cleanup
read -p "Uninstall release? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    helm uninstall my-nginx
    echo "✓ Release uninstalled"
fi
```

**Explanation**: Adds Helm repo, searches for charts, installs with parameters, lists releases, upgrades and uninstalls.

---

## Solution 10: Multi-Container Pod with Health Checks

```bash
#!/bin/bash
# multi_container_pod.sh - Create pod with multiple containers and health checks

cat > multi-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: web
    image: nginx
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "100m"
      limits:
        memory: "128Mi"
        cpu: "200m"
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 15
      periodSeconds: 20
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 10
  
  - name: sidecar
    image: busybox
    command: ['sh', '-c', 'echo "Sidecar running"; sleep 3600']
    resources:
      requests:
        memory: "32Mi"
        cpu: "50m"
      limits:
        memory: "64Mi"
        cpu: "100m"
EOF

echo "Creating multi-container pod..."
kubectl apply -f multi-pod.yaml

echo "✓ Pod created"

# Monitor pod
echo ""
echo "Pod Status:"
kubectl get pod multi-container-pod

echo ""
echo "Container Events:"
kubectl describe pod multi-container-pod | grep -E "Container|Image|Health|Event"

# Show logs
echo ""
echo "Pod Logs (web):"
kubectl logs multi-container-pod -c web || echo "No logs yet"

# Cleanup
read -p "Delete pod? (y/n): " -r
if [[ $REPLY =~ ^[Yy]$ ]]; then
    kubectl delete -f multi-pod.yaml
    echo "✓ Pod deleted"
fi
```

**Explanation**: Creates multi-container pod with resource limits, liveness/readiness probes, monitors events, shows logs.

---

**Pro Tip**: Use `kubectl explain <resource>` to see full resource specification documentation.
