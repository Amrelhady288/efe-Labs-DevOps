# Lab 11 - ArgoCD Installation and Configuration

## Task 1 – Install ArgoCD

### Step 1: Create ArgoCD Namespace

```bash
kubectl create namespace argocd
```

### Step 2: Install ArgoCD

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Step 3: Wait for Pods to be Ready

```bash
kubectl get pods -n argocd
```

Wait until all pods show **Running** status (may take 2–3 minutes).

---

## Task 2 – Access ArgoCD Web UI

### Step 1: Get Admin Password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

### Step 2: Set Up Port Forwarding

```bash
kubectl port-forward svc/argocd-server -n argocd 9090:443
```

Keep this terminal open.

### Step 3: Access Web UI

Open browser and go to:

```
https://localhost:9090
```

Accept the SSL warning.

Login with:

```
Username: admin
Password: (output from Step 1)
```

### Troubleshooting Login Issues

If you get **Invalid username or password**, reset the password:

```bash
kubectl -n argocd patch secret argocd-secret -p '{"stringData": {"admin.password": "$2a$10$rRyBsGSHK6.uc8fntPwVIuLVHgsAhAX7TcdrqW/RADU0uh7CaChLa","admin.passwordMtime": "'$(date +%FT%T%Z)'"}}'
```

Restart ArgoCD server:

```bash
kubectl rollout restart deployment/argocd-server -n argocd
kubectl rollout status deployment/argocd-server -n argocd
```

Use:

```
Username: admin
Password: password
```

---

## Task 3 – Create an Application (GUI)

### Step 1: Open ArgoCD Dashboard

Go to:

```
https://localhost:9090
```

Login with admin credentials.

### Step 2: Create New Application

Click **NEW APP** → Fill the form:

#### General Settings

* **Application Name:** nginx-app
* **Project:** default
* **Sync Policy:** Manual

#### Source Settings

* **Repository URL:** [https://github.com/abdelrahmanonline4/lab-seniorLab](https://github.com/abdelrahmanonline4/lab-seniorLab)
* **Revision:** HEAD
* **Path:** .

#### Destination Settings

* **Cluster URL:** [https://kubernetes.default.svc](https://kubernetes.default.svc)
* **Namespace:** default

### Step 3: Create the Application

Click **CREATE**.
Application will appear with **OutOfSync** status.

---

## Task 4 – Sync the Application

### Step 1: Select Application

Click **nginx-app**.

### Step 2: Review Resources

Status should show **OutOfSync**.

### Step 3: Sync

Click **SYNC** → **SYNCHRONIZE**.

### Step 4: Monitor Sync

Wait until all resources are deployed.

---

## Task 5 – Verify Deployment

### Step 1: GUI Check

* Application status should be **Synced** and **Healthy**
* All resources in green

### Step 2: View Resources

Check Deployment, Service, and Pods.

### Step 3: CLI Verification

```bash
kubectl get all -n default
kubectl get deployment nginx -n default
kubectl get service nginx -n default
```

### Step 4: Access NGINX Service

```bash
kubectl port-forward service/nginx 8080:80 -n default
```

Visit:

```
http://localhost:8080
```

---

## Verification Commands

### ArgoCD Status

```bash
kubectl get pods -n argocd
kubectl get svc -n argocd
```

### Applications

```bash
kubectl get applications -n argocd
```

### Kubernetes Resources

```bash
kubectl get all -n default
```

### ArgoCD Logs

```bash
kubectl logs -n argocd deployment/argocd-server --tail=20
```

---

## Troubleshooting

### ArgoCD Pods Not Starting

```bash
kubectl describe pods -n argocd
kubectl logs -n argocd <pod-name>
```

### Login Issues

* Ensure all pods are running
* Restart ArgoCD server
* Reset admin password if needed

### Application Sync Fails

* Check repo URL
* Ensure valid Kubernetes manifests exist in the path

### NGINX Not Accessible

```bash
kubectl get pods -n default
kubectl describe service nginx -n default
kubectl logs <nginx-pod-name> -n default
```
