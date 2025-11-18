# Ansible Lab 07 – Apache Web Server Role

## 📌 Overview

This lab demonstrates how to create and use an **Ansible Role** to deploy and configure an Apache Web Server automatically. The lab includes tasks, handlers, templates, and a main playbook.

---

## 🧱 Project Structure

After creating the role, your directory structure should look like:

```
roles/apache/
├── tasks/
│   └── main.yml
├── handlers/
│   └── main.yml
├── templates/
│   └── index.html.j2
├── vars/
│   └── main.yml
└── ...
site.yml
inventory
```

---

## 🚀 Step 1 — Create Role Skeleton

Run the following command inside your Ansible project directory:

```bash
cd ~/Desktop/ansible
ansible-galaxy init roles/apache
```

---

## 🧩 Step 2 — Define Main Tasks

Edit the file:
`roles/apache/tasks/main.yml`

```yaml
---
- name: Install Apache package
  apt:
    name: apache2
    state: present
    update_cache: yes
  when: ansible_os_family == "Debian"

- name: Install Apache (RHEL/CentOS)
  yum:
    name: httpd
    state: present
  when: ansible_os_family == "RedHat"

- name: Deploy index.html from template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
  notify: restart apache
```

---

## 🔄 Step 3 — Define Handler

Edit:
`roles/apache/handlers/main.yml`

```yaml
---
- name: restart apache
  service:
    name: "{{ apache_service }}"
    state: restarted
```

---

## 📝 Step 4 — Create Template

File:
`roles/apache/templates/index.html.j2`

```html
<html>
<head>
<title>Welcome Page</title>
</head>
<body>
<h1>Welcome to {{ ansible_hostname }} web server!</h1>
<p>Deployed automatically using Ansible Role: Apache</p>
</body>
</html>
```

---

## ⚙️ Step 5 — Set Variables (Recommended)

File:
`roles/apache/vars/main.yml`

```yaml
---
apache_service: "{{ 'apache2' if ansible_os_family == 'Debian' else 'httpd' }}"
```

---

## ▶️ Step 6 — Main Playbook

Create `site.yml`:

```yaml
---
- name: Deploy Apache Web Server
  hosts: webservers
  become: yes
  roles:
    - apache
```

---

## 🗂️ Step 7 — Inventory File Example

Create an `inventory` file:

```
[webservers]
192.168.56.101
192.168.56.102
```

---

## 🚀 Step 8 — Execute the Playbook

Run:

```bash
ansible-playbook -i inventory site.yml
```

---

## ✔️ Verification

On the managed node, or from your machine run:

```bash
curl http://<server-ip>
```

You should see the custom page deployed using the Ansible role.

---
