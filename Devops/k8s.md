# Kubernetes (K8s) — Detailed Study Notes

---

## 1. What is Kubernetes?

Kubernetes is an open-source **container orchestration platform** that automates deploying, scaling, and managing containerized applications across a cluster of machines.

**The core problem it solves:** Running Docker containers manually works for one machine. At scale — dozens of containers, multiple servers, automatic restarts, traffic routing, rolling deployments — you need an orchestrator. That's K8s.

**What K8s gives you:**
- Automatic container scheduling across nodes
- Self-healing (restarts failed containers, replaces unhealthy pods)
- Horizontal scaling (scale up/down based on load)
- Rolling deployments and rollbacks with zero downtime
- Service discovery and load balancing
- Secret and config management

---

## 2. Architecture — Deep Dive

```
┌─────────────────────────────────────────────────┐
│                  CONTROL PLANE                  │
│                                                 │
│  ┌────────────┐  ┌──────┐  ┌────────────────┐  │
│  │ API Server │  │ etcd │  │ Controller Mgr │  │
│  └────────────┘  └──────┘  └────────────────┘  │
│         │                  ┌───────────┐        │
│         └──────────────────│ Scheduler │        │
│                            └───────────┘        │
└─────────────────────────────────────────────────┘
            │ (manages)
┌───────────┴──────────────────────────────────────┐
│                  WORKER NODES                    │
│                                                  │
│  Node 1                   Node 2                 │
│  ┌──────────────────┐     ┌──────────────────┐   │
│  │ kubelet          │     │ kubelet          │   │
│  │ kube-proxy       │     │ kube-proxy       │   │
│  │ container runtime│     │ container runtime│   │
│  │ ┌─────┐ ┌─────┐ │     │ ┌─────┐ ┌─────┐ │   │
│  │ │ Pod │ │ Pod │ │     │ │ Pod │ │ Pod │ │   │
│  │ └─────┘ └─────┘ │     │ └─────┘ └─────┘ │   │
│  └──────────────────┘     └──────────────────┘   │
└──────────────────────────────────────────────────┘
```

### Control Plane Components

| Component | Role |
|---|---|
| **API Server** | Front door for everything. All `kubectl` commands hit this. Validates and processes requests. |
| **etcd** | Distributed key-value store. Source of truth for the entire cluster state. If etcd goes down, the cluster is blind. |
| **Scheduler** | Watches for unscheduled pods and assigns them to a node based on resource availability, affinity rules, taints/tolerations. |
| **Controller Manager** | Runs controllers that ensure desired state matches actual state — ReplicaSet controller, Deployment controller, Node controller, etc. |

### Worker Node Components

| Component | Role |
|---|---|
| **kubelet** | Agent on every node. Receives pod specs from API Server and ensures the containers are running as specified. Reports node health. |
| **kube-proxy** | Manages network rules on each node. Enables Services to route traffic to the right pod. |
| **Container Runtime** | Actually runs containers — containerd (default), Docker, CRI-O. |

---

## 3. Core Objects — Full Reference

### Pod

The **smallest deployable unit** in K8s. A pod wraps one or more containers that share network (same IP) and storage.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp
    image: myapp:v1
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    env:
    - name: ENV_VAR
      value: "hello"
```

> **Note:** You rarely create bare Pods. Use a Deployment — it manages pods for you (restarts, scaling, updates).

---

### Deployment

Manages a set of identical pod replicas. Handles rolling updates and rollbacks.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp                    # Must match pod template labels
  strategy:
    type: RollingUpdate             # or Recreate
    rollingUpdate:
      maxSurge: 1                   # Max extra pods during update
      maxUnavailable: 0             # Zero downtime — no pods removed until new ones are ready
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:v2
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "128Mi"
            cpu: "250m"
          limits:
            memory: "256Mi"
            cpu: "500m"
        env:
        - name: DB_CONNECTION
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: connection-string
        livenessProbe:
          httpGet:
            path: /health
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
        readinessProbe:
          httpGet:
            path: /ready
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 3
```

---

### Rolling Update vs Recreate Strategy

| | RollingUpdate | Recreate |
|---|---|---|
| How | Gradually replaces old pods with new | Kills all old pods, then starts new ones |
| Downtime | Zero downtime | Brief downtime |
| Use case | Production APIs | Dev, or when two versions can't run simultaneously (e.g. DB schema changes) |
| Config | `maxSurge`, `maxUnavailable` | No extra config |

---

### Service

Pods have dynamic IPs that change on restart. A **Service** gives a stable DNS name and IP that routes traffic to matching pods via label selectors.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp                      # Routes to pods with this label
  ports:
  - port: 80                        # Port the service exposes
    targetPort: 80                  # Port on the pod
    protocol: TCP
  type: ClusterIP                   # See types below
