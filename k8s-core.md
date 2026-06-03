# Pods, ReplicaSets and Deployments

# POD

A Pod is the smallest deployable unit in Kubernetes.

Think of it as a wrapper around one or more containers.

```text
┌─────────────────────────────┐
│           POD               │
│  ┌─────────┐  ┌─────────┐   │
│  │Container│  │Container│   │
│  └─────────┘  └─────────┘   │
│                             │
│  - Shared Network (IP)      │
│  - Shared Storage           │
│  - On Same Node             │
└─────────────────────────────┘
```

---

## Pod Characteristics

All containers inside a Pod share:

- Same Network IP
- Same Storage Volumes
- Same Node

---

## How Many Containers Should a Pod Have?

### 99% Cases

One Pod = One Container

```text
Pod
 └── Application Container
```

---

### 1% Cases

One Pod = Multiple Containers

Used when containers need to work closely together.

---

# Multi-Container Pod Patterns

## Pattern 1: Sidecar Container

A Sidecar Container runs alongside the main application container throughout the Pod lifecycle.

### Example

```text
Main Container = Node.js Application

Sidecar Container = Fluentd
```

Flow:

```text
Node.js App
     │
     ▼
Writes Logs
     │
     ▼
Log Files
     │
     ▼
Fluentd Sidecar
     │
     ▼
Elasticsearch
     │
     ▼
Dashboard
```

### Real World Example

```text
Main Container = Node.js App
                 (logs likh raha hai files mein)

Sidecar Container = Fluentd
                    (woh files read karta hai
                     aur Elasticsearch ko bhejta hai)

Dono saath chalte hain —

App logs likhti hai →
Fluentd collect karta hai →
Dashboard mein dikhta hai ✅
```

---

## Pattern 2: Init Container

An Init Container performs initialization tasks before the main application starts.

Examples:

- Waiting for Database
- Downloading Configurations
- Running Migrations
- Creating Directories

### Execution Flow

```text
Init Container
      ↓
Task Complete
      ↓
Exit Successfully
      ↓
Main Application Starts
```

Important:

- Runs only once
- Must complete successfully
- Main container won't start until it finishes

---

# REPLICASET

ReplicaSet ensures that the current state matches the desired state.

Example:

Desired Pods = 3

If one Pod dies, ReplicaSet automatically creates a new Pod.

---

## How ReplicaSet Identifies Pods?

ReplicaSet uses:

- Labels
- Selectors

### Pod

```yaml
labels:
  app: nginx
```

### ReplicaSet

```yaml
selector:
  matchLabels:
    app: nginx
```

If labels match, ReplicaSet knows:

```text
"These Pods belong to me."
```

---

# Self-Healing in ReplicaSet

ReplicaSet continuously watches Pods.

### Normal State

```text
[Pod1] [Pod2] [Pod3] ✅
```

### Pod Crash Scenario

```text
[DEAD] [Pod2] [Pod3]
```

ReplicaSet detects mismatch.

```text
ReplicaSet:
"Sirf 2 hain! 3 chahiye!"
```

Creates new Pod automatically.

```text
[Pod4] [Pod2] [Pod3] ✅
```

---

## Node Failure Scenario

```text
Node-1
[Pod1] [Pod2] [Pod3]

Node-1 Dies ❌
```

ReplicaSet detects missing Pods.

```text
ReplicaSet
      ↓
Creates New Pods
      ↓
Schedules Them
      ↓
Healthy Nodes
```

Result:

```text
3 New Pods Running ✅
```

---

# DEPLOYMENT

Deployment is a higher-level Kubernetes object that manages:

- ReplicaSets
- Rolling Updates
- Rollbacks

In production environments, Deployments are always preferred.

---

# Why Deployment?

Without Deployment:

```text
Pod
↓
ReplicaSet
```

With Deployment:

```text
Deployment
     ↓
ReplicaSet
     ↓
Pods
```

Deployment provides version management.

---

# Rolling Updates

Suppose:

```text
Current Version = v1.21
New Version = v1.22
```

Deployment updates Pods gradually.

```text
Old Pod
↓
New Pod
↓
Health Check
↓
Next Pod Update
```

Benefits:

- Zero Downtime
- Safe Releases
- Easy Rollback

---

# Rollback

Suppose:

```text
New Version Deployed
        ↓
Application Crashes
```

Rollback immediately:

```bash
kubectl rollout undo deployment/my-app
```

Deployment automatically returns to the previous ReplicaSet.

```text
Old Version Restored ✅
```

---

# Troubleshooting Failed Deployment

Check rollout status:

```bash
kubectl rollout status deployment/my-app
```

Output:

```text
Stuck Deployment
```

---

Check Pods:

```bash
kubectl get pods
```

Possible output:

```text
CrashLoopBackOff
```

---

Check Previous Logs:

```bash
kubectl logs <pod-name> --previous
```

Used to identify startup failures.

---

# Sample Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: my-app

spec:
  replicas: 3

  strategy:
    type: RollingUpdate

    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1

  selector:
    matchLabels:
      app: my-app

  template:
    metadata:
      labels:
        app: my-app

    spec:
      containers:
      - name: my-app
        image: nginx:1.21

        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"

          limits:
            memory: "128Mi"
            cpu: "500m"
```

---

# Understanding Rolling Update Settings

## maxSurge

```yaml
maxSurge: 1
```

Kubernetes can create:

```text
1 Extra Pod
```

during update.

---

## maxUnavailable

```yaml
maxUnavailable: 1
```

Allows:

```text
1 Pod Unavailable
```

during update.

---

# Interview Summary

## Pod

- Smallest unit in Kubernetes
- Runs one or more containers
- Shared IP and Storage

## ReplicaSet

- Maintains desired number of Pods
- Provides self-healing
- Uses Labels and Selectors

## Deployment

- Manages ReplicaSets
- Provides Rolling Updates
- Provides Rollbacks
- Production-ready workload object