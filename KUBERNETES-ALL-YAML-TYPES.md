# Complete Kubernetes YAML Reference - All Types

This covers EVERY Kubernetes YAML file type you'll see in interviews.
Master this, and you won't get stuck on "I don't know this YAML" 💯

---

## Overview: All Kubernetes YAML Types

There are 18 main YAML resource types. Here they are categorized:

### Core Resources (Basic, MUST know)
1. Deployment
2. Service
3. ConfigMap
4. Secret
5. Ingress

### Storage Resources (Data persistence)
6. PersistentVolume (PV)
7. PersistentVolumeClaim (PVC)
8. StorageClass

### Workload Resources (Running applications)
9. StatefulSet
10. DaemonSet
11. Job
12. CronJob

### Network Resources (Traffic & communication)
13. NetworkPolicy

### Security & Access
14. ServiceAccount
15. Role
16. RoleBinding

### Cluster Resources
17. Namespace
18. ResourceQuota

### Auto-scaling
19. HorizontalPodAutoscaler (HPA)
20. PodDisruptionBudget (PDB)

---

## ALREADY COVERED (See KUBERNETES-YAML-GUIDE.md)

1. ✅ Deployment
2. ✅ Service
3. ✅ ConfigMap
4. ✅ Secret
5. ✅ Ingress

This file covers the remaining 15 types.

---

## STORAGE RESOURCES

### 6) PersistentVolume (PV)

**What it is:**
Physical storage provisioned by admin. Independent of pods.

**When to use:**
When you need persistent data that survives pod restart/deletion.

**Real World Example:**
Database needs to store data permanently on disk.

**File name:** `pv.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: todo-db-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: fast-ssd
  hostPath:
    path: "/mnt/data"
```

**What each part means:**

- `capacity.storage: 10Gi` - Size of volume
- `accessModes: ReadWriteOnce` - Only one pod can write at a time
- `storageClassName: fast-ssd` - Which storage to use
- `hostPath.path` - Physical location on node

**Access Modes:**
- `ReadWriteOnce` (RWO) - Single pod, read+write
- `ReadOnlyMany` (ROX) - Multiple pods, read only
- `ReadWriteMany` (RWX) - Multiple pods, read+write

**Types of storage:**

```yaml
# Option 1: Local node storage
hostPath:
  path: "/mnt/data"

# Option 2: Cloud storage (AWS)
awsElasticBlockStore:
  volumeID: "vol-123456"
  fsType: ext4

# Option 3: Cloud storage (Azure)
azureDisk:
  diskName: "myDisk"
  diskURI: "https://..."

# Option 4: NFS (network storage)
nfs:
  server: "nfs-server.example.com"
  path: "/exported/path"

# Option 5: iSCSI
iscsi:
  targetPortal: "10.0.0.1:3260"
  iqn: "iqn.2016-04.example.com:storage.disk1.sys1.xyz"
```

**Common use cases:**
- Database storage
- File uploads
- Cache data

---

### 7) PersistentVolumeClaim (PVC)

**What it is:**
Request for storage by a pod. Pod "claims" a PV using PVC.

**When to use:**
Every time a pod needs persistent storage.

**File name:** `pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: todo-db-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: fast-ssd
  resources:
    requests:
      storage: 5Gi
```

**How PV and PVC work together:**

```
PersistentVolume (10Gi total)
        ↓
PersistentVolumeClaim requests 5Gi
        ↓
Kubernetes binds them together
        ↓
Pod uses the PVC to access storage
```

**Using PVC in Deployment:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-database
spec:
  replicas: 1
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
        image: postgres:13
        volumeMounts:
        - name: db-storage
          mountPath: /var/lib/postgresql/data
      volumes:
      - name: db-storage
        persistentVolumeClaim:
          claimName: todo-db-pvc
```

---

### 8) StorageClass

**What it is:**
Defines HOW storage is provisioned (automatically creates PV when PVC requests it).

**When to use:**
To enable automatic storage provisioning.

**File name:** `storageclass.yaml`

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/aws-ebs
parameters:
  type: io1
  iops: "300"
  fstype: ext4
allowVolumeExpansion: true
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
```

