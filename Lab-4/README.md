# Kubernetes Web App Deployment

This README explains the full Kubernetes workflow used to deploy, scale, update, and manage an NGINX application inside the **web-app** namespace.

---

## 🏗️ 1. Create Namespace, Deployment, Service, ResourceQuota, and HPA

```bash
kubectl apply -f web_app-namespace.yaml -f nginx-service.yaml -f nginx-deployment.yaml -f quota.yaml -f hpa.yaml
```

Expected output:

```
namespace/web-app created
service/nginx-service created
deployment.apps/nginx-deployment created
resourcequota/webapp-quota created
horizontalpodautoscaler.autoscaling/nginx-hpa created
```

---

## 📈 2. Manual Scaling

```bash
kubectl scale deployment nginx-deployment --replicas=4 -n web-app
kubectl get pods -n web-app
```

Example:

```
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-c6c897f54-fzrvf   1/1     Running   0          8s
nginx-deployment-c6c897f54-mq2xp   1/1     Running   0          8s
nginx-deployment-c6c897f54-xr8l2   1/1     Running   0          3m2s
nginx-deployment-c6c897f54-z8n4f   1/1     Running   0          3m2s
```

Deployment status:

```bash
kubectl get deployment nginx-deployment -n web-app
```

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   4/4     4            4           4m4s
```

---

## 🔄 3. Rolling Update

Apply new rollout strategy:

```bash
kubectl apply -f rolling-update.yaml
```

Check rollout status:

```bash
kubectl rollout status deployment/nginx-deployment -n web-app
```

Example output shows replica updates until rollout completes:

```
deployment "nginx-deployment" successfully rolled out
```

Check current pods:

```bash
kubectl get pods -n web-app -o wide
```

---

## ↩️ 4. Rollback

List rollback history:

```bash
kubectl rollout history deployment/nginx-deployment -n web-app
```

Rollback to previous revision:

```bash
kubectl rollout undo deployment/nginx-deployment -n web-app
```

Check rollout:

```bash
kubectl rollout status deployment/nginx-deployment -n web-app
```

Check pods after rollback:

```bash
kubectl get pods -n web-app
```

---

## ✅ 5. Verification

### Pods

```bash
kubectl get pods -n web-app
```

### Service

```bash
kubectl get services -n web-app
```

Output:

```
NAME            TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
nginx-service   ClusterIP   10.110.9.22   <none>        80/TCP    10m
```

### Horizontal Pod Autoscaler

```bash
kubectl get hpa -n web-app
```

### Resource Quotas

```bash
kubectl get resourcequota -n web-app
```

### Get All Resources

```bash
kubectl get all -n web-app
```

Example includes pods, services, deployments, replica sets, and HPA.

---

## 📘 Summary

This deployment demonstrates:

* Namespace creation
* NGINX deployment & service setup
* Manual & automatic scaling with HPA
* Rolling updates and rollbacks
* Resource quotas enforcement

All resources are isolated inside the **web-app** namespace.

---

**End of README.md**