```

### Service Types

| Type | Accessibility | Use Case |
|---|---|---|
| **ClusterIP** | Inside cluster only (default) | Internal microservice communication |
| **NodePort** | Via `<NodeIP>:<NodePort>` (30000–32767) | Dev/testing, not production |
| **LoadBalancer** | External IP via cloud LB (AWS ELB, Azure LB) | Production external access |
| **ExternalName** | DNS alias to an external service | Pointing to external DB or API |

---

### Ingress

Ingress manages external HTTP/HTTPS routing to internal services — like an API gateway. Needs an **Ingress Controller** installed (nginx-ingress, Traefik, etc.).

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
  tls:
  - hosts:
    - myapp.example.com
    secretName: tls-secret           # TLS cert stored as a K8s Secret
```

**Service vs Ingress:**
- Service routes traffic to pods inside the cluster.
- Ingress routes external HTTP/HTTPS traffic to services, with host/path-based rules, TLS termination, and rate limiting.

---

### ConfigMap

Store non-sensitive config — environment variables, config files — separately from container images.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
data:
  APP_ENV: "production"
  LOG_LEVEL: "info"
  config.json: |
    {
      "timeout": 30,
      "retries": 3
    }
```

**Using in a pod:**

```yaml
# As environment variables
envFrom:
- configMapRef:
    name: myapp-config

# As a mounted file
volumes:
- name: config-volume
  configMap:
    name: myapp-config
volumeMounts:
- name: config-volume
  mountPath: /app/config
```

---

### Secret

Store sensitive data — passwords, API keys, connection strings. Stored base64-encoded (NOT encrypted by default — enable encryption at rest separately).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: cGFzc3dvcmQxMjM=      # base64 encoded value
  connection-string: U2VydmVyPW15c2Vyd...
```

```bash
# Create from CLI (easier than encoding manually)
kubectl create secret generic db-secret \
  --from-literal=password=password123 \
  --from-literal=connection-string="Server=db;Database=mydb;"
```

**Using in a pod:**

```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: password
```

---

### Namespace

Logical isolation within a cluster. Useful for separating environments (dev, staging, production) or teams.

```bash
kubectl create namespace production
kubectl get pods -n production
kubectl get all -n production
kubectl config set-context --current --namespace=production  # Set default namespace
```

```yaml
# Apply resource quotas per namespace
apiVersion: v1
kind: ResourceQuota
metadata:
  name: production-quota
  namespace: production
spec:
  hard:
    pods: "20"
    requests.cpu: "4"
    requests.memory: 8Gi
    limits.cpu: "8"
    limits.memory: 16Gi
```

---

## 4. Liveness vs Readiness Probes

Critical interview topic. Both are health checks but serve different purposes.

| | Liveness Probe | Readiness Probe |
|---|---|---|
| **Purpose** | Is the container alive? | Is the container ready to serve traffic? |
| **Failure action** | Restart the container | Remove pod from Service endpoints (no traffic) |
| **Use case** | Detect deadlocks, crashes | Wait for app startup, DB connection, cache warm-up |

```yaml
containers:
- name: myapp
  image: myapp:v1
  livenessProbe:
    httpGet:
      path: /health/live        # Returns 200 if app is alive
      port: 80
    initialDelaySeconds: 15     # Wait 15s before first check
    periodSeconds: 10           # Check every 10s
    failureThreshold: 3         # Restart after 3 consecutive failures
    timeoutSeconds: 5

  readinessProbe:
    httpGet:
      path: /health/ready       # Returns 200 only when app is fully ready
      port: 80
    initialDelaySeconds: 5
    periodSeconds: 5
    failureThreshold: 3

  # Also available: TCP socket check and exec command check
  # tcpSocket:
  #   port: 80
  # exec:
  #   command: ["cat", "/tmp/healthy"]
```

**Tip:** During a rolling update, K8s waits for the readiness probe to pass before routing traffic to the new pod. This is how zero-downtime deployments work.

---

## 5. Resource Requests vs Limits

```yaml
resources:
  requests:
    memory: "128Mi"    # Guaranteed — scheduler uses this to pick a node
    cpu: "250m"        # 250 millicores = 0.25 CPU cores
  limits:
    memory: "256Mi"    # Hard cap — container killed (OOMKilled) if exceeded
    cpu: "500m"        # Soft cap — CPU throttled if exceeded (not killed)
```

| | Requests | Limits |
|---|---|---|
| **Purpose** | Minimum guaranteed resources | Maximum allowed resources |
| **Scheduler** | Uses requests to find a node with enough capacity | Not used for scheduling |
| **Exceed behavior** | N/A | CPU: throttled. Memory: OOMKilled (pod restarted). |

**Best practice:** Always set both. Without requests, the scheduler can't make intelligent decisions. Without limits, a runaway pod can starve the entire node.

