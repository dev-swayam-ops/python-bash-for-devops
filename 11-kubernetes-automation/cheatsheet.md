# Kubernetes Automation Cheatsheet

## Cluster Management

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl cluster-info` | Display cluster info | `kubectl cluster-info` |
| `kubectl get nodes` | List all nodes | `kubectl get nodes -o wide` |
| `kubectl describe node <name>` | Node details | `kubectl describe node minikube` |
| `kubectl get ns` | List namespaces | `kubectl get namespaces` |
| `kubectl create ns <name>` | Create namespace | `kubectl create namespace production` |
| `kubectl config current-context` | Show current context | `kubectl config current-context` |
| `kubectl config use-context <name>` | Switch context | `kubectl config use-context minikube` |

## Pod Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl get pods` | List pods | `kubectl get pods -n production` |
| `kubectl get pods -A` | All namespaces | `kubectl get pods --all-namespaces` |
| `kubectl describe pod <name>` | Pod details | `kubectl describe pod nginx-xyz -n production` |
| `kubectl logs <pod>` | Show pod logs | `kubectl logs nginx-xyz -n production` |
| `kubectl logs -f <pod>` | Follow logs | `kubectl logs -f nginx-xyz` |
| `kubectl exec -it <pod> -- bash` | Shell in pod | `kubectl exec -it nginx-xyz -- bash` |
| `kubectl delete pod <name>` | Delete pod | `kubectl delete pod nginx-xyz -n production` |

## Deployment Management

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl create deployment <name> --image=<image>` | Create deployment | `kubectl create deployment nginx --image=nginx:latest` |
| `kubectl get deployments` | List deployments | `kubectl get deployments -n production` |
| `kubectl scale deployment <name> --replicas=5` | Scale replicas | `kubectl scale deployment nginx --replicas=5` |
| `kubectl set image deployment/<name> <container>=<image>` | Update image | `kubectl set image deployment/nginx nginx=nginx:1.20` |
| `kubectl rollout status deployment/<name>` | Rollout status | `kubectl rollout status deployment/nginx` |
| `kubectl rollout history deployment/<name>` | Revision history | `kubectl rollout history deployment/nginx` |
| `kubectl rollout undo deployment/<name>` | Rollback | `kubectl rollout undo deployment/nginx` |
| `kubectl delete deployment <name>` | Delete deployment | `kubectl delete deployment nginx` |

## Service Management

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl expose deployment <name> --type=LoadBalancer --port=80` | Expose service | Create external service |
| `kubectl get svc` | List services | `kubectl get services -n production` |
| `kubectl describe svc <name>` | Service details | `kubectl describe svc nginx` |
| `kubectl port-forward svc/<name> 8080:80` | Port forward | Forward local:8080 to service:80 |
| `kubectl delete svc <name>` | Delete service | `kubectl delete svc nginx` |

## ConfigMaps and Secrets

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl create configmap <name> --from-literal=key=value` | Create ConfigMap | `kubectl create configmap app-config --from-literal=env=prod` |
| `kubectl create configmap <name> --from-file=<file>` | From file | `kubectl create configmap app-config --from-file=app.conf` |
| `kubectl get configmap` | List ConfigMaps | `kubectl get configmap -n production` |
| `kubectl describe configmap <name>` | ConfigMap details | `kubectl describe configmap app-config` |
| `kubectl create secret generic <name> --from-literal=key=value` | Create Secret | `kubectl create secret generic db-creds --from-literal=password=secret` |
| `kubectl get secrets` | List Secrets | `kubectl get secrets -n production` |

## Persistent Volumes

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl get pv` | List PersistentVolumes | `kubectl get pv` |
| `kubectl get pvc` | List PersistentVolumeClaims | `kubectl get pvc -n production` |
| `kubectl describe pv <name>` | PV details | `kubectl describe pv app-pv` |
| `kubectl describe pvc <name>` | PVC details | `kubectl describe pvc app-pvc` |
| `kubectl delete pv <name>` | Delete PV | `kubectl delete pv app-pv` |
| `kubectl delete pvc <name>` | Delete PVC | `kubectl delete pvc app-pvc` |