**What each part means:**

- `provisioner` - Which storage provider (AWS, Azure, GCP, NFS, etc.)
- `parameters` - Storage type and settings
- `allowVolumeExpansion` - Can grow storage size
- `reclaimPolicy` - What happens when PVC deleted (Delete, Retain, Recycle)
- `volumeBindingMode` - When to bind volume (Immediate or WaitForFirstConsumer)

**ReclaimPolicy options:**

```yaml
reclaimPolicy: Delete      # Delete PV when PVC deleted (default)
reclaimPolicy: Retain      # Keep PV even after PVC deleted
reclaimPolicy: Recycle     # Clear data and reuse volume
```

**Common provisioners:**

```yaml
# AWS EBS
provisioner: kubernetes.io/aws-ebs

# Azure Disk
provisioner: kubernetes.io/azure-disk

# Google Compute Engine
provisioner: kubernetes.io/gce-pd

# NFS
provisioner: example.com/nfs

# Local storage
provisioner: kubernetes.io/local

# Managed storage class (cloud provider default)
provisioner: ebs.csi.aws.com
```

**Workflow with StorageClass:**

```
1. Define StorageClass
2. PVC references StorageClass
3. Kubernetes sees PVC
4. Automatically creates PV
5. Pod uses PVC
6. Data persists
```

---

## WORKLOAD RESOURCES

### 9) StatefulSet

**What it is:**
Like Deployment, but for apps that need stable identity and ordered startup.

**When to use:**
For stateful applications:
- Databases (PostgreSQL, MySQL, MongoDB)
- Message queues (RabbitMQ, Kafka)
- Search engines (Elasticsearch)
- Any app that needs stable hostname

**File name:** `statefulset.yaml`

**Key difference from Deployment:**

```
Deployment:
- Pods are interchangeable (pod-123, pod-456)
- Can be created/deleted in any order
- For stateless apps (web servers, APIs)

StatefulSet:
- Pods have stable names (db-0, db-1, db-2)
- Created/deleted in order
- Each pod has persistent identity
- For stateful apps (databases, caches)
```

**Complete StatefulSet Example:**

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres-statefulset
spec:
  serviceName: postgres-headless
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
        image: postgres:13
        ports:
        - containerPort: 5432
          name: postgres
        env:
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        volumeMounts:
        - name: data
          mountPath: /var/lib/postgresql
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 10Gi
```

**What makes it special:**

- `serviceName: postgres-headless` - Headless service (no load balancing)
- `volumeClaimTemplates` - Each pod gets its own PVC automatically
- Pods named: postgres-0, postgres-1, postgres-2 (ordinal naming)
- Pods start in order: 0 → 1 → 2 → (2 → 1 → 0 when scaling down)

**Headless Service for StatefulSet:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres-headless
spec:
  clusterIP: None
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432
```

---

### 10) DaemonSet

**What it is:**
Ensures one pod runs on EVERY node in the cluster.

**When to use:**
For cluster-wide services:
- Log collectors (Fluentd, Logstash)
- Monitoring agents (Prometheus, Datadog)
- Network utilities (CNI plugins)
- Node management (node-exporter)

**File name:** `daemonset.yaml`

**Example:**

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-collector
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd:latest
        volumeMounts:
        - name: var-log
          mountPath: /var/log
      volumes:
      - name: var-log
        hostPath:
          path: /var/log
```

**Key points:**

- No `replicas` field (one per node)
- `tolerations` - Run on master nodes too
- Access host filesystem via `hostPath`
- Useful for monitoring and logging

---

### 11) Job

**What it is:**
Runs a task to completion (then stops).

**When to use:**
For one-off tasks:
- Data migration
- Batch processing
- Database backups
- Report generation
- File processing

**File name:** `job.yaml`

**Example:**

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: database-migration
spec:
  template:
    spec:
      containers:
      - name: migration
        image: myregistry/db-migration:1.0
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url
      restartPolicy: Never
  backoffLimit: 3
  ttlSecondsAfterFinished: 3600
```

