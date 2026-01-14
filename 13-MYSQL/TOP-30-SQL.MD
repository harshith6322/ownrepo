Perfect 👍
Below is a **curated Top 30 MySQL 5.7 commands & SQL queries every DevOps engineer MUST know**, especially for **master–read replica, backups, Docker, and production ops**.

I’ve grouped them so they’re **easy to memorize for interviews** and **useful on-call**.

---

## 🔰 1. Connection & Server Health (1–5)

### 1️⃣ Connect locally

```bash
mysql -u root -p
```

### 2️⃣ Connect to remote MySQL

```bash
mysql -h 10.0.0.10 -P 3306 -u dbuser -p
```

### 3️⃣ Check MySQL version

```sql
SELECT VERSION();
```

### 4️⃣ Check server uptime

```sql
SHOW STATUS LIKE 'Uptime';
```

### 5️⃣ Check active connections

```sql
SHOW STATUS LIKE 'Threads_connected';
```

---

## 🗄️ 2. Database & Storage Info (6–10)

### 6️⃣ List databases

```sql
SHOW DATABASES;
```

### 7️⃣ Use database

```sql
USE mydb;
```

### 8️⃣ List tables

```sql
SHOW TABLES;
```

### 9️⃣ Check DB size (VERY COMMON)

```sql
SELECT
  table_schema AS db,
  ROUND(SUM(data_length + index_length)/1024/1024, 2) AS size_mb
FROM information_schema.tables
GROUP BY table_schema;
```

### 🔟 MySQL data directory

```sql
SHOW VARIABLES LIKE 'datadir';
```

---

## 👤 3. Users & Security (11–15)

### 1️⃣1️⃣ List MySQL users

```sql
SELECT user, host FROM mysql.user;
```

### 1️⃣2️⃣ Check user privileges

```sql
SHOW GRANTS FOR 'dbuser'@'%';
```

### 1️⃣3️⃣ Create read-only user (replica)

```sql
CREATE USER 'readonly'@'%' IDENTIFIED BY 'password';
GRANT SELECT ON mydb.* TO 'readonly'@'%';
```

### 1️⃣4️⃣ Backup user (best practice)

```sql
CREATE USER 'backup_user'@'%' IDENTIFIED BY 'password';
GRANT SELECT, LOCK TABLES, PROCESS, RELOAD ON *.* TO 'backup_user'@'%';
```

### 1️⃣5️⃣ Check authentication plugin

```sql
SELECT user, plugin FROM mysql.user;
```

---

## 🔄 4. Replication (MASTER–REPLICA) (16–20)

![Image](https://code.openark.org/blog/wp-content/uploads/2014/03/slave_status_explained_uptodate.png)

![Image](https://www.opsdash.com/static/images/mysql_replication_master.png)

![Image](https://www.sqlshack.com/wp-content/uploads/2021/03/output-of-mysqlbinlog-1.png)

### 1️⃣6️⃣ Check replica status (MOST IMPORTANT)

```sql
SHOW SLAVE STATUS\G;
```

Look for:

```text
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Seconds_Behind_Master: 0
```

### 1️⃣7️⃣ Check master binlog

```sql
SHOW MASTER STATUS;
```

### 1️⃣8️⃣ Start replication

```sql
START SLAVE;
```

### 1️⃣9️⃣ Stop replication

```sql
STOP SLAVE;
```

### 2️⃣0️⃣ Read-only mode (replica safety)

```sql
SHOW VARIABLES LIKE 'read_only';
```

---

## 💾 5. Backup & Restore (21–25) 🚨

### 2️⃣1️⃣ Backup single database

```bash
mysqldump -u root -p mydb > mydb.sql
```

### 2️⃣2️⃣ Backup all databases

```bash
mysqldump -u root -p --all-databases > alldb.sql
```

### 2️⃣3️⃣ Safe backup from replica (PROD)

```bash
mysqldump -u backup_user -p \
--single-transaction \
--set-gtid-purged=OFF \
mydb > mydb.sql
```

### 2️⃣4️⃣ Restore database

```bash
mysql -u root -p mydb < mydb.sql
```

### 2️⃣5️⃣ Backup from Docker MySQL

```bash
docker exec mysql-container mysqldump -u root -p mydb > mydb.sql
```

---

## 📊 6. Performance & Troubleshooting (26–30)

### 2️⃣6️⃣ Running queries

```sql
SHOW FULL PROCESSLIST;
```

### 2️⃣7️⃣ Kill a query (emergency)

```sql
KILL 123;
```

### 2️⃣8️⃣ Check slow query log

```sql
SHOW VARIABLES LIKE 'slow_query_log';
```

### 2️⃣9️⃣ Check max connections

```sql
SHOW VARIABLES LIKE 'max_connections';
```

### 3️⃣0️⃣ Disk usage per table

```sql
SELECT
  table_name,
  ROUND((data_length + index_length)/1024/1024, 2) AS size_mb
FROM information_schema.tables
WHERE table_schema = 'mydb'
ORDER BY size_mb DESC;
```

---

## 🎯 How to Use This for Interviews

Memorize **bold commands**:

- `SHOW SLAVE STATUS\G`
- `mysqldump --single-transaction`
- `SHOW MASTER STATUS`
- `SHOW FULL PROCESSLIST`

Interview killer line:

> “We always take backups from read replicas using `mysqldump --single-transaction` and monitor replication lag.”

---

## 🚀 Want More?

I can give you:

- **Top 20 MySQL interview questions (DevOps only)**
- **Replica failover drills**
- **MySQL monitoring with Prometheus**
- **RDS MySQL vs EC2 MySQL commands**
- **Production MySQL runbook**

Just tell me what’s next 🔥
