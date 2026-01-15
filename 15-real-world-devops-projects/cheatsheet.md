# Real-World DevOps Projects - Cheatsheet

## Deployment Patterns

| Pattern | Use Case | Command |
|---------|----------|---------|
| Blue-Green | Zero-downtime updates | `kubectl patch svc app -p '{"spec":{"selector":{"version":"blue"}}}'` |
| Canary | Gradual rollout | `istioctl virtualservice patch app --weight 5` |
| Rolling Update | Sequential pod replacement | `kubectl set image deployment/app app=new:v1.1` |
| Recreate | Stop old, start new (downtime) | `strategy: Recreate` in deployment |
| Shadow | Test without affecting users | Deploy to separate traffic mirror |

## Environment Management

| Task | Command | Example |
|------|---------|---------|
| List environments | `kubectl get namespaces` | Show all environments |
| Create namespace | `kubectl create ns staging` | New environment |
| Set active env | `export KUBECONFIG=~/.kube/staging` | Switch context |
| Get env status | `kubectl cluster-info` | Verify connectivity |
| Describe namespace | `kubectl describe ns prod` | Show quotas/limits |
| Get resource quotas | `kubectl describe resourcequota -n prod` | Memory/CPU limits |

## Deployment Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl apply -f` | Deploy from manifest | `kubectl apply -f deployment.yaml` |
| `kubectl rollout status` | Wait for completion | `kubectl rollout status deployment/app` |
| `kubectl rollout undo` | Rollback to previous | `kubectl rollout undo deployment/app` |
| `kubectl set image` | Update image version | `kubectl set image deployment/app app=new:v1.1` |
| `kubectl scale deployment` | Change replicas | `kubectl scale deployment/app --replicas=5` |
| `kubectl patch` | Update specific field | `kubectl patch deployment app -p '{"spec":{"replicas":3}}'` |
| `kubectl describe deployment` | Get deployment details | `kubectl describe deployment app` |

## Service and Load Balancing

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl expose deployment` | Create service | `kubectl expose deployment app --type=LoadBalancer` |
| `kubectl get svc` | List services | Show endpoints and IPs |
| `kubectl port-forward` | Local port forward | `kubectl port-forward svc/app 8080:8080` |
| `kubectl get endpoints` | Show backend pods | List pod IPs for service |
| `istioctl virtualservice` | Traffic management | Route based on headers/weight |
| `istioctl destinationrule` | Load balancing | Connection pooling, outlier detection |

## Monitoring and Logging

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl logs -f` | Stream pod logs | `kubectl logs -f deployment/app` |
| `kubectl logs --tail=100` | Recent logs | Last 100 lines |
| `kubectl logs -p` | Previous container logs | Crashed container output |
| `kubectl exec -it` | Run command in pod | `kubectl exec -it pod/app -- /bin/bash` |
| `kubectl top nodes` | Node resource usage | Memory and CPU per node |
| `kubectl top pods` | Pod resource usage | Usage breakdown |
| `kubectl describe pod` | Pod detailed info | Events, resource requests |

## Health Checks and Probes

| Probe Type | Purpose | Config |
|-----------|---------|--------|
| Liveness | Restart dead pod | `livenessProbe.httpGet.path=/health` |
| Readiness | Remove from service | `readinessProbe.tcpSocket.port=8080` |
| Startup | Wait for ready | `startupProbe.exec.command=/check.sh` |
| Termination | Graceful shutdown | `terminationGracePeriodSeconds: 30` |

## ConfigMaps and Secrets

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl create configmap` | Create config | `kubectl create configmap app-config --from-file=config/` |
| `kubectl create secret` | Create secret | `kubectl create secret generic db-creds --from-literal=pass=secret` |
| `kubectl set env` | Update env vars | `kubectl set env deployment/app NEW_VAR=value` |
| `kubectl rollout restart` | Restart for config pickup | `kubectl rollout restart deployment/app` |

## Networking and Ingress

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl apply -f ingress.yaml` | Create ingress route | HTTP route to service |
| `kubectl get ingress` | List ingress rules | Show external IPs |
| `kubectl describe ingress` | Ingress details | Path routing rules |
| `kubectl get networkpolicies` | List network policies | Traffic restrictions |
| `kubectl apply -f netpol.yaml` | Create network policy | Pod-to-pod restrictions |

