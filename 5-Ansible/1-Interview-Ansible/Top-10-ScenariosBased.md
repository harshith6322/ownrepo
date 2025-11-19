Here you go Harshith — **Top 10 Hands-On / Practical Ansible Scenarios** that **almost every DevOps interview** asks.
Each scenario includes the **question + exact answer + YAML example** (short & interview ready).

---

# 🚀 **Top 10 Hands-On Ansible Scenarios for DevOps Interviews**

---

## **1️⃣ Write a playbook to install Nginx and start the service**

**Asked in 80% interviews**

### **Answer (Short)**

Use `apt`/`yum` module + service module + idempotency.

### **YAML**

```yaml
- hosts: web
  become: yes
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

    - name: Start nginx
      service:
        name: nginx
        state: started
        enabled: yes
```

---

## **2️⃣ Copy a template file with dynamic variables**

**Tests Jinja2 template usage.**

### **Answer**

Use `template` module and variables in `.j2`.

### **YAML**

```yaml
- hosts: app
  tasks:
    - name: Deploy config with variables
      template:
        src: app.conf.j2
        dest: /etc/app.conf
```

**Template example (`app.conf.j2`):**

```
port={{ app_port }}
env={{ app_env }}
```

---

## **3️⃣ Create a user and add SSH key**

### **Answer**

Use `user` and `authorized_key`.

```yaml
- hosts: all
  become: yes
  tasks:
    - name: Create dev user
      user:
        name: devuser
        shell: /bin/bash

    - name: Add SSH key
      authorized_key:
        user: devuser
        key: "{{ lookup('file', 'id_rsa.pub') }}"
```

---

## **4️⃣ Deploy a web application using roles**

### **Answer**

Use directory structure + `roles:` keyword.

### **playbook.yml**

```yaml
- hosts: web
  roles:
    - nginx
    - deploy_app
```

---

## **5️⃣ Run a task only when OS type is Ubuntu**

### **Answer**

Use a condition with `ansible_os_family`.

```yaml
- hosts: all
  tasks:
    - name: Install apache on Ubuntu only
      apt:
        name: apache2
        state: present
      when: ansible_os_family == "Debian"
```

---

## **6️⃣ Register output of a command and use it**

### **Answer**

Use `register` + debug.

```yaml
- hosts: all
  tasks:
    - name: Check disk usage
      command: df -h
      register: disk

    - debug:
        var: disk.stdout
```

---

## **7️⃣ Write a playbook to restart service only when config changes**

**Tests handlers.**

```yaml
- hosts: web
  tasks:
    - name: Copy nginx config
      copy:
        src: nginx.conf
        dest: /etc/nginx/nginx.conf
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted
```

---

## **8️⃣ Use loops to install multiple packages**

### **Answer**

Use `loop:` or `with_items:`.

```yaml
- hosts: all
  become: yes
  tasks:
    - name: Install packages
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - curl
        - tree
```

---

## **9️⃣ Rolling deployment – update servers in batches**

### **Answer**

Use `serial`.

```yaml
- hosts: web
  serial: 2
  tasks:
    - name: Update package
      apt:
        name: myapp
        state: latest
```

---

## **🔟 Use Ansible Vault to store DB password & use inside playbook**

### **Answer**

Encrypt:

```
ansible-vault encrypt secrets.yml
```

### **playbook.yml**

```yaml
- hosts: db
  vars_files:
    - secrets.yml
  tasks:
    - name: Configure DB
      template:
        src: db.cnf.j2
        dest: /etc/db.cnf
```

### **secrets.yml**

```
db_password: mysecret
```

---

# 🎯 Want More?

I can also give you:

✅ **Top 10 Mini-Projects using Ansible (For Resume)**
✅ **Most tricky Ansible interview questions**
✅ **One-page Ansible cheat sheet PDF**

Just tell me which one you want!
