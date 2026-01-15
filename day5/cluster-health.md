# Cluster Health Monitoring Guide

## Cluster Health Checklist
1. Cluster Operators (platform health)
2. Control Plane (API, etcd)
3. Nodes (CPU, memory, disk, network)
4. Workloads (pods, deployments)
5. Observability (alerts, metrics)

## 1️⃣ Cluster Operators — FIRST thing to check (MOST IMPORTANT)

OpenShift is operator-driven. If operators are unhealthy → cluster is unhealthy.

### Command (run this FIRST always)
```bash
oc get co
```

### Example healthy output

| NAME | AVAILABLE | PROGRESSING | DEGRADED |
|------|-----------|-------------|----------|
| authentication | True | False | False |
| console | True | False | False |
| image-registry | True | False | False |
| ingress | True | False | False |
| kube-apiserver | True | False | False |
| machine-config | True | False | False |

### How to interpret

| Field | Meaning |
|-------|---------|
| AVAILABLE | Operator working |
| PROGRESSING | Upgrade / change ongoing |
| DEGRADED | 🚨 Problem |

🚨 If DEGRADED = True → investigate immediately

```bash
oc describe co ingress
```

## 2️⃣ Control Plane Health (API + etcd)

### 2.1 Kubernetes API Server
```bash
oc get pods -n openshift-kube-apiserver
```

All pods should be: **Running**

Check readiness:
```bash
oc get --raw /healthz
```

Expected: `ok`

### 2.2 etcd (CRITICAL)

etcd is the brain of the cluster.

```bash
oc get pods -n openshift-etcd
```

Check etcd health:
```bash
oc exec -n openshift-etcd etcd-master-0 -- etcdctl endpoint health
```

Healthy cluster: `is healthy`

🚨 etcd issues = cluster-wide outage risk

## 3️⃣ Node Health & Performance

### 3.1 Node status
```bash
oc get nodes
```

Healthy: **Ready**

Unhealthy examples: `NotReady`, `SchedulingDisabled`

### 3.2 Resource pressure (VERY common)
```bash
oc describe node <node-name>
```

Look for:
- MemoryPressure
- DiskPressure
- PIDPressure

If any = True → workloads will fail or evict.

### 3.3 Node resource usage
```bash
oc adm top nodes
```

| NAME | CPU(cores) | CPU% | MEMORY(bytes) | MEMORY% |
|------|-----------|------|---------------|---------|
| worker-1 | 1500m | 75% | 6Gi | 80% |

**Rules of thumb:**
- CPU > 80% → scaling needed
- Memory > 85% → eviction risk

## 4️⃣ Pod & Workload Health

### 4.1 Pod status
```bash
oc get pods -A
```

🚨 Watch for: `CrashLoopBackOff`, `ImagePullBackOff`, `Pending`

### 4.2 Resource usage per pod
```bash
oc adm top pods -n <project>
```

Use this to:
- Detect memory leaks
- Detect CPU starvation
- Tune requests/limits

### 4.3 Events (MOST UNDERRATED)
```bash
oc get events -A --sort-by=.lastTimestamp
```

This shows:
- Scheduling failures
- SCC violations
- Image pull issues
- Network errors

## 5️⃣ Networking & Ingress Health

### 5.1 Router health (HAProxy)
```bash
oc get pods -n openshift-ingress
```

All router pods must be: **Running**

Check service:
```bash
oc get svc -n openshift-ingress
```

If router down → apps inaccessible

### 5.2 DNS health (often ignored)
```bash
oc get pods -n openshift-dns
```

DNS issues cause:
- Image pull failures
- Service discovery problems

## 6️⃣ Storage Health

### 6.1 Persistent Volumes
```bash
oc get pv
oc get pvc -A
```

Look for:
- Pending PVCs
- Failed mounts

### 6.2 Registry storage (very important)
```bash
oc get pods -n openshift-image-registry
```

If registry is down:
- Builds fail
- Deployments fail

## 7️⃣ Monitoring & Alerts (ADMIN GOLD)

OpenShift ships built-in Prometheus + Alertmanager.

### Check alert status (CLI)
```bash
oc get alerts -A
```

### Web UI (preferred)
Administrator → Observe → Alerts

🚨 Red alerts = immediate action
🟡 Warnings = capacity planning

## 8️⃣ Performance Signals

| Signal | Why |
|--------|-----|
| API latency | Control plane overload |
| etcd disk IOPS | Cluster stability |
| Node memory | Pod eviction risk |
| Router latency | User experience |
| Pod restarts | App instability |

## 9️⃣ One-command QUICK health check (save this)
```bash
oc get co && oc get nodes && oc get pods -A | grep -v Running
```

This gives 80% cluster health in seconds.

## 🔟 Real-world admin checklist (daily)

- ✔ `oc get co`
- ✔ `oc get nodes`
- ✔ `oc adm top nodes`
- ✔ Check alerts
- ✔ Scan events