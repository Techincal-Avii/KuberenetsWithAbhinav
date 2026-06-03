# Kubernetes Fundamentals

## What is Kubernetes?

Kubernetes (K8s) is a container orchestration tool used to manage containerized applications at scale.

It helps in:
- Deploying containers
- Scaling applications
- Managing failures
- Automating operations

---

# Why Kubernetes?

Kubernetes solves five major problems:

## 1. Self-Healing

If a container crashes, Kubernetes automatically restarts it.

Example:
- Container crashes
- Kubernetes detects failure
- New container is started automatically

---

## 2. Auto Scaling

If traffic increases, Kubernetes can automatically create more Pods.

Benefits:
- Handles high traffic
- Better performance
- Cost optimization

---

## 3. Rescheduling

If a worker node fails:

- Kubernetes detects node failure
- Pods are automatically moved to healthy nodes

This ensures high availability.

---

## 4. Rolling Updates

Used for deploying new application versions without downtime.

Benefits:
- Zero downtime deployments
- Easy rollback
- Better user experience

---

## 5. Declarative Configuration

Kubernetes uses YAML files to define the desired state.

Example:

```yaml
replicas: 3
image: nginx
```

Instead of manually managing hundreds of containers, we simply update the YAML file.

---

# Kubernetes Architecture

Kubernetes consists of two major components:

## 1. Control Plane

The brain of Kubernetes.

Responsible for:
- Taking decisions
- Managing cluster state
- Scheduling workloads

---

## 2. Worker Nodes

The machines where applications actually run.

Responsible for:
- Running Pods
- Executing workloads
- Serving traffic

---

# Control Plane Components

## 1. API Server

Acts as the entry point for Kubernetes.

All commands are received through the API Server.

Example:

```bash
kubectl get pods
kubectl apply -f pod.yaml
```

Responsibilities:
- Receives requests
- Validates requests
- Communicates with other components

---

## 2. ETCD

The database of Kubernetes.

Stores:
- Pods
- Services
- Nodes
- Secrets
- ConfigMaps
- Cluster State

Important:

If ETCD is lost or corrupted, the entire cluster state is lost.

---

## 3. Scheduler

Responsible for deciding where Pods should run.

Checks:
- Available nodes
- CPU availability
- Memory availability
- Node constraints

Example:

```text
Pod Created
↓
Scheduler Selects Node-2
↓
Pod Scheduled
```

---

## 4. Controller Manager

Acts as the monitoring component of Kubernetes.

Responsibilities:
- Self-healing
- Replica management
- Node monitoring
- Reconciliation

---

### What is Reconciliation?

Controller Manager continuously compares:

```text
Desired State
vs
Current State
```

If a mismatch is detected, Kubernetes automatically fixes it.

Example:

Desired Pods = 3

Current Pods = 2

Result:

Kubernetes automatically creates 1 new Pod.

---

# Worker Node Components

## 1. Kubelet

Agent running on every worker node.

Responsibilities:
- Receives instructions from Control Plane
- Starts Pods
- Monitors Pod health
- Reports status back to API Server

---

## 2. Kube Proxy

Responsible for networking.

Responsibilities:
- Service networking
- Load balancing
- Traffic routing

Ensures traffic reaches the correct Pod.

---

## 3. Container Runtime

Component that actually runs containers.

Examples:
- containerd
- CRI-O

Responsibilities:
- Pull images
- Create containers
- Start containers

---

# Kubernetes Request Flow

When we run:

```bash
kubectl apply -f pod.yaml
```

The following sequence happens:

```text
kubectl apply -f pod.yaml
        ↓
API Server
(Request Received)
        ↓
ETCD
(Desired State Stored)
        ↓
Scheduler
(Selects Best Node)
        ↓
API Server
        ↓
Kubelet on Worker Node
        ↓
Container Runtime
        ↓
Container Starts Running
```

---

# Interview Summary

### Control Plane Components

- API Server
- ETCD
- Scheduler
- Controller Manager

### Worker Node Components

- Kubelet
- Kube Proxy
- Container Runtime

### Key Kubernetes Features

- Self-Healing
- Auto Scaling
- Rescheduling
- Rolling Updates
- Declarative Configuration