**CPU units:** `1000m = 1 CPU core`. `250m = 0.25 core`.  
**Memory units:** `Mi = mebibytes`, `Gi = gibibytes`. Use `Mi`/`Gi` not `MB`/`GB`.

---

## 6. Horizontal Pod Autoscaler (HPA)

Automatically scales pod replicas based on CPU/memory usage or custom metrics.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70      # Scale up when avg CPU > 70%
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

```bash
# Create HPA via CLI
kubectl autoscale deployment myapp --cpu-percent=70 --min=2 --max=10

# Check HPA status
kubectl get hpa
kubectl describe hpa myapp-hpa
```

> **Requires:** metrics-server installed in the cluster (`kubectl top pods` also needs this).

---

## 7. PersistentVolume & PersistentVolumeClaim

Kubernetes abstracts storage into two layers:

- **PersistentVolume (PV):** The actual storage resource (provisioned by admin or dynamically).
- **PersistentVolumeClaim (PVC):** A request for storage by a user/pod. Like a ticket that gets matched to a PV.

```yaml
# PersistentVolumeClaim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: db-pvc
spec:
  accessModes:
  - ReadWriteOnce                   # One node can read/write at a time
  storageClassName: standard        # StorageClass (cloud-specific)
  resources:
    requests:
      storage: 10Gi
```

```yaml
# Use the PVC in a pod
volumes:
- name: db-storage
  persistentVolumeClaim:
    claimName: db-pvc
volumeMounts:
- name: db-storage
  mountPath: /var/lib/postgresql/data
```

**Access Modes:**

| Mode | Abbreviation | Description |
|---|---|---|
| ReadWriteOnce | RWO | One node, read + write |
| ReadOnlyMany | ROX | Many nodes, read only |
| ReadWriteMany | RWX | Many nodes, read + write (needs NFS or similar) |

---

## 8. StatefulSet

For stateful applications (databases, Kafka, Redis) where each pod needs a stable identity and persistent storage.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: postgres
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15
        volumeMounts:
        - name: data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

**Deployment vs StatefulSet:**

| | Deployment | StatefulSet |
|---|---|---|
| Pod names | Random (`myapp-7d4b9c-xkz`) | Ordered (`postgres-0`, `postgres-1`) |
| Storage | Shared or ephemeral | Each pod gets its own PVC |
| Scaling | Any order | Ordered (0 → 1 → 2) |
| Use case | Stateless apps | Databases, message brokers |

---

## 9. kubectl — Full Command Reference

### Get / Describe

```bash
kubectl get pods                            # All pods in default namespace
kubectl get pods -n production              # Specific namespace
kubectl get pods -A                         # All namespaces
kubectl get pods -o wide                    # With node and IP info
kubectl get pods -o yaml                    # Full YAML output
kubectl get all -n production               # All resources in namespace
kubectl get nodes                           # Cluster nodes
kubectl get events -n production            # Events (great for debugging)

kubectl describe pod <pod-name>             # Detailed pod info + events
kubectl describe deployment myapp           # Deployment status
kubectl describe node <node-name>           # Node capacity + conditions
```

### Apply / Delete

```bash
kubectl apply -f deployment.yaml            # Create or update resource
kubectl apply -f ./manifests/               # Apply all files in directory
kubectl delete -f deployment.yaml           # Delete by manifest
kubectl delete pod <pod-name>               # Delete specific pod (Deployment recreates it)
kubectl delete pod <pod-name> --force       # Force delete (stuck pods)
```

### Deployments & Rollouts

```bash
kubectl scale deployment myapp --replicas=5
kubectl rollout status deployment/myapp     # Watch rollout progress
kubectl rollout history deployment/myapp    # View revision history
kubectl rollout undo deployment/myapp       # Roll back to previous version
kubectl rollout undo deployment/myapp --to-revision=2  # Roll back to specific revision

# Update image (triggers rolling update)
kubectl set image deployment/myapp myapp=myapp:v2
```

### Debugging

```bash
kubectl logs <pod-name>                     # Pod logs
kubectl logs <pod-name> -f                  # Stream logs
kubectl logs <pod-name> --previous          # Logs from crashed container
kubectl logs <pod-name> -c <container>      # Multi-container pod — specific container

kubectl exec -it <pod-name> -- /bin/bash    # Shell into pod
kubectl exec -it <pod-name> -- /bin/sh      # If bash not available
kubectl exec <pod-name> -- env              # List environment variables

kubectl top pods                            # CPU/memory usage (needs metrics-server)
kubectl top nodes

kubectl port-forward pod/<pod-name> 8080:80      # Forward local port to pod
kubectl port-forward svc/myapp-service 8080:80   # Forward to service
```

### Config & Context

