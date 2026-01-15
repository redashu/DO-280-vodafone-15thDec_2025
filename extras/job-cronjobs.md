# What is a Job?

A Job in Kubernetes / OpenShift is used to run one-time or batch workloads.

Key idea:

Run a Pod until it completes successfully

Unlike Deployments:

Pods are not restarted forever

Job completes and exits

Success or failure is tracked

## When to Use Jobs

Use Jobs for:

1. Database migrations
2. Batch data processing
3. Backup / restore tasks
4. One-time scripts
5. CI/CD build steps
6. ETL jobs

## Job Execution Model

Job creates one or more Pods

Pod runs → exits

Job tracks completion

Job finishes when desired completions are met

Important states:

- Active
- Succeeded
- Failed

## Job Types (Important)

1. Single Pod Job – run once
2. Parallel Job – run multiple Pods
3. Indexed Job – each Pod gets an index (batch processing)

## Hands-On Demo: Simple Job

### Step 1: Job YAML

Create file `simple-job.yaml`

```yaml
apiVersion: batch/v1
kind: Job
metadata:
    name: hello-job
spec:
    backoffLimit: 3
    template:
        spec:
            restartPolicy: Never
            containers:
                - name: hello
                    image: busybox
                    command: ["sh", "-c", "echo Hello from Job && sleep 5"]
```

Apply:

```bash
kubectl apply -f simple-job.yaml
# or oc apply -f simple-job.yaml
```

### Step 2: Observe Job

```bash
kubectl get jobs
kubectl get pods
kubectl logs job/hello-job
```

Result:

- Pod runs
- Prints message
- Exits
- Job status → Complete

## Failure Handling (Backoff)

Modify command to fail:

```bash
command: ["sh", "-c", "exit 1"]
```

Observe:

```bash
kubectl describe job hello-job
```

Kubernetes retries until backoffLimit is reached.

## Parallel Job Demo

```yaml
apiVersion: batch/v1
kind: Job
metadata:
    name: parallel-job
spec:
    completions: 5
    parallelism: 2
    template:
        spec:
            restartPolicy: Never
            containers:
                - name: worker
                    image: busybox
                    command: ["sh", "-c", "echo Processing task && sleep 10"]
```

Meaning:

- Total Pods: 5
- Max running at once: 2

## Cleanup Jobs Automatically

Add TTL:

```yaml
ttlSecondsAfterFinished: 60
```

Job and Pods auto-delete after 60 seconds (very important for prod).

## What is a CronJob?

A CronJob runs Jobs on a schedule, just like Linux crontab.

Key idea:

CronJob = Scheduler → Job → Pod

## When to Use CronJobs

Use CronJobs for:

1. Nightly backups
2. Log cleanup
3. Report generation
4. Sync jobs
5. Health checks

## Hands-On Demo: CronJob

### Step 1: CronJob YAML

Create `hello-cronjob.yaml`

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
    name: hello-cron
spec:
    schedule: "*/1 * * * *"
    successfulJobsHistoryLimit: 3
    failedJobsHistoryLimit: 1
    jobTemplate:
        spec:
            template:
                spec:
                    restartPolicy: Never
                    containers:
                        - name: hello
                            image: busybox
                            command:
                                - sh
                                - -c
                                - date && echo Hello from CronJob
```

Apply:

```bash
kubectl apply -f hello-cronjob.yaml
```

### Step 2: Observe CronJob

```bash
kubectl get cronjobs
kubectl get jobs
kubectl get pods
```

Logs:

```bash
kubectl logs job/<job-name>
```

A new Job is created every minute ⏱️

## Cron Schedule Format (Important)

```
* * * * *
| | | | |
| | | | └─ Day of week (0-6)
| | | └── Month (1-12)
| | └── Day of month (1-31)
| └──── Hour (0-23)
└────── Minute (0-59)
```

Example:

- `0 2 * * *` → every day at 2 AM
- `*/5 * * * *` → every 5 minutes

## Concurrency Policies (VERY IMPORTANT)

```yaml
concurrencyPolicy: Forbid
```

Options:

- **Allow** (default) – overlapping allowed
- **Forbid** – skip if previous still running
- **Replace** – kill old job, start new

## Suspend CronJob

```yaml
suspend: true
```

Useful during:

- Maintenance windows
- Incident response

## Jobs & CronJobs in OpenShift (OCP)

In OpenShift:

- Use `oc` instead of `kubectl`
- Jobs run under SCC & RBAC
- CronJobs commonly used for:
    - Cluster maintenance
    - Operator internal tasks
    - Backup automation

## Job vs Deployment (Interview Gold)

| Feature | Job | Deployment |
|---------|-----|-----------|
| Runs forever | ❌ | ✅ |
| Completion tracked | ✅ | ❌ |
| Retry on failure | ✅ | ❌ |
| Batch workloads | ✅ | ❌ |
| Long-running apps | ❌ | ✅ |

## Common Exam / Real-World Mistakes

- Using Deployment for batch tasks ❌
- Forgetting `restartPolicy: Never`
- Not cleaning old Jobs
- Wrong cron schedule timezone
- Allowing overlapping CronJobs