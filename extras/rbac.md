# RBAC Building Blocks and Commands

## 1️⃣ RBAC Building Blocks (Quick Clarity)

| Object | Scope | Purpose |
|--------|-------|---------|
| User | Cluster | Identity |
| Group | Cluster | Collection of users |
| Role | Namespace | Permissions in ONE project |
| ClusterRole | Cluster | Permissions cluster-wide |
| RoleBinding | Namespace | Bind Role/ClusterRole to user/group |
| ClusterRoleBinding | Cluster | Bind ClusterRole to user/group |

## 2️⃣ Create a Project (Admin or Self-Service)

**Self-service (default users)**

```bash
oc new-project demo-proj
```

**Admin-controlled**

```bash
oc adm new-project demo-proj --admin=ashu
```

## 3️⃣ View Existing Roles & ClusterRoles (VERY Useful)

**List namespace roles**

```bash
oc get roles -n demo-proj
```

**List cluster roles**

```bash
oc get clusterroles
```

**Describe a role (see exact permissions)**

```bash
oc describe clusterrole edit
```

## 4️⃣ Most Common Permission Assignments (REAL LIFE)

**🔹 Give user admin access to ONE project**

```bash
oc adm policy add-role-to-user admin ashu -n demo-proj
```

**🔹 Give user edit access (dev role)**

```bash
oc adm policy add-role-to-user edit dev1 -n demo-proj
```

**🔹 Give read-only access**

```bash
oc adm policy add-role-to-user view auditor -n demo-proj
```

## 5️⃣ Group-Based Access (ENTERPRISE BEST PRACTICE)

**Create a group**

```bash
oc adm groups new dev-team
```

**Add users to group**

```bash
oc adm groups add-users dev-team dev1 dev2 ashu
```

**Bind role to group (BEST PRACTICE)**

```bash
oc adm policy add-role-to-group edit dev-team -n demo-proj
```

- ✅ Add/remove users → permissions auto-apply
- ❌ No per-user RBAC chaos

## 6️⃣ Cluster-Level Permissions (Use Carefully ⚠️)

**Give full cluster admin (VERY RESTRICTED)**

```bash
oc adm policy add-cluster-role-to-user cluster-admin ashu
```

**Read-only cluster access**

```bash
oc adm policy add-cluster-role-to-user view auditor
```

**Allow user to manage projects**

```bash
oc adm policy add-cluster-role-to-user self-provisioner dev1
```

## 7️⃣ Remove Permissions (IMPORTANT)

**Remove role from user**

```bash
oc adm policy remove-role-from-user edit dev1 -n demo-proj
```

**Remove cluster role**

```bash
oc adm policy remove-cluster-role-from-user cluster-admin ashu
```

## 8️⃣ Verify Permissions (MOST IMPORTANT DEBUG TOOL)

**Check what you can do**

```bash
oc auth can-i create pods -n demo-proj
```

**Check as another user**

```bash
oc auth can-i delete deployments -n demo-proj --as=dev1
```

**Cluster-level check**

```bash
oc auth can-i delete nodes
```

## 9️⃣ Inspect Bindings (Debugging)

**Namespace bindings**

```bash
oc get rolebinding -n demo-proj
oc describe rolebinding <name> -n demo-proj
```

**Cluster bindings**

```bash
oc get clusterrolebinding
```

## 🔟 Create Your Own Custom Role (Advanced but Common)

Example: allow pod + log access only

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
    name: pod-reader
    namespace: demo-proj
rules:
    - apiGroups: [""]
        resources: ["pods", "pods/log"]
        verbs: ["get", "list", "watch"]
```

**Apply:**

```bash
oc apply -f pod-reader-role.yaml
```

**Bind it:**

```bash
oc adm policy add-role-to-user pod-reader dev1 -n demo-proj
```

## 1️⃣1️⃣ ServiceAccount Permissions (CI/CD Use Case)

**Grant SA edit access**

```bash
oc adm policy add-role-to-user edit \
    system:serviceaccount:demo-proj:default \
    -n demo-proj
```

## 1️⃣2️⃣ Common Enterprise RBAC Patterns

| Team | Role |
|------|------|
| Developers | edit |
| QA | view |
| CI/CD SA | edit |
| Platform Admin | cluster-admin |
| Auditors | view (cluster) |

## 1️⃣3️⃣ Very Common Mistakes ❌

- ❌ Giving cluster-admin to developers
- ❌ Per-user RoleBindings (no groups)
- ❌ Forgetting to verify with `oc auth can-i`
- ❌ Mixing cluster & namespace scope