**What each means:**

- `backoffLimit: 3` - Retry up to 3 times if fails
- `restartPolicy: Never` - Don't restart on failure (Job will retry)
- `ttlSecondsAfterFinished: 3600` - Delete job 1 hour after completion

**Job states:**

```
Running → Succeeded (exit 0) → Completed
       → Failed (exit 1) → Check logs, retry
       → Timeout → backoffLimit exceeded
```

**Check Job status:**

```bash
kubectl get jobs
kubectl describe job database-migration
kubectl logs job/database-migration
```

---

### 12) CronJob

**What it is:**
Runs a Job on a schedule (like cron on Linux).

**When to use:**
For scheduled tasks:
- Daily backups
- Hourly reports
- Periodic cleanup
- Nightly data sync

**File name:** `cronjob.yaml`

**Example:**

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-backup
spec:
  schedule: "0 2 * * *"  # 2 AM every day
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: myregistry/backup-tool:1.0
            env:
            - name: BACKUP_PATH
              value: "/backups"
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: url
          restartPolicy: OnFailure
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 1
```

**Schedule format (cron):**

```
"0 2 * * *"      # 2 AM every day
"0 */4 * * *"    # Every 4 hours
"30 3 * * 0"     # 3:30 AM every Sunday
"0 0 1 * *"      # 1 AM on 1st of month

Format: minute hour day month day-of-week
```

**Common schedules:**

```yaml
schedule: "0 0 * * *"      # Daily at midnight
schedule: "0 * * * *"      # Hourly
schedule: "*/30 * * * *"   # Every 30 minutes
schedule: "0 0 * * 0"      # Weekly on Sunday
schedule: "0 0 1 * *"      # Monthly on 1st
```

---

## NETWORK RESOURCES

### 13) NetworkPolicy

**What it is:**
Firewall rules for pod-to-pod communication.

**When to use:**
To restrict network traffic between pods.

**File name:** `networkpolicy.yaml`

**Example: Deny all ingress by default**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

**Example: Allow only specific pods**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 3000
```

**Example: Allow external traffic on specific port**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-external-api
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: ingress-nginx
    ports:
    - protocol: TCP
      port: 8080
```

---

## SECURITY & ACCESS

### 14) ServiceAccount

**What it is:**
Identity for pods. Used for authentication and authorization.

**When to use:**
Every pod needs a ServiceAccount (default one is used if not specified).

**File name:** `serviceaccount.yaml`

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: todo-app-sa
  namespace: default
```

**Using ServiceAccount in Deployment:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-app
spec:
  template:
    spec:
      serviceAccountName: todo-app-sa
      containers:
      - name: app
        image: myapp:latest
```

---

### 15) Role

**What it is:**
Set of permissions for doing things in Kubernetes.

**When to use:**
Define what a ServiceAccount can do.

**File name:** `role.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
spec:
  rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["configmaps"]
    verbs: ["get"]
```

**Common verbs:**

```yaml
verbs: ["get"]           # Read one resource
verbs: ["list"]          # List resources
verbs: ["watch"]         # Watch for changes
verbs: ["create"]        # Create new resource
verbs: ["update"]        # Update resource
verbs: ["delete"]        # Delete resource
verbs: ["patch"]         # Partial update
verbs: ["*"]             # All permissions
```

---

### 16) RoleBinding

**What it is:**
Links a Role to a ServiceAccount.

**When to use:**
To grant permissions to a ServiceAccount.

**File name:** `rolebinding.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
spec:
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: pod-reader
  subjects:
  - kind: ServiceAccount
    name: todo-app-sa
    namespace: default
```

**RBAC Flow:**

```
ServiceAccount (identity)
        ↓
RoleBinding (grants permissions)
        ↓
Role (defines permissions)
        ↓
