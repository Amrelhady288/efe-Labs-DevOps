# Docker Network Configuration and Service Lab

## **Lab Objectives**

* Custom Bridge Network Creation
* IP Address Allocation and Management
* Service Discovery and Internal Connectivity
* Multi-Network Container Setup
* Network Isolation and Security Validation

---

# ## **Task 1: Custom Bridge Network Setup**

### **Requirements**

* Create custom bridge network
* Verify configuration
* Run containers (nginx + alpine)
* Verify IP addresses
* Test service discovery

---

# ### **1. Create Custom Bridge Network**

```bash
docker network create \
  --driver bridge \
  --subnet 192.168.20.0/24 \
  --gateway 192.168.20.1 \
  hr-app-net
```

**Output example:**

```
5e4170bd30ba9a2cefd6d5624bcf7aff30f23c3230b8446bf8783a4bb6604f6a
```

---

### **2. Verify Network Configuration**

```bash
docker network inspect hr-app-net
```

Or filter subnet & gateway:

```bash
docker network inspect hr-app-net | egrep "Subnet|Gateway"
```

Expected output:

```
"Subnet": "192.168.20.0/24",
"Gateway": "192.168.20.1"
```

---

### **3. Run the Containers**

#### **Run nginx container:**

```bash
docker run -d --name nginx-server --network hr-app-net nginx
```

#### **Run alpine tester:**

```bash
docker run -it --name alpine-tester --network hr-app-net alpine sh
```

> Keep the alpine terminal open.

---

### **4. Verify IP Addresses**

#### **Check nginx-server:**

```bash
docker inspect nginx-server | egrep "Gateway|IPAddress"
```

Expected:

```
"Gateway": "192.168.20.1"
"IPAddress": "192.168.20.2"
```

#### **Check alpine-tester:**

```bash
docker inspect alpine-tester | egrep "Gateway|IPAddress"
```

Expected:

```
"Gateway": "192.168.20.1"
"IPAddress": "192.168.20.3"
```

---

### **5. Test Service Discovery**

From inside alpine:

```bash
ping -c 4 nginx-server
```

Expected:

```
4 packets transmitted, 4 received, 0% packet loss
```

---

### **Cleanup (Task 1)**

```bash
docker rm -f nginx-server alpine-tester
docker network rm hr-app-net
```

---

---

# ## **Task 2: Multi-Network & Network Isolation Lab**

### **Requirements**

* Create two networks (frontend + backend)
* Run multi-network container (nginx-lb)
* Test isolation
* Validate routing through load balancer container

---

# ### **1. Create Network 1 (Frontend)**

```bash
docker network create \
  --driver bridge \
  --subnet 10.1.1.0/24 \
  frontend-net
```

Output example:

```
532acdb88a63bae52b1194713b28af5ccdd948b2e55426e1cdf4b3cc5b0e09f4
```

---

### **2. Create Network 2 (Backend)**

```bash
docker network create \
  --driver bridge \
  --subnet 10.1.2.0/24 \
  backend-net
```

Output example:

```
a8a40832faa273a2004d80ee83acca0bedc150c4624cc181c9cb945ff0421ad4
```

---

## **3. Containers Configuration and Running**

### **Run backend-db:**

```bash
docker run -it --name backend-db --network backend-net alpine sh
```

### **Run client-tester:**

```bash
docker run -it --name client-tester --network frontend-net alpine sh
```

### **Run nginx-lb (connected to both networks):**

```bash
docker run -d --name nginx-lb --network frontend-net --network backend-net nginx
```

Output example:

```
3633d39c62ef573fa92d2c52d1b789e07cc088e120bec99d83471a0c6a0ef93b
```

---

### **4. Verify IP Allocation**

```bash
docker inspect nginx-lb | egrep "Gateway|IPAddress"
```

Expected:

```
Gateway: 10.1.2.1
IPAddress: 10.1.2.3
Gateway: 10.1.1.1
IPAddress: 10.1.1.3
```

---

## **5. Test Network Isolation and Routing**

### **Backend cannot reach frontend directly:**

```
ping -c 4 frontend-net
ping: bad address 'frontend-net'
```

### **From nginx-lb (must install ping):**

```bash
docker exec -it nginx-lb sh
apt update
apt install iputils-ping
```

#### **Ping backend-db:**

```bash
ping -c 4 backend-db
```

#### **Ping client-tester:**

```bash
ping -c 4 client-tester
```

Both should succeed.

---

### **Cleanup (Task 2)**

```bash
docker rm -f nginx-lb backend-db client-tester
docker network rm frontend-net backend-net
```

---

# ✅ **Lab Completed Successfully**
