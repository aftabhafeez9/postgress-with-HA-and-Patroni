# 🚀 PostgreSQL High Availability Cluster

This project demonstrates a multi-node **PostgreSQL High Availability Cluster** using:

- PostgreSQL  
- Patroni (automatic failover & cluster management)  
- Docker  

The cluster consists of:

- 3 PostgreSQL nodes
- Automatic leader election
- Streaming replication
- Automatic failover

---

# 🛠️ Setup Instructions

## 1️⃣ Clone the Repository

```bash
git clone https://github.com/your-username/postgres-ha-cluster.git
cd postgres-ha-cluster
```

---

## 2️⃣ Start the Cluster

```bash
docker-compose up -d
```

Wait until all containers are fully up and running.

---

# 🧪 Testing Automatic Failover

This section demonstrates automatic failover using Patroni.

---

## 📊 Step 1 – Check Initial Cluster Status

Run the following command:

```bash
docker exec -it node1 patronictl -c /etc/patroni.yml list
```

You should see output similar to:

```
+ Cluster: postgres-cluster (7611221409206767638) ------+-----+------------+-----+
| Member | Host  | Role    | State   | TL | Receive LSN | Lag | Replay LSN | Lag |
+--------+-------+---------+---------+----+-------------+-----+------------+-----+
| node1  | node1 | Leader  | running |  1 |             |     |            |     |
| node2  | node2 | Replica | running |  1 |   0/404D5D8 |   0 |  0/404D5D8 |   0 |
| node3  | node3 | Replica | running |  1 |   0/404D5D8 |   0 |  0/404D5D8 |   0 |
+--------+-------+---------+---------+----+-------------+-----+------------+-----+
```

### ✅ Explanation

- node1 is the **Leader**
- node2 and node3 are **Replicas**
- Timeline (TL) = 1
- All nodes are running normally

---

## ❌ Step 2 – Stop the Leader Node

Simulate failure by stopping node1:

```bash
docker stop node1
```

---

## 🔄 Step 3 – Check Cluster After Failure

Now check the cluster status again:

```bash
docker exec -it node1 patronictl -c /etc/patroni.yml list
```

You should see:

```
+ Cluster: postgres-cluster (7611221409206767638) --------+-----+------------+-----+
| Member | Host  | Role    | State     | TL | Receive LSN | Lag | Replay LSN | Lag |
+--------+-------+---------+-----------+----+-------------+-----+------------+-----+
| node2  | node2 | Leader  | running   |  2 |             |     |            |     |
| node3  | node3 | Replica | streaming |  2 |   0/404D6F0 |   0 |  0/404D6F0 |   0 |
+--------+-------+---------+-----------+----+-------------+-----+------------+-----+
```

### ✅ What Happened?

- node1 is down
- node2 automatically becomes the **Leader**
- node3 continues as Replica
- Timeline (TL) changed from 1 → 2
- Failover handled automatically by Patroni

---

## 🔁 Step 4 – Start node1 Again

```bash
docker start node1
```

---

## 📊 Step 5 – Check Final Cluster Status

```bash
docker exec -it node2 patronictl -c /etc/patroni.yml list
```

You should now see:

```
+ Cluster: postgres-cluster (7611221409206767638) --------+-----+------------+-----+
| Member | Host  | Role    | State     | TL | Receive LSN | Lag | Replay LSN | Lag |
+--------+-------+---------+-----------+----+-------------+-----+------------+-----+
| node1  | node1 | Replica | streaming |  2 |   0/404D6F0 |   0 |  0/404D6F0 |   0 |
| node2  | node2 | Leader  | running   |  2 |             |     |            |     |
| node3  | node3 | Replica | streaming |  2 |   0/404D6F0 |   0 |  0/404D6F0 |   0 |
+--------+-------+---------+-----------+----+-------------+-----+------------+-----+
```

### ✅ Final State

- node2 remains Leader
- node1 rejoins as Replica
- node3 continues as Replica
- Cluster is fully healthy
- No manual intervention required

---

# 🎯 What This Project Demonstrates

- Automatic Leader Election
- Automatic Failover
- Streaming Replication
- Timeline Switching
- High Availability Architecture

---

# 📌 Requirements

- Docker
- Docker Compose

---

# 📄 License

This project is for learning and reference purposes.