## RBAC (Role-Based Access Control)

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl create serviceaccount` | Create account | `kubectl create sa app-user` |
| `kubectl create role` | Create permission set | `kubectl create role app-role --verb=get,list --resource=pods` |
| `kubectl create rolebinding` | Bind role to account | `kubectl create rolebinding app-binding --role=app-role --serviceaccount=default:app-user` |
| `kubectl auth can-i` | Check permissions | `kubectl auth can-i get pods --as=system:serviceaccount:default:app-user` |

## StatefulSets and DaemonSets

| Command | Purpose | Example |
|---------|---------|---------|
| `kubectl apply -f statefulset.yaml` | Deploy stateful app | Database, cache clusters |
| `kubectl delete sts app --cascade=orphan` | Keep pods when deleting | Preserve data |
| `kubectl apply -f daemonset.yaml` | Deploy on all nodes | Monitoring agents, loggers |
| `kubectl get pvc` | Persistent volumes | Data storage |

## Helm Deployment

| Command | Purpose | Example |
|---------|---------|---------|
| `helm repo add` | Add chart repository | `helm repo add bitnami https://charts.bitnami.com/bitnami` |
| `helm search repo` | Find charts | `helm search repo mysql` |
| `helm install` | Deploy chart | `helm install mysql bitnami/mysql` |
| `helm upgrade` | Update release | `helm upgrade mysql bitnami/mysql --set auth.password=newpass` |
| `helm rollback` | Revert release | `helm rollback mysql 1` |
| `helm values` | Show chart values | `helm show values bitnami/mysql` |

## Disaster Recovery

| Task | Command | Purpose |
|------|---------|---------|
| Backup etcd | `ETCDCTL_API=3 etcdctl snapshot save backup.db` | Cluster state backup |
| Restore etcd | `etcdctl snapshot restore backup.db` | Restore cluster |
| Backup volume | `kubectl exec pod -- tar czf - /data` | Extract volume data |
| Export manifest | `kubectl get all -o yaml > backup.yaml` | Save configurations |
| Recreate from backup | `kubectl apply -f backup.yaml` | Restore from manifest |

## Troubleshooting

| Issue | Command | Solution |
|-------|---------|----------|
| Pod pending | `kubectl describe pod POD` | Check events for why |
| CrashLoopBackOff | `kubectl logs POD` | Review error messages |
| ImagePullBackOff | `kubectl describe pod` | Check registry, image name |
| Stuck deletion | `kubectl patch pod POD -p '{"metadata":{"finalizers":null}}'` | Force delete |
| No resources | `kubectl top nodes` | Check node capacity |
| Slow response | `kubectl exec POD -- top` | Check process usage |

## Quick Diagnostics

```bash
# Full cluster status
kubectl get nodes,po,svc,ing -A

# Troubleshoot pod
kubectl describe pod POD -n NS && kubectl logs POD -n NS

# Check resource usage
kubectl top nodes && kubectl top pods -A

# Recent events
kubectl get events -A --sort-by='.lastTimestamp'

# Network connectivity
kubectl exec POD -- wget -O- http://SERVICE:PORT

# Persistent volumes
kubectl get pv,pvc -A

# Resource quotas
kubectl describe resourcequota -A
```

## Common Issues and Fixes

| Problem | Cause | Fix |
|---------|-------|-----|
| Deployment not progressing | Insufficient resources | `kubectl describe node` and add capacity |
| Service not accessible | No endpoints | Check pod readiness and selectors |
| Pod can't reach external | Network policy blocking | Add egress rule to netpol |
| ConfigMap not updating | No pod restart | `kubectl rollout restart deployment/app` |
| High memory usage | Memory leak | Check container logs for leaks |
| Slow deployments | Image pull timeout | Use local registry, pre-pull images |
