# Kubernetes Interview Questions

## Q1. Your Pod is in CrashLoopBackOff. Walk me through your complete debugging process.

### What is CrashLoopBackOff?

CrashLoopBackOff is a Kubernetes pod state that indicates a container is continuously crashing and Kubernetes is repeatedly trying to restart it.

---

## Debugging Process

### Step 1: Check Pod Status

```bash
kubectl get pods
```

Purpose:
- Check current pod status
- Verify restart count
- Confirm CrashLoopBackOff state

---

### Step 2: Check Events

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

Purpose:
- Identify recent failures
- Check scheduling issues
- Check probe failures
- Check image pull errors

---

### Step 3: Describe the Pod

```bash
kubectl describe pod <pod-name>
```

Purpose:
- View detailed pod information
- Check liveness probe
- Check readiness probe
- Check resource limits
- Check container exit code
- Check recent events

---

### Step 4: Check Container Logs

Current container logs:

```bash
kubectl logs <pod-name>
```

Previous crashed container logs:

```bash
kubectl logs <pod-name> --previous
```

Purpose:
- Identify application errors
- Check startup failures
- Check stack traces
- Check dependency issues

---

### Step 5: Check Resource Usage

Pod level:

```bash
kubectl top pod <pod-name>
```

Node level:

```bash
kubectl top nodes
```

Purpose:
- Identify memory pressure
- Identify CPU throttling
- Check for OOMKilled scenarios

---

### Step 6: Verify ConfigMaps and Secrets

Check ConfigMaps:

```bash
kubectl get configmap
```

Check Secrets:

```bash
kubectl get secrets
```

Purpose:
- Verify environment variables
- Check missing configuration values
- Validate secret references

---

## Common Root Causes

### 1. Application Crash

Examples:
- Syntax error
- Runtime exception
- Missing dependency

Result:
- Container exits immediately

---

### 2. OOMKilled (Memory Issue)

Symptoms:

```bash
Exit Code: 137
```

Reason:
- Application exceeded memory limit

Solution:
- Increase memory limits
- Optimize application memory usage

---

### 3. Incorrect Liveness Probe

Reason:
- Wrong endpoint path
- Wrong port
- Insufficient startup time

Solution:
- Verify probe configuration
- Increase initialDelaySeconds

---

### 4. ConfigMap or Secret Mismatch

Reason:
- Missing environment variable
- Wrong secret key
- Invalid configuration

Solution:
- Verify ConfigMap and Secret values

---

### 5. Image or Startup Command Issue

Reason:
- Wrong image version
- Invalid entrypoint
- Incorrect startup command

Solution:
- Verify image and command configuration

---

## Important Exit Codes

| Exit Code | Meaning |
|------------|----------|
| 0 | Success |
| 1 | Application Error |
| 137 | OOMKilled |
| 139 | Segmentation Fault |
| 143 | SIGTERM (Graceful Shutdown) |

---

## Interview Summary

"When troubleshooting a CrashLoopBackOff, I first check pod status and events, then describe the pod, inspect current and previous logs, verify resource consumption, and validate ConfigMaps and Secrets. The most common causes are application crashes, OOMKilled errors, probe misconfigurations, configuration issues, and incorrect startup commands."