## Helm Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `helm repo add <name> <url>` | Add repository | `helm repo add bitnami https://charts.bitnami.com/bitnami` |
| `helm repo list` | List repositories | `helm repo list` |
| `helm repo update` | Update repositories | `helm repo update` |
| `helm search repo <keyword>` | Search charts | `helm search repo nginx` |
| `helm install <release> <chart>` | Install chart | `helm install my-nginx bitnami/nginx` |
| `helm list` | List releases | `helm list -n production` |
| `helm upgrade <release> <chart>` | Upgrade release | `helm upgrade my-nginx bitnami/nginx` |
| `helm uninstall <release>` | Uninstall chart | `helm uninstall my-nginx` |

## Resource Management

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl get resources` | List all resource types | `kubectl api-resources` |
| `kubectl apply -f <file>` | Apply YAML manifest | `kubectl apply -f deployment.yaml` |
| `kubectl delete -f <file>` | Delete from YAML | `kubectl delete -f deployment.yaml` |
| `kubectl get all` | All resources | `kubectl get all -n production` |
| `kubectl top nodes` | Node resource usage | `kubectl top nodes` |
| `kubectl top pods` | Pod resource usage | `kubectl top pods -n production` |

## Label and Selector Patterns

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl label pod <name> key=value` | Add label | `kubectl label pod nginx tier=backend` |
| `kubectl get pods -l key=value` | Filter by label | `kubectl get pods -l tier=backend` |
| `kubectl get pods -l key!=value` | Exclude label | `kubectl get pods -l tier!=backend` |
| `kubectl get pods -l key in (val1,val2)` | Multiple values | Filter by multiple label values |

## Troubleshooting Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl describe pod <name>` | Event log | See why pod isn't running |
| `kubectl logs <pod>` | Application logs | Troubleshoot crashing container |
| `kubectl get events` | Cluster events | Recent cluster events |
| `kubectl debug pod <name>` | Debug pod | Ephemeral container for debugging |
| `kubectl explain <resource>` | Documentation | `kubectl explain deployment` |

## Useful Aliases

```bash
alias k='kubectl'
alias kgp='kubectl get pods'
alias kgd='kubectl get deployments'
alias kdp='kubectl describe pod'
alias kl='kubectl logs'
alias ke='kubectl exec -it'
alias kaf='kubectl apply -f'
alias kdel='kubectl delete'
```

## Common YAML Shortcuts

| Template | Purpose |
|----------|---------|
| `kubectl create deployment <name> --image=<img> --dry-run=client -o yaml` | Generate deployment YAML |
| `kubectl run <name> --image=<img> --dry-run=client -o yaml` | Generate pod YAML |
| `kubectl expose deployment <name> --port=80 --dry-run=client -o yaml` | Generate service YAML |

## Debugging Checklist

- [ ] Pod stuck pending: `kubectl describe pod`
- [ ] ImagePullBackOff: Check image name/registry
- [ ] CrashLoopBackOff: `kubectl logs` to see crash
- [ ] Service not accessible: Check service selector labels
- [ ] Node not ready: `kubectl describe node`, check kubelet
- [ ] Memory/CPU issues: `kubectl top`, check requests/limits

## Namespace Tips

```bash
# Work in namespace by default
kubectl config set-context --current --namespace=production

# Query across namespaces
kubectl get pods --all-namespaces

# Delete entire namespace (careful!)
kubectl delete namespace production
```

## Resource Limits Example

```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "100m"
  limits:
    memory: "128Mi"
    cpu: "200m"
```

## Common Mistakes

1. **Forgetting namespace**: Add `-n <namespace>` flag
2. **Wrong image**: Verify image exists in registry
3. **Port conflicts**: Check service ports
4. **Label mismatch**: Deployment selector must match pod labels
5. **Resource limits too low**: Pod evicted or CPU throttled

---

**Pro Tip**: Use `--dry-run=client -o yaml` before applying changes to see what will be created.
