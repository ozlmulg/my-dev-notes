---
description: Kubernetes Job & CronJob - Core Concepts, Commands, YAML Configuration, and Practical Examples
---

# Kubernetes Objects - Job & CronJob

## Overview

Jobs and CronJobs in Kubernetes are used for running batch processes and scheduled tasks. Jobs run to completion, while
CronJobs run on a schedule.

## Job

### Purpose

Jobs are used for:

* **Batch Processing**: One-time tasks that need to complete
* **Data Processing**: ETL jobs, data analysis
* **Backup Operations**: Database backups, file backups
* **Testing**: Running tests, validation scripts

### Basic Job

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command:
        - perl
        - -Mbignum=bpi
        - -wle
        - print bpi(2000)
      restartPolicy: Never
  backoffLimit: 4
```

### Job with Parallelism

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: parallel-job
spec:
  parallelism: 3
  completions: 6
  template:
    spec:
      containers:
      - name: worker
        image: busybox
        command: ["sh", "-c", "echo 'Hello from job'; sleep 10"]
      restartPolicy: Never
```

## CronJob

### Purpose

CronJobs are used for:

* **Scheduled Tasks**: Regular maintenance, backups
* **Periodic Jobs**: Data synchronization, cleanup
* **Automated Operations**: System maintenance, monitoring

### Basic CronJob

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

### CronJob with Concurrency Policy

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup-job
spec:
  schedule: "0 2 * * *"
  concurrencyPolicy: Forbid
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: backup-tool:latest
            command: ["/backup.sh"]
          restartPolicy: OnFailure
```

## Cron Schedule Format

```shell
# Format: minute hour day month day-of-week
# Examples:
"0 0 * * *"      # Daily at midnight
"0 2 * * *"      # Daily at 2 AM
"0 */6 * * *"    # Every 6 hours
"0 9 * * 1"      # Every Monday at 9 AM
"*/15 * * * *"   # Every 15 minutes
```

## Basic Commands

```shell
# Create Job
kubectl apply -f job.yaml

# Create CronJob
kubectl apply -f cronjob.yaml

# List Jobs
kubectl get jobs

# List CronJobs
kubectl get cronjobs

# Check Job status
kubectl describe job <job-name>

# Check CronJob status
kubectl describe cronjob <cronjob-name>

# Delete Job
kubectl delete job <job-name>

# Delete CronJob
kubectl delete cronjob <cronjob-name>
```

## Common Use Cases

### Database Backup Job

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: db-backup
spec:
  template:
    spec:
      containers:
      - name: backup
        image: postgres:13
        command:
        - /bin/sh
        - -c
        - pg_dump -h db-service -U postgres > /backup/backup.sql
        env:
        - name: PGPASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        volumeMounts:
        - name: backup-volume
          mountPath: /backup
      volumes:
      - name: backup-volume
        persistentVolumeClaim:
          claimName: backup-pvc
      restartPolicy: Never
```

### Log Cleanup CronJob

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: log-cleanup
spec:
  schedule: "0 3 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cleanup
            image: busybox
            command:
            - /bin/sh
            - -c
            - find /logs -name "*.log" -mtime +7 -delete
          restartPolicy: OnFailure
```

## References

- [Kubernetes Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/)
- [Kubernetes CronJobs](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)
- [GitHub - Aytitech K8sFundamentals - Job/CronJob](https://github.com/aytitech/k8sfundamentals/tree/main/jobcronjob)