Pod can do specific actions
```

---

## CLUSTER RESOURCES

### 17) Namespace

**What it is:**
Virtual cluster partition. Isolates resources.

**When to use:**
Separate apps, teams, or environments.

**File name:** `namespace.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: todo-app
  labels:
    environment: production
```

**Using Namespace:**

```bash
# Deploy to specific namespace
kubectl apply -f deployment.yaml --namespace=todo-app

# Or specify in YAML
metadata:
  namespace: todo-app
```

**Common namespaces:**

```
default         - Default namespace
kube-system     - Kubernetes system
kube-public     - Public resources
kube-node-lease - Node heartbeats
```

---

### 18) ResourceQuota

**What it is:**
Limits total resource usage in a namespace.

**When to use:**
Prevent one team/app from using all resources.

**File name:** `resourcequota.yaml`

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: todo-quota
  namespace: todo-app
spec:
  hard:
    requests.cpu: "10"
    requests.memory: "20Gi"
    limits.cpu: "20"
    limits.memory: "40Gi"
    pods: "100"
    services: "10"
    configmaps: "20"
    secrets: "20"
```

**What it limits:**

```yaml
requests.cpu: "10"       # Total CPU requests
requests.memory: "20Gi"  # Total memory requests
limits.cpu: "20"         # Total CPU limits
limits.memory: "40Gi"    # Total memory limits
pods: "100"              # Max pods in namespace
services: "10"           # Max services
configmaps: "20"         # Max configmaps
persistentvolumeclaims: "5"  # Max PVCs
```

---

## AUTO-SCALING RESOURCES

### 19) HorizontalPodAutoscaler (HPA)

**What it is:**
Automatically scales number of pods based on metrics (CPU, memory).

**When to use:**
When traffic varies and you want automatic scaling.

**File name:** `hpa.yaml`

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: todo-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: todo-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 50
        periodSeconds: 15
```

**What it does:**

```
Average CPU usage > 70%?
    → Scale UP (add pods)

Average CPU usage < 70%?
    → Scale DOWN (remove pods)

Keep between 2-10 replicas
```

**Common scaling metrics:**

```yaml
# CPU-based
metrics:
- type: Resource
  resource:
    name: cpu
    target:
      averageUtilization: 70

# Memory-based
metrics:
- type: Resource
  resource:
    name: memory
    target:
      averageUtilization: 80

# Custom metrics (app-specific)
metrics:
- type: Pods
  pods:
    metric:
      name: requests_per_second
    target:
      averageValue: "1000"
```

---

### 20) PodDisruptionBudget (PDB)

**What it is:**
Ensures minimum pods are always available during disruptions.

**When to use:**
Ensure high availability during maintenance, node failures.

**File name:** `pdb.yaml`

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: todo-app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: todo-app
```

**What it means:**

```yaml
minAvailable: 2
# At least 2 pods must always be running
# Even during cluster maintenance

# If you have 3 replicas:
# - Can disrupt 1 pod maximum
# - Must keep 2 running

maxUnavailable: 1
# Alternative: maximum 1 pod can be disrupted
```

---

## COMPLETE TODO APP WITH ALL YAMLS

Here's a production-ready Todo App with multiple YAML files:

### Step 1: Create Namespace

```yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: todo-production
```

### Step 2: Create ResourceQuota

```yaml
# resourcequota.yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: todo-quota
  namespace: todo-production
spec:
  hard:
    requests.cpu: "10"
    requests.memory: "20Gi"
    pods: "50"
```

### Step 3: Create ServiceAccount

```yaml
# serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: todo-app-sa
  namespace: todo-production
```

### Step 4: Create StorageClass

```yaml
# storageclass.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/aws-ebs
parameters:
  type: io1
```

### Step 5: Create ConfigMap

```yaml
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: todo-config
  namespace: todo-production
data:
  DB_HOST: "postgres-statefulset-0.postgres-headless.todo-production.svc.cluster.local"
  DB_PORT: "5432"
  DB_NAME: "tododb"
  REDIS_HOST: "redis"
```

