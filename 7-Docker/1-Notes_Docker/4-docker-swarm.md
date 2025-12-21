Perfect 👍
Below is a **clean, complete, interview-ready cheat sheet** of **Docker Swarm commands**, including **service, stack, networking, scaling, rollout, debugging, and troubleshooting** — mapped to **what you actually did today**.

You can **save this as notes**.

---

# 🐳 Docker Swarm – Complete Command Cheat Sheet (DevOps)

---

## 🔹 1. Docker & Swarm Setup

```bash
yum install docker -y
systemctl start docker
systemctl enable docker
```

Initialize Swarm (Manager):

```txt
Manage Swarm

Commands:
  ca          Display and rotate the root CA
  init        Initialize a swarm
  join        Join a swarm as a node and/or manager
  join-token  Manage join tokens
  leave       Leave the swarm
  unlock      Unlock swarm
  unlock-key  Manage the unlock key
  update      Update the swarm
```

```bash
docker swarm init
```

---

### Get join tokens

```bash
docker swarm join-token worker
docker swarm join-token manager
docker swarm join --token SWMTKN-1-xxxx 192.168.1.10:2377
##To rotate tokens (security)
docker swarm join-token --rotate worker
#Leave swarm
docker swarm leave
docker swarm leave --force

```

## 2. node

```txt
Commands:
 demote      Demote one or more nodes from manager in the swarm
 inspect     Display detailed information on one or more nodes
 ls          List nodes in the swarm
 promote     Promote one or more nodes to manager in the swarm
 ps          List tasks running on one or more nodes, defaults to current node
 rm          Remove one or more nodes from the swarm
 update      Update a node

```

```bash
docker node ls
docker node rm <id>
docker node inspect <id>
docker node promte <id>
docker node demote <id>
docker node ps
docker node update
```

---

## 🔹 3. Docker Service (Core of Swarm)

```txt
Commands:
  create      Create a new service
  inspect     Display detailed information on one or more services
  logs        Fetch the logs of a service or task
  ls          List services
  ps          List the tasks of one or more services
  rm          Remove one or more services
  rollback    Revert changes to a service's configuration
  scale       Scale one or multiple replicated services
  update      Update a service

```

### Create service

```bash
docker service create --name flm --publish 1111:80 shaikmustafa/dm
```

### Create service with replicas

```bash
docker service create \
--name mustafa \
--replicas 6 \
--publish 2222:80 \
shaikmustafa/cycle
```

### List services

```bash
docker service ls
```

### Inspect service

```bash
docker service inspect mustafa
```

### Service logs

```bash
docker service logs mustafa
```

---

## 🔹 4. Tasks & Containers

### See service tasks

```bash
docker service ps mustafa
```

### See running containers (on node)

```bash
docker ps
```

### Inspect a task container

```bash
docker inspect mustafa.1.t1luzubplndki883ttfiz7pzt
```

### Check published ports

```bash
docker inspect mustafa.1.xxx | grep -i port
docker inspect mustafa.1.xxx | grep -i hostport
```

---

## 🔹 5. Scaling Services

Scale UP:

```bash
docker service scale mustafa=10
```

Scale DOWN:

```bash
docker service scale mustafa=3
```

Check scaling result:

```bash
docker service ps mustafa
```

---

## 🔹 6. Rolling Updates & Rollback

Update image:

```bash
docker service update --image shaikmustafa/dm mustafa   <newimage> <servicename>
```

Update again:

```bash
docker service update --image shaikmustafa/mygame mustafa
```

Rollback:

```bash
docker service rollback mustafa
```

Check status:

```bash
docker service ps mustafa
```

---

## 🔹 7. Remove Services

```bash
docker service rm flm
docker service rm mustafa
```

---

## 🔹 8. Docker Stack (Compose in Swarm)

```txt
Commands:
  config      Outputs the final config file, after doing merges and interpolations
  deploy      Deploy a new stack or update an existing stack
  ls          List stacks
  ps          List the tasks in the stack
  rm          Remove one or more stacks
  services    List the services in the stack

```

### Deploy stack

```bash
docker stack deploy -c docker-compose.yml my_stack
```

### List stacks

```bash
docker stack ls
```

### List stack services

```bash
docker stack services my_stack
```

### List stack tasks

```bash
docker stack ps my_stack
```

### Remove stack

```bash
docker stack rm my_stack
```

---

## 🔹 9. Networking in Swarm

### List networks

```bash
docker network ls
```

### Inspect overlay network

```bash
docker network inspect my_overlay_net
```

### Create overlay network

```bash
docker network create -d overlay my_overlay
```

💡 Swarm provides **built-in internal load balancing (VIP + DNS)**

---

## 🔹 10. Node Management

### List nodes

```bash
docker node ls
```

### Inspect node

```bash
docker node inspect <node-id>
```

### Drain node (maintenance)

```bash
docker node update --availability drain <node-id>
```

### Activate node

```bash
docker node update --availability active <node-id>
```

### Remove node

```bash
docker node rm <node-id>
```

---

## 🔹 11. Debugging & Troubleshooting (VERY IMPORTANT)

### Check service not running

```bash
docker service ps mustafa
```

### Check task errors

```bash
docker inspect mustafa.1.xxx
```

### Logs not showing?

```bash
docker service logs mustafa --tail 50
```

### Check swarm status

```bash
docker info | grep Swarm
```

### Check node reachability

```bash
docker node ls
```

### Check overlay issues

```bash
iptables -L
```

---

## 🔹 12. Common Swarm Debug Scenarios

### ❌ Worker not joining

- Port 2377 blocked
- Manager IP wrong
- Docker version mismatch

### ❌ Service not accessible

- Published port wrong
- Node firewall blocked
- Using wrong node IP

### ❌ Tasks restarting

- App crashing
- Healthcheck failing
- Image error

---

## 🔹 13. Important Swarm Concepts (Interview Gold)

- Swarm has **built-in load balancing**
- Uses **routing mesh**
- No auto-scaling (manual only)
- No ALB like ECS
- Reverse proxy (NGINX/Traefik) must be managed by you
- Good for learning, **not cloud-native production**

---

## 🔹 14. Swarm vs ECS (Quick)

| Feature         | Swarm | ECS |
| --------------- | ----- | --- |
| Auto scaling    | ❌    | ✅  |
| ALB integration | ❌    | ✅  |
| Cloud native    | ❌    | ✅  |
| Learning        | ✅    | ⚠️  |
| Production      | ❌    | ✅  |

---

## 🧠 One-Line Interview Answer

> Docker Swarm provides container orchestration with built-in service discovery and load balancing, but lacks auto-scaling and cloud-native integrations like AWS ECS.

---

If you want next (highly recommended):

- **Swarm vs ECS deep architecture**
- **Swarm routing mesh demo**
- **ECS ALB + Auto Scaling hands-on**
- **Convert Swarm app → ECS**

Just say 👍
