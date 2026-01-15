# PART 1 — What is Istio? (Concept)

## 1️⃣ What is Istio?

Istio is a service mesh that adds a dedicated infrastructure layer for managing:

- Service-to-service traffic
- Security (mTLS, identity)
- Observability (metrics, tracing)
- Policy enforcement

👉 Without changing application code

## 2️⃣ Why Kubernetes alone is not enough

Kubernetes gives you:

- Pod networking
- Basic load balancing
- Service discovery

But NO native support for:

- mTLS between services
- Traffic splitting (canary, A/B)
- Retry, timeout, circuit breaking
- End-to-end tracing
- Fine-grained access policies

Istio adds all of this uniformly.

## 3️⃣ How Istio works (key architecture idea)

```
App Pod
 ├─ Application container
 └─ Envoy sidecar  ← Istio injects this
```

- Every pod gets an Envoy proxy
- All traffic flows through Envoy
- Istio control plane configures Envoy centrally

## 4️⃣ Install operators

Install these operators in this order:

1. Elasticsearch Operator
2. Jaeger Operator
3. Kiali Operator
4. Red Hat OpenShift Service Mesh

From: Administrator → Operators → OperatorHub

Install them cluster-wide (default).

## 5️⃣ Create Service Mesh control plane

Create project:

```bash
oc new-project istio-system
```

Create control plane (minimal, demo-friendly):

```yaml
apiVersion: maistra.io/v2
kind: ServiceMeshControlPlane
metadata:
    name: basic
    namespace: istio-system
spec:
    tracing:
        type: Jaeger
    addons:
        kiali:
            enabled: true
        grafana:
            enabled: true
        prometheus:
            enabled: true
```

Apply:

```bash
oc apply -f smcp.yaml
```

Verify:

```bash
oc get pods -n istio-system
```

All pods should be Running.

## 6️⃣ Add an application namespace to the mesh

Create app project:

```bash
oc new-project mesh-demo
```

Add it to the mesh:

```yaml
apiVersion: maistra.io/v1
kind: ServiceMeshMember
metadata:
    name: mesh-demo
    namespace: mesh-demo
spec:
    controlPlaneRef:
        name: basic
        namespace: istio-system
```

Apply:

```bash
oc apply -f smm.yaml
```

# PART 3 — Demo: Two microservices + traffic control

## 🎯 Demo goal

- Deploy two versions of a service
- Control traffic using Istio
- Enable mTLS
- Observe traffic in Kiali

## 7️⃣ Enable automatic sidecar injection

```bash
oc label namespace mesh-demo istio-injection=enabled
```

Verify:

```bash
oc get ns mesh-demo --show-labels
```

## 8️⃣ Deploy sample microservices

### Service v1

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: app-v1
spec:
    replicas: 1
    selector:
        matchLabels:
            app: app
            version: v1
    template:
        metadata:
            labels:
                app: app
                version: v1
        spec:
            containers:
            - name: app
                image: docker.io/hashicorp/http-echo
                args:
                - "-text=Hello from v1"
                ports:
                - containerPort: 5678
```

### Service v2

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: app-v2
spec:
    replicas: 1
    selector:
        matchLabels:
            app: app
            version: v2
    template:
        metadata:
            labels:
                app: app
                version: v2
        spec:
            containers:
            - name: app
                image: docker.io/hashicorp/http-echo
                args:
                - "-text=Hello from v2"
                ports:
                - containerPort: 5678
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
    name: app
spec:
    selector:
        app: app
    ports:
    - port: 80
        targetPort: 5678
```

Apply:

```bash
oc apply -f .
```

Check sidecars:

```bash
oc get pods
```

Pods will show 2/2 containers (app + envoy).

# PART 4 — Istio Traffic Management

## 9️⃣ Create DestinationRule

```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
    name: app
spec:
    host: app
    subsets:
    - name: v1
        labels:
            version: v1
    - name: v2
        labels:
            version: v2
```

## 🔟 VirtualService (traffic split demo)

### 100% traffic to v1

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
    name: app
spec:
    hosts:
    - app
    http:
    - route:
        - destination:
                host: app
                subset: v1
```

Test:

```bash
oc exec -it <pod> -- curl app
```

### Canary: 80% v1 / 20% v2

```yaml
http:
- route:
    - destination:
            host: app
            subset: v1
        weight: 80
    - destination:
            host: app
            subset: v2
        weight: 20
```

Now traffic is controlled at L7 🎯

# PART 5 — Security: mTLS

## 1️⃣1️⃣ Enable STRICT mTLS

```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
    name: default
spec:
    mtls:
        mode: STRICT
```

This ensures:

- All service-to-service traffic is encrypted
- Identity-based communication

## 1️⃣2️⃣ Kiali (service topology)

Access:

Administrator → Observe → Kiali

You get:

- Live service graph
- Traffic split visualization
- mTLS indicators
- Error rates & latency

## 1️⃣3️⃣ Jaeger (distributed tracing)

Automatically captures:

- Request paths
- Latency per service
- Bottlenecks

Zero code instrumentation required.

# PART 7 — Policy & control basics

Istio can enforce:

- Who can talk to whom
- Rate limits
- Deny rules

Example (deny all except v1 → v2):

```yaml
kind: AuthorizationPolicy
```

Used heavily in zero-trust architectures.

## Final mental model (remember this)

```
Kubernetes = deploy & scale
Istio      = secure, observe, control traffic
```