### Step 6: Create Secrets

```yaml
# secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
  namespace: todo-production
type: Opaque
data:
  username: dG9kb191c2Vy
  password: c3RyMG5nUGFzc3dvcmQxMjM=
```

### Step 7: Create PersistentVolumeClaim

```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-pvc
  namespace: todo-production
spec:
  accessModes: ["ReadWriteOnce"]
  storageClassName: fast-ssd
  resources:
    requests:
      storage: 20Gi
```

### Step 8: Create StatefulSet for Database

```yaml
# postgres-statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres-statefulset
  namespace: todo-production
spec:
  serviceName: postgres-headless
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      serviceAccountName: todo-app-sa
      containers:
      - name: postgres
        image: postgres:13
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_DB
          valueFrom:
            configMapKeyRef:
              name: todo-config
              key: DB_NAME
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: password
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 20Gi
---
apiVersion: v1
kind: Service
metadata:
  name: postgres-headless
  namespace: todo-production
spec:
  clusterIP: None
  selector:
    app: postgres
  ports:
  - port: 5432
```

### Step 9: Create DaemonSet for Logging

```yaml
# logging-daemonset.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-collector
  namespace: todo-production
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd:latest
        volumeMounts:
        - name: var-log
          mountPath: /var/log
      volumes:
      - name: var-log
        hostPath:
          path: /var/log
```

### Step 10: Create Backend Deployment

```yaml
# backend-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-backend
  namespace: todo-production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: todo-backend
  template:
    metadata:
      labels:
        app: todo-backend
    spec:
      serviceAccountName: todo-app-sa
      containers:
      - name: backend
        image: myregistry/todo-backend:1.0
        ports:
        - containerPort: 3000
        env:
        - name: DB_HOST
          valueFrom:
            configMapKeyRef:
              name: todo-config
              key: DB_HOST
        - name: DB_USER
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: username
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-credentials
              key: password
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 10
---
apiVersion: v1
kind: Service
metadata:
  name: todo-backend-service
  namespace: todo-production
spec:
  type: ClusterIP
  selector:
    app: todo-backend
  ports:
  - port: 3000
    targetPort: 3000
```

### Step 11: Create HPA for Backend

```yaml
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: todo-backend-hpa
  namespace: todo-production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: todo-backend
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        averageUtilization: 70
```

### Step 12: Create PDB

```yaml
# pdb.yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: todo-app-pdb
  namespace: todo-production
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: todo-backend
```

### Step 13: Create NetworkPolicy

```yaml
# networkpolicy.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-default
  namespace: todo-production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-backend-to-db
  namespace: todo-production
spec:
  podSelector:
    matchLabels:
      app: postgres
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: todo-backend
    ports:
    - protocol: TCP
      port: 5432
```

### Step 14: Create Frontend Deployment

```yaml
# frontend-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-frontend
  namespace: todo-production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: todo-frontend
  template:
    metadata:
      labels:
        app: todo-frontend
    spec:
      containers:
      - name: frontend
        image: myregistry/todo-frontend:1.0
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_API_URL
          value: "http://todo-backend-service:3000"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
---
apiVersion: v1
kind: Service
metadata:
  name: todo-frontend-service
  namespace: todo-production
spec:
  type: LoadBalancer
  selector:
    app: todo-frontend
  ports:
  - port: 80
    targetPort: 3000
```

### Step 15: Create Ingress

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: todo-ingress
  namespace: todo-production
spec:
  ingressClassName: nginx
  rules:
  - host: todoapp.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: todo-backend-service
            port:
              number: 3000
      - path: /
        pathType: Prefix
        backend:
          service:
            name: todo-frontend-service
            port:
              number: 80
```

### Step 16: Create CronJob for Backup

```yaml
# backup-cronjob.yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-db-backup
  namespace: todo-production
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          serviceAccountName: todo-app-sa
          containers:
          - name: backup
            image: myregistry/backup-tool:1.0
            env:
            - name: DB_HOST
              valueFrom:
                configMapKeyRef:
                  name: todo-config
                  key: DB_HOST
            - name: DB_USER
              valueFrom:
                secretKeyRef:
                  name: db-credentials
                  key: username
          restartPolicy: OnFailure
