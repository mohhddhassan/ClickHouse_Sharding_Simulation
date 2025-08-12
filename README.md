# ClickHouse Sharding & Weights Distribution ⚡

This project demonstrates **ClickHouse sharding with weighted distribution** using Docker.  
It simulates how data is distributed across shards when different **weights** are assigned, allowing you to test horizontal scaling behavior locally.

---

## 📦 What’s Inside?

* **Docker Compose** setup for a 2-shard ClickHouse cluster
* `remote_servers.xml` to configure shard weights (e.g., 1 vs 10)
* SQL examples to create `shard_local` and `shard_dist` tables
* Step-by-step guide to insert and verify weighted data distribution

---

## 🔧 Getting Started

### 1. Clone the repo
```bash
git clone https://github.com/mohhddhassan/ClickHouse_Sharding_Simulation.git
cd ClickHouse_Sharding_Simulation
````

### 2. Start the cluster

```bash
docker-compose up -d
```

### 3. Create tables on each shard

```sql
CREATE TABLE shard_local (
    id UInt64,
    name String
) ENGINE = MergeTree()
ORDER BY id;
```

### 4. Create the distributed table

```sql
CREATE TABLE shard_dist AS shard_local
ENGINE = Distributed(my_cluster, default, shard_local, rand());
```

### 5. Insert test data

```sql
INSERT INTO shard_dist
SELECT number, concat('name_', toString(number))
FROM numbers(10000);
```

### 6. Verify distribution

```bash
docker exec -it ch-shard1 clickhouse-client -q "SELECT count() FROM shard_local;"
docker exec -it ch-shard2 clickhouse-client -q "SELECT count() FROM shard_local;"
```

---

## 📂 File Structure

```
ClickHouse_Sharding_Simulation/
├── docker-compose.yml
├── configs/
│   └── remote_servers.xml
└── README.md
```

---

## 🧠 What I Learned

* How **ClickHouse** distributes data across shards
* Using **weights** to control shard load distribution
* Running a ClickHouse cluster locally with **Docker Compose**

---

## 📝 Notes

This setup is intended for **learning and simulation** purposes.
In real-world production, sharding strategies may also depend on partition keys, cluster size, and hardware capacity.

```

---
