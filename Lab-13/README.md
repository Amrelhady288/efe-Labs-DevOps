# OpenShift Lab - Deploying NGINX with HTTPS Route

## 1. Create New Project

Create a new OpenShift project named **nginx-ozil**:

```bash
oc new-project nginx-ozil
oc project
```

---

## 2. Apply Security Policy (SCC anyuid)

Grant the *anyuid* SCC to the default service account in the project:

```bash
oc adm policy add-scc-to-user anyuid -z default -n nginx-ozil
```

Expected output:

```
clusterrole.rbac.authorization.k8s.io/system:openshift:scc:anyuid added: "default"
```

Verify SCC:

```bash
oc describe scc anyuid | grep Users
```

---

## 3. Deploy NGINX Application

Create a new NGINX application:

```bash
oc new-app --name=nginx-app --docker-image=nginx:latest
```

> Note: `--docker-image` is deprecated; use `--image` in future.

Check running pods:

```bash
oc get pods -n nginx-ozil
```

Example output:

```
nginx-app-84fd578d56-sp9nl   1/1   Running   0   46s
```

Check the created service:

```bash
oc get services
```

---

## 4. Create HTTPS Route (Edge Termination)

Expose the application using an edge-terminated HTTPS route:

```bash
oc create route edge nginx-https-route --service=nginx-app --port=80
```

Verify route:

```bash
oc get routes
```

Example output:

```
nginx-https-route   nginx-https-route-nginx-ozil.apps-crc.testing   nginx-app   80   edge
```

Describe route:

```bash
oc describe route nginx-https-route
```

---

## 5. Verify Resources

List all resources:

```bash
oc get all
```

Check pods:

```bash
oc get pods -o wide
```

Check services:

```bash
oc get services -o wide
```

Check routes:

```bash
oc get routes -o wide
```

Check deployments:

```bash
oc get deployments -o wide
```

Cluster status:

```bash
oc status
```

---

## 6. Test Application Access

Test HTTPS access using curl:

```bash
curl -k https://nginx-https-route-nginx-ozil.apps-crc.testing
```

You should receive the default **Welcome to nginx!** page.

---

## 7. View Pod Logs

Check logs of the NGINX pod:

```bash
oc logs nginx-app-84fd578d56-sp9nl -n nginx-ozil
```

Expected output includes NGINX startup messages and requests served.

---

## 8. Verify Pods Again

```bash
oc get pods -n nginx-ozil
```

Output:

```
nginx-app-84fd578d56-sp9nl   1/1   Running   0   19m
```

---

## Summary

You successfully:

* Created an OpenShift project
* Applied anyuid SCC
* Deployed an NGINX application
* Exposed it with an **HTTPS edge route**
* Verified access and logs

This completes the OpenShift NGINX HTTPS deployment lab.
