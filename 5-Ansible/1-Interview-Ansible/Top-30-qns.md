Here you go Harshith — **30 most common Ansible interview questions with clean, short, interview-ready answers**.

---

# ✅ **Top 30 Ansible Interview Questions & Answers (DevOps Focus)**

---

## **1. What is Ansible?**

Ansible is an **agentless configuration management, automation, and orchestration tool** that uses SSH to manage remote servers. Playbooks (YAML) define tasks.

---

## **2. Why is Ansible agentless?**

It uses **SSH (Linux)** and **WinRM (Windows)**—no agent installation required. This makes Ansible lightweight, secure, and easy to maintain.

---

## **3. What is an Inventory in Ansible?**

Inventory is a list of hosts to automate.
Example:

```
[web]
192.168.1.10
```

---

## **4. What are Playbooks?**

Playbooks are **YAML** files that define tasks executed on remote hosts.

---

## **5. What is a Task in Ansible?**

A task is a single action (like install package, copy file). A play contains multiple tasks.

---

## **6. What are Modules in Ansible?**

Modules are reusable units that perform tasks (e.g., `yum`, `apt`, `copy`, `service`, `user`).
Almost everything in Ansible uses modules.

---

## **7. What is Idempotency?**

Running the same playbook multiple times results in **no changes if the system is already in the correct state**.
Most Ansible modules are idempotent.

---

## **8. What are Roles?**

Roles allow you to structure playbooks into reusable components (tasks, handlers, vars, templates, files).

---

## **9. What are Handlers used for?**

Handlers run **only when notified** by a task.
Used for services restart/reload.

---

## **10. How do you run an ad-hoc command?**

Example:

```
ansible all -m ping
```

---

## **11. What is the difference between vars, vars_files, vars_prompt?**

* `vars`: define variables inside playbook
* `vars_files`: load variables from external files
* `vars_prompt`: ask user to input value during runtime

---

## **12. How to encrypt sensitive data in Ansible?**

Using **Ansible Vault**:

```
ansible-vault encrypt file.yml
```

---

## **13. How do you execute a playbook with vault password?**

```
ansible-playbook site.yml --ask-vault-pass
```

---

## **14. What is the difference between templates and files?**

* **files/**: used to copy static files
* **templates/**: dynamic files using **Jinja2** variables (e.g., `nginx.conf.j2`)

---

## **15. What is “gather_facts”?**

It collects system information (OS, CPU, IP, RAM).
To disable facts:

```
gather_facts: no
```

---

## **16. How to test connectivity?**

```
ansible all -m ping
```

---

## **17. What is “become” in Ansible?**

Privilege escalation (sudo):

```
become: yes
```

---

## **18. What is the default location of inventory?**

`/etc/ansible/hosts`

---

## **19. How to define host groups?**

```
[webservers]
192.168.1.10
192.168.1.11
```

---

## **20. How do you run a playbook on a specific host group?**

```
ansible-playbook site.yml -l webservers
```

---

## **21. What is the purpose of "when" condition in Ansible?**

Conditional task execution:

```
when: ansible_os_family == "Debian"
```

---

## **22. What is “register”?**

Used to store command output in a variable.

Example:

```
register: result
```

---

## **23. What are tags in Ansible?**

Used to run only specific tasks:

```
ansible-playbook site.yml --tags setup
```

---

## **24. How to use loops?**

```
with_items:
  - nginx
  - httpd
```

---

## **25. What is a dynamic inventory?**

Inventory generated from cloud APIs (AWS, GCP, Azure) instead of static files.

---

## **26. Ansible Pull vs Ansible Push?**

* **Push (default)**: Control node pushes config to hosts.
* **Pull**: Hosts pull config from git repo (good for large scale).

---

## **27. What is “serial” in Ansible?**

Used to roll out changes in batches:

```
serial: 2
```

---

## **28. What is a "delegate_to"?**

Runs a task on a different host than the target host.

```
delegate_to: localhost
```

---

## **29. Difference between “copy” and “template” modules?**

* `copy` → copies static files
* `template` → copies Jinja2 templates with variables

---

## **30. How to check what Ansible will do without making changes?**

```
ansible-playbook site.yml --check
```

This is the **dry-run** mode.

---

If you want, I can also give you:

✅ **Ansible Architecture Explanation (Short for Interviews)**
✅ **Hands-on Ansible Scenarios Asked in Interviews**
✅ **Create your own Ansible cheat sheet PDF**

Just tell me!
