# PART 1 — SERVICE ACCOUNTS (SA)

## 1. What is a Service Account? (simple but critical)

A Service Account is:

- The identity used by a pod to talk to the OpenShift/Kubernetes API

### Important:

- **Users** → humans
- **ServiceAccounts** → applications
- Every pod runs as a ServiceAccount.

## 2. Default ServiceAccount behavior

Each project has:

- `default`
- `builder`
- `deployer`

Check:

```bash
oc get sa
```

By default:

- Pods run as default SA
- Very restricted permissions
- Very restricted SCC

## 3. Why Service Accounts matter for security

ServiceAccount controls:

- API access (RBAC)
- Which SCC can be used
- Whether pod can:
    - Run as root
    - Mount host paths
    - Use privileged mode

SAs are the security identity of workloads

## 4. Create a custom ServiceAccount (BEST PRACTICE)

```bash
oc create sa web-sa
```

Use it in a deployment:

```yaml
spec:
    serviceAccountName: web-sa
```

# PART 2 — SECURITY CONTEXT CONSTRAINTS (SCC)

## 5. What is SCC? (VERY IMPORTANT)

- SCC is OpenShift-only.
- SCC defines WHAT a pod is allowed to do at runtime

Examples:

- Can it run as root?
- Can it mount hostPath?
- Can it use privileged containers?
- Can it use specific SELinux contexts?

Kubernetes has PodSecurity, but SCC is more powerful.

## 6. Default SCCs you MUST know

List:

```bash
oc get scc
```

Key ones:

| SCC | Purpose |
|-----|---------|
| restricted | Default, most secure |
| anyuid | Allows root |
| privileged | Full host access |
| nonroot | Enforces non-root |
| hostnetwork | Allows host networking |

## 7. Default behavior (this explains MANY errors)

By default:

- `default SA` → `restricted SCC`

Container runs with:

- Random UID
- No root
- No host access

That's why images like nginx / mysql / redis often fail initially.

# PART 3 — HANDS-ON DEMO (REAL & PRACTICAL)

## 8. Scenario: Pod fails due to SCC

Check pod error:

```bash
oc get pods
oc describe pod demo-web-xxx
```

You'll see:

```
permission denied
cannot create directory /var/cache/nginx
```

This means:

- Image expects root
- SCC does not allow root

## 9. Quick demo fix (NOT recommended for prod)

Allow root using anyuid SCC

```bash
oc adm policy add-scc-to-user anyuid \
    -z web-sa \
    -n demo-proj
```

Now restart pod:

```bash
oc rollout restart deployment demo-web
```

Verify:

```bash
oc get pod demo-web-xxx -o yaml | grep scc
```

Output:

```yaml
openshift.io/scc: anyuid
```

## 10. Why this works

| Before | After |
|--------|-------|
| restricted SCC | anyuid SCC |
| Random UID | UID 0 |
| Permission denied | App runs |

### ⚠️ VERY IMPORTANT WARNING (ENTERPRISE RULE)

❌ **Do NOT use anyuid in production**

Why?

- Breaks OpenShift security guarantees
- Fails audits (PCI, SOC2)
- Increases container escape risk

# PART 4 — CORRECT ENTERPRISE WAY (RECOMMENDED)

## 11. Fix image instead of SCC

Use images that:

- Support arbitrary UID
- Write to /tmp
- Don't require root

Example Dockerfile fix:

```dockerfile
FROM registry.access.redhat.com/ubi8/nginx-120
RUN chmod -R g+w /var/cache/nginx /var/run /etc/nginx
USER 1001
```

Now it runs with:

- restricted SCC
- No security exception

## 12. Custom SCC (advanced, platform teams)

Platform teams may create:

- Custom SCC
- Limited permissions
- Assigned to specific SA only

⚠️ This is admin-level and audited