# Kubernetes Automation Exercises

## Easy Exercises (1-4)

### Exercise 1: Cluster Information and Nodes
Write a script that:
- Checks cluster connectivity
- Lists all nodes and their status
- Shows Kubernetes version
- Displays node capacity (CPU/memory)

**Hint**: Use `kubectl cluster-info`, `kubectl get nodes`, `kubectl describe node`

**Requirements**:
- Show cluster info
- List all nodes
- Display node resources
- Handle cluster unreachable gracefully

### Exercise 2: Pod Listing and Filtering
Create a script that:
- Lists all pods across all namespaces
- Filters pods by namespace
- Shows pod status (Running, Pending, Failed)
- Displays pod creation time

**Hint**: Use `kubectl get pods --all-namespaces`, `-o jsonpath`

**Requirements**:
- List from all namespaces
- Show status
- Filter by namespace parameter
- Count pods by state

### Exercise 3: Create and Delete Deployment
Write a script that:
- Creates a deployment with specified image
- Sets replicas
- Verifies deployment created
- Waits for pods running
- Deletes deployment

**Hint**: Use `kubectl create deployment`, `kubectl rollout status`

**Requirements**:
- Accept image and replica parameters
- Verify creation
- Wait for rollout
- Clean up after

### Exercise 4: ConfigMap and Secrets Management
Create a script that:
- Creates ConfigMap from file
- Creates Secret from literal values
- Lists ConfigMaps and Secrets
- Updates ConfigMap
- Deletes ConfigMap

**Hint**: Use `kubectl create configmap`, `kubectl create secret`

**Requirements**:
- Create from file/literal
- List resources
- Update existing ConfigMap
- Proper cleanup

## Medium Exercises (5-10)

### Exercise 5: Service Exposure and Port Forwarding
Build a script that:
- Creates deployment
- Exposes via service
- Sets up port forwarding
- Tests connectivity
- Cleans up resources

**Hint**: Use `kubectl expose`, `kubectl port-forward`

**Requirements**:
- Create service
- Port forward to local machine
- Test with curl/wget
- Verify connectivity

### Exercise 6: Rolling Deployment and Rollback
Create a script that:
- Creates initial deployment
- Updates image version
- Monitors rollout status
- Rolls back to previous version
- Shows deployment history

**Hint**: Use `kubectl set image`, `kubectl rollout history/undo`

**Requirements**:
- Update deployment
- Show rollout status
- Perform rollback
- Verify version change

### Exercise 7: Namespace and RBAC Setup
Write a script that:
- Creates namespace
- Creates service account
- Creates role with permissions
- Binds role to service account
- Lists RBAC resources

**Hint**: Use `kubectl create namespace`, `kubectl create role`, `kubectl create rolebinding`

**Requirements**:
- Create namespace
- Create RBAC resources
- Verify permissions
- Clean up properly

### Exercise 8: Persistent Volume and StatefulSet
Build a script that:
- Creates PersistentVolume
- Creates PersistentVolumeClaim
- Deploys StatefulSet
- Mounts volume in pod
- Verifies data persistence

**Hint**: Use `kubectl create -f`, StatefulSet YAML

**Requirements**:
- Create PV and PVC
- Deploy StatefulSet
- Verify mount
- Test persistence

### Exercise 9: Helm Chart Creation and Deployment
Create a script that:
- Adds Helm repository
- Searches for charts
- Installs Helm chart
- Lists releases
- Upgrades release
- Uninstalls chart

**Hint**: Use `helm repo add`, `helm install`, `helm list`

**Requirements**:
- Add repository
- Install chart
- Verify deployment
- Upgrade version
- Proper cleanup

### Exercise 10: Multi-Container Pod and Network Policy
Build comprehensive script that:
- Creates multi-container pod
- Sets resource limits/requests
- Applies network policy
- Implements health checks
- Monitors pod events and logs
- Tests inter-pod communication

**Hint**: Combine Pod specs, NetworkPolicy, probes

**Requirements**:
- Multiple containers
- Resource constraints
- Network policy
- Health checks (liveness, readiness)
- Logging setup
