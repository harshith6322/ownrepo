
---

# 📘 YAML Notes for DevOps

### 1. Basics

* YAML = "YAML Ain’t Markup Language"
* Human-readable data format (alternative to JSON, INI, XML).
* **Indentation matters** → spaces only, no tabs.
* File usually ends with `.yaml` or `.yml`.

```yaml
# comment starts with #
name: Harshith
age: 23
```

---

### 2. Key–Value Pairs

```yaml
key: value
version: "1.0"   # strings can be quoted or not
enabled: true    # booleans → true/false (lowercase)
count: 5         # numbers → no quotes
```

---

### 3. Lists (arrays)

```yaml
fruits:
  - apple
  - banana
  - mango

fruits: [apple,banana,mango]
```

Equivalent JSON:

```json
{"fruits": ["apple", "banana", "mango"]}
```

---

### 4. Dictionaries (maps)

```yaml
database:
  host: localhost
  port: 5432
  username: admin
```

---

### 5. Nested Structures

```yaml
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
  db:
    image: postgres:14
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
```

---

### 6. Multi-line Strings

```yaml
description: |
  This is a multi-line string.
  All new lines are preserved.
  
notes: >
  This is folded style.
  Newlines become spaces unless there’s a blank line.
```

---

### 7. Anchors & Aliases (DRY principle)

```yaml
default: &base
  image: node:18
  restart: always

service1:
  <<: *base
  container_name: app1

service2:
  <<: *base
  container_name: app2
```

---

### 8. Null, Boolean, Numbers

```yaml
nullable: null     # or ~
isReady: false
count: 100
pi: 3.1415
```

---

### 9. Environment Variables

Common in Docker Compose / K8s:

```yaml
env:
  - name: DB_USER
    value: admin
  - name: DB_PASS
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: password
```

---

### 10. Indentation Rules

✅ Correct:

```yaml
app:
  name: myapp
  version: 1.0
```

❌ Wrong (mixing spaces/tabs or bad indent):

```yaml
app:
   name: myapp
 version: 1.0
```

---

# ⚡ Quick DevOps Examples

### GitHub Actions

```yaml
name: CI Pipeline
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: npm test
```

### Docker Compose

```yaml
version: "3.9"
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: example
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: myorg/myapp:latest
          ports:
            - containerPort: 8080
```

---

✅ **Quick mnemonic to remember YAML basics:**

* **I**ndentation
* **K**eys & values
* **L**ists with `-`
* **S**tructures (nested maps, env vars, anchors)

👉 Think **"IKLS"** = Indentation, Keys, Lists, Structures.

---

