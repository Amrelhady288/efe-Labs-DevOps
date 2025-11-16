# Kubernetes Lab: Multi-Namespace Deployment with Resource Quotas

This README documents all commands and outputs used to create namespaces, deploy NGINX and MySQL, enforce ResourceQuotas, scale deployments, and clean up all resources.

---

## 🚀 Create `web-app` Namespace

```bash
kubectl apply -f webapp-namespace.yaml
kubectl get namespaces
```

**Output:**

```
namespace/web-app created
```

---

## 🌐 Deploy NGINX Deployment & Service

```bash
kubectl apply -f nginx-deployment.yaml -f nginx-service.yaml
kubectl get pods -n web-app
```

Expected running pods:

```
nginx-deployment-xxxxx   1/1 Running
```

---

## 🌍 Test Connectivity

### Get NodePort

```bash
kubectl get service nginx-service -n web-app
```

### Get Node IP

```bash
kubectl get nodes -o wide
```

### Test access

```bash
curl http://<NODE-IP>:<NODE-PORT>/
```

You should see the default **NGINX Welcome page**.

### Minikube Service URL

```bash
minikube service nginx-service -n web-app
```

---

## 📈 Scale Deployment

### Scale to 3 replicas

```bash
kubectl scale deployment nginx-deployment -n web-app --replicas=3
kubectl get pods -n web-app
```

### Scale to 4 replicas

```bash
kubectl scale deployment nginx-deployment -n web-app --replicas=4
kubectl get pods -n web-app
```

---

## 🧱 Apply ResourceQuota

```bash
kubectl apply -f quota.yaml
kubectl describe resourcequota web-app-quota -n web-app
```

Example output:

```
limits.cpu: 800m/1
pods: 4/5
requests.cpu: 400m/500m
```

---

## 🛑 Test Quota Enforcement

Try to scale beyond quota:

```bash
kubectl scale deployment nginx-deployment -n web-app --replicas=15
kubectl get pods -n web-app
kubectl describe resourcequota web-app-quota -n web-app
```

Only **5 pods** will be allowed due to ResourceQuota.

---

## ✔ Verify All Resources

```bash
kubectl get all -n web-app
kubectl get resourcequota -n web-app
kubectl describe namespace web-app
```

---

# 🗄 Create `db-app` Namespace

```bash
kubectl apply -f db-namespace.yaml
kubectl get namespaces
```

---

# 🛢 Deploy MySQL

```bash
kubectl apply -f mysql-deployment.yaml
kubectl get pods -n db-app
```

Check deployment:

```bash
kubectl get deployments -n db-app
```

---

## 🔐 Verify MySQL Environment Variables

### From deployment YAML

```bash
kubectl get deployment mysql-deployment -n db-app -o yaml
```

### Inside the pod

```bash
kubectl exec -it $(kubectl get pod -n db-app -l app=mysql -o jsonpath='{.items[0].metadata.name}') -n db-app -- env | grep MYSQL
```

Example output:

```
MYSQL_ROOT_PASSWORD=rootpassword
MYSQL_DATABASE=mydb
```

---

# 🧹 Cleanup All Resources

```bash
kubectl delete -f db-namespace.yaml -f mysql-deployment.yaml -f nginx-deployment.yaml -f nginx-service.yaml -f quota.yaml -f webapp-namespace.yaml
```

Output:

```
namespace "db-app" deleted
namespace "web-app" deleted
```

---

## ✅ Lab Completed Successfully

This README contains all steps, commands, and expected outputs for setting up a multi-namespace Kubernetes environment with web and database workloads, resource quotas, and scaling tests.