```bash
kubectl config get-contexts                 # List all clusters/contexts
kubectl config use-context my-cluster       # Switch cluster
kubectl config set-context --current --namespace=production  # Set default namespace

kubectl cluster-info                        # Cluster endpoint info
kubectl api-resources                       # All available resource types
```

---

## 10. AKS-Specific (Azure Kubernetes Service)

Relevant for your Azure background.

```bash
# Login and get credentials
az aks get-credentials --resource-group myRG --name myAKS

# Node pools
az aks nodepool list --resource-group myRG --cluster-name myAKS
az aks nodepool scale --resource-group myRG --cluster-name myAKS \
  --name nodepool1 --node-count 3

# Enable cluster autoscaler
az aks update --resource-group myRG --name myAKS \
  --enable-cluster-autoscaler --min-count 1 --max-count 5
```

### AKS Key Concepts

**Managed Identity:** AKS nodes can use Azure Managed Identity to access Azure resources (ACR, Key Vault, Storage) without storing credentials.

```bash
# Attach Azure Container Registry to AKS (pull images without secrets)
az aks update --resource-group myRG --name myAKS --attach-acr myACR
```

**Azure AD Integration (Entra ID):** Authenticate kubectl with your Azure AD account — no separate K8s user management.

**Node Pools:** AKS lets you have multiple node pools with different VM sizes — a system pool for K8s components, user pools for workloads, GPU pools for ML inference.

---

## 11. EKS-Specific (AWS Elastic Kubernetes Service)

**Node Groups:** Managed groups of EC2 instances. Can be On-Demand or Spot.

**IAM Roles for Service Accounts (IRSA):** The AWS way to give pods fine-grained IAM permissions without storing AWS credentials.

```yaml
# Service account with IAM role annotation
apiVersion: v1
kind: ServiceAccount
metadata:
  name: myapp-sa
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::123456789:role/myapp-role
```

```yaml
# Reference the service account in deployment
spec:
  serviceAccountName: myapp-sa
```

---

## 12. Common Interview Q&A

**Q: What's the difference between a Pod and a Deployment?**  
A Pod is a single instance of your container(s). A Deployment manages a set of Pod replicas, handles rolling updates, restarts failed pods, and maintains desired state. You almost always use Deployments, not bare Pods.

**Q: What happens when a pod crashes?**  
The kubelet detects the crash and restarts it (with exponential backoff — CrashLoopBackOff). If it's managed by a Deployment/ReplicaSet, the controller ensures the desired replica count is maintained by scheduling a new pod if needed.

**Q: How does a Service know which pods to route to?**  
Via label selectors. The Service's `selector` field matches pods that have the corresponding labels. kube-proxy maintains iptables/IPVS rules to load balance traffic across matching pods.

**Q: What's the difference between ClusterIP, NodePort, and LoadBalancer?**  
ClusterIP is internal only. NodePort exposes on a static port on every node (dev/testing). LoadBalancer provisions a cloud load balancer with an external IP (production). Ingress sits in front of services for HTTP routing with host/path rules.

**Q: What are liveness and readiness probes?**  
Liveness: is the container still alive? Failure → restart. Readiness: is the container ready to accept traffic? Failure → removed from Service endpoints, no traffic sent until it recovers.

**Q: What is the difference between requests and limits?**  
Requests are the guaranteed minimum resources; the scheduler uses them to pick a node. Limits are the hard cap; exceeding CPU causes throttling, exceeding memory causes OOMKill (pod restarts).

**Q: How do rolling updates achieve zero downtime?**  
K8s starts new pods, waits for their readiness probe to pass, then gradually removes old pods. With `maxUnavailable: 0`, it never removes an old pod until the new one is healthy. Traffic is routed only to ready pods via the Service.

**Q: What is etcd and why is it important?**  
etcd is the distributed key-value store that holds the entire cluster state — all resource definitions, secrets, configmaps. It's the source of truth. If etcd is lost without a backup, the cluster state is gone.

---

## 13. Quick Reference Card

```
# Get resources
kubectl get pods/deployments/services/nodes -n <namespace>
kubectl describe <resource> <name>
kubectl get events -n <namespace>

# Apply / Delete
kubectl apply -f file.yaml
kubectl delete -f file.yaml

# Rollout
kubectl rollout status deployment/<name>
kubectl rollout undo deployment/<name>
kubectl set image deployment/<name> <container>=<image>:<tag>

# Debug
kubectl logs <pod> -f
kubectl exec -it <pod> -- /bin/bash
kubectl port-forward svc/<name> 8080:80
kubectl top pods

# Scale
kubectl scale deployment <name> --replicas=5
kubectl autoscale deployment <name> --cpu-percent=70 --min=2 --max=10

# Namespace shortcut
kubectl config set-context --current --namespace=<ns>
```

---

*Kubernetes notes for Kushal — May 2026*