# Lab 14 – LimitRange and CronJob in OpenShift

## 1. Create a New Project

Create a new project for the lab:

```bash
oc new-project limitrange-lab
```

---

## 2. Define a LimitRange

Apply a LimitRange to control Pod CPU and memory usage:

```bash
oc create limitrange pod-limit-range \
  --max=cpu=1,memory=1Gi \
  --min=cpu=100m,memory=128Mi \
  --default=cpu=500m,memory=512Mi \
  --default-request=cpu=200m,memory=256Mi \
  --type=Pod \
  -n limitrange-lab
```

### Verify LimitRange

```bash
oc get limitrange -n limitrange-lab
oc describe limitrange pod-limit-range -n limitrange-lab
```

---

## 3. Test with a Pod

Run a test NGINX pod to validate LimitRange behavior:

```bash
oc run nginx-test-pod --image=nginx:latest --port=80 -n limitrange-lab
```

Describe the pod to verify applied limits:

```bash
oc describe pod nginx-test-pod -n limitrange-lab
```

---

## 4. Create a CronJob

Create a CronJob that runs every minute:

```bash
oc create cronjob scheduled-task \
  --image=busybox:latest \
  --schedule="* * * * *" \
  --restart=OnFailure \
  -n limitrange-lab \
  -- /bin/sh -c 'echo "Task executed at $(date)"'
```

### Verify CronJob

```bash
oc get cronjobs -n limitrange-lab
oc describe cronjob scheduled-task -n limitrange-lab
oc get jobs -n limitrange-lab
```

---

## 5. View Job Logs

List jobs:

```bash
oc get jobs -n limitrange-lab
```

View logs for a specific job:

```bash
oc logs job/<JOB_NAME> -n limitrange-lab
```

---

## 6. Verification Commands (For Screenshots)

```bash
oc describe limitrange pod-limit-range -n limitrange-lab

oc describe pod nginx-test-pod -n limitrange-lab

oc get cronjobs -n limitrange-lab
oc get jobs -n limitrange-lab

oc logs job/<JOB_NAME> -n limitrange-lab
```

---

## 7. Cleanup

Delete the entire project:

```bash
oc delete project limitrange-lab
```

---

## Summary

In this lab, you successfully:

* Created a dedicated OpenShift project
* Applied a LimitRange to enforce CPU and memory constraints
* Deployed a pod to verify enforcement
* Created and monitored a CronJob
* Captured pod and job logs
* Cleaned up resources at the end