```

### Deployment Order

```bash
# 1. Namespace
kubectl apply -f namespace.yaml

# 2. Storage
kubectl apply -f storageclass.yaml
kubectl apply -f pvc.yaml

# 3. Config
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml

# 4. RBAC
kubectl apply -f serviceaccount.yaml

# 5. Limits
kubectl apply -f resourcequota.yaml

# 6. Data
kubectl apply -f postgres-statefulset.yaml

# 7. App
kubectl apply -f backend-deployment.yaml
kubectl apply -f frontend-deployment.yaml

# 8. Networking
kubectl apply -f networkpolicy.yaml
kubectl apply -f ingress.yaml

# 9. Scaling
kubectl apply -f hpa.yaml
kubectl apply -f pdb.yaml

# 10. Monitoring
kubectl apply -f logging-daemonset.yaml

# 11. Jobs
kubectl apply -f backup-cronjob.yaml
```

---

## INTERVIEW ANSWER: All YAML Types

When asked: "What are all the Kubernetes YAML resource types?"

**Say this confidently:**

"There are 20 main Kubernetes resources, grouped by category:

**Core (5):**
1. Deployment - Run stateless apps
2. Service - Expose network access
3. ConfigMap - Non-secret config
4. Secret - Sensitive data
5. Ingress - Route external traffic

**Storage (3):**
6. PersistentVolume - Physical storage
7. PersistentVolumeClaim - Storage request
8. StorageClass - Automated storage provisioning

**Workload (4):**
9. StatefulSet - Stateful apps (databases)
10. DaemonSet - One pod per node (logging)
11. Job - Run to completion
12. CronJob - Scheduled jobs

**Network & Security (5):**
13. NetworkPolicy - Firewall rules
14. ServiceAccount - Pod identity
15. Role - Permissions
16. RoleBinding - Grant permissions
17. Namespace - Resource isolation

**Cluster (1):**
18. ResourceQuota - Resource limits

**Auto-scaling (2):**
19. HorizontalPodAutoscaler - Auto-scale pods
20. PodDisruptionBudget - High availability

I can explain each one, when to use it, and provide YAML examples."

This will definitely impress the interviewer. 💯

---

## QUICK REFERENCE TABLE

| Resource | Purpose | Use Case |
|----------|---------|----------|
| Deployment | Run containers | Web apps, APIs |
| Service | Expose network | Load balancing |
| ConfigMap | Non-secret config | Database host |
| Secret | Secret data | Passwords |
| Ingress | Route traffic | Domain routing |
| PV | Physical storage | Data persistence |
| PVC | Storage request | Apps needing storage |
| StorageClass | Auto provisioning | On-demand storage |
| StatefulSet | Stateful apps | Databases, queues |
| DaemonSet | One per node | Logging, monitoring |
| Job | Run once | Batch jobs |
| CronJob | Scheduled | Backups |
| NetworkPolicy | Firewall | Secure communication |
| ServiceAccount | Pod identity | Permissions |
| Role | Permissions | RBAC |
| RoleBinding | Grant access | RBAC |
| Namespace | Isolation | Multi-tenant |
| ResourceQuota | Limits | Prevent overuse |
| HPA | Auto-scale | Load balancing |
| PDB | High availability | Maintenance safety |

---

## Final Checklist Before Interview

- [ ] I can explain all 20 Kubernetes resources
- [ ] I know when to use each one
- [ ] I can write basic YAML for each
- [ ] I understand how they work together
- [ ] I can deploy a complete application
- [ ] I know common errors and fixes
- [ ] I can troubleshoot deployment issues
- [ ] I understand RBAC and networking
- [ ] I know about storage and persistence
- [ ] I understand scaling and auto-scaling

If all checked - you are ready for any Kubernetes interview! 🚀
