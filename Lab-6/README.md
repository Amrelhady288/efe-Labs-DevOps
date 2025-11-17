# Lab 6 - Working with Ansible AD-HOC Commands and Playbooks

This lab demonstrates how to use **Ansible ad-hoc commands** and **Ansible playbooks** to automate package management, service control, gathering system information, and running commands across multiple nodes.

---

## **Machine Information**

Retrieve active Vagrant machines:

```
$ vagrant global-status
```

Example output:

```
id       name    provider   state   directory                                 
-----------------------------------------------------------------------------
c4eed3b  master  virtualbox running /home/.../k8s-on-premises
02fb6d1  worker1 virtualbox running /home/.../k8s-on-premises
a9305a2  worker2 virtualbox running /home/.../k8s-on-premises
```

Get SSH configuration for a node:

```
$ vagrant ssh-config a9305a2
```

---

# Part 1 - Ansible AD-HOC Commands

## **1. Check Connectivity**

```
$ ansible all -m ping
```

Expected output: all nodes return **pong**.

---

## **2. Install Package (nginx)**

```
$ ansible all -m package -a "name=nginx state=present" --become
```

---

## **3. Verify Installation**

```
$ ansible all -m command -a "nginx -v"
```

Each node prints the installed nginx version.

---

## **4. Remove nginx**

```
$ ansible all -m package -a "name=nginx state=absent" --become
```

---

# Part 2 - Playbook with Variables and Loops

Run playbook:

```
$ ansible-playbook playbook.yml
```

### **Playbook tasks include:**

* Removing existing web package
* Installing tools: git, curl, wget, vim
* Installing nginx
* Starting and enabling nginx service
* Checking nginx version
* Displaying captured version output

Play recap example:

```
master  : ok=7 changed=2
worker1 : ok=7 changed=2
worker2 : ok=7 changed=2
```

---

## **Verify Service Status**

```
$ ansible all -m service -a "name=nginx state=started" --become
```

---

## **Check Installed Packages**

```
$ ansible all -m package -a "name=nginx state=present"
```

---

## **Test Web Server Functionality**

```
$ ansible all -m uri -a "url=http://localhost method=GET"
```

Should return **status: 200** with Nginx default HTML page content.

---

# Cleanup

Stop nginx:

```
$ ansible all -m service -a "name=nginx state=stopped" --become
```

Remove nginx:

```
$ ansible all -m package -a "name=nginx state=absent" --become
```

Remove installed tools:

```
$ ansible all -m package -a "name=git,curl,wget,vim state=absent" --become
```

---
