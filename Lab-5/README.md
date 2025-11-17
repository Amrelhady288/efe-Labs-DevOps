# Lab 5 - Working with ConfigMaps and Secrets

This README documents the full process of deploying a MySQL database on Kubernetes using:

* ConfigMaps
* Secrets
* Persistent Volumes
* Taints & Tolerations
* Best‑practice deployment patterns

---

## 🧱 1. Apply Node Taint

Ensure MySQL pods only schedule on the tainted node.

```bash
kubectl taint nodes minikube db=only:NoSchedule --overwrite
```

---

## 🗂️ 2. Create ConfigMap & Secret

### Create ConfigMap from file

```bash
kubectl create configmap mysql-config --from-file=config.txt
```

### Create Secret from environment file

```bash
kubectl create secret generic mysql-secret --from-env-file=secret.txt
```

---

## 💾 3. Persistent Storage

### Create PersistentVolume

```bash
kubectl apply -f mysql-pv.yaml
```

### Create PersistentVolumeClaim

```bash
kubectl apply -f mysql-pvc.yaml
```

---

## 🐬 4. Deploy MySQL

```bash
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml
```

---

## ✅ 5. Verify Resources

### Pods

```bash
kubectl get pods
```

### ConfigMap

```bash
kubectl get configmap mysql-config
kubectl describe configmap mysql-config
```

### Secret

```bash
kubectl get secret mysql-secret
kubectl describe secret mysql-secret
```

### PV & PVC

```bash
kubectl get pv,pvc
```

### Service

```bash
kubectl get service mysql-service
```

### Node Taint Verification

```bash
kubectl describe node minikube | grep -A 5 "Taints:"
```

### Pod Scheduling & Node Assignment

```bash
kubectl get pods -l app=mysql -o wide
```

### Verify MySQL Environment Variables

```bash
kubectl exec -it <mysql-pod> -- env | grep MYSQL
```

### Verify Pod Tolerations

```bash
kubectl describe pod <mysql-pod> | grep -A 10 "Tolerations:"
```

---

## 🧪 6. Test Database Functionality

Run SQL commands inside the MySQL pod:

```bash
kubectl exec -it <mysql-pod> -- mysql -u testuser -puserpassword123 testdb -e "
CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(50));
INSERT INTO users (name) VALUES ('John Doe'), ('Jane Smith');
SELECT * FROM users;"
```

Expected output:

```
+----+------------+
| id | name       |
+----+------------+
|  1 | John Doe   |
|  2 | Jane Smith |
+----+------------+
```

---

## 🧹 7. Cleanup

### Delete all manifest‑based resources

```bash
kubectl delete -f .
```

### Delete ConfigMap & Secret

```bash
kubectl delete configmap mysql-config
kubectl delete secret mysql-secret
```

---
