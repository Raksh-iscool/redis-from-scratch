# **Lightweight Key-Value Store**

This project is a simple, in-memory key-value store similar to **Redis**, supporting **string and hash operations** with concurrency safety and periodic data persistence.

## Prequisites

Have Go installed.

## Usage

- Clone the repo
- Run by executing

```
go run .
```

- Or build and run using

```
go build . && ./redis
```

- Connect to the server with any redis client. For example, `redis-cli`. The server listens on port `6379` by default.

```
$ redis-cli
```

## Persistence

The `encoding/gob` package is used to asynchronously create a dump of the store in a binary file called `dump.rdb` every one second. This `dump.rdb` file is used to restore the data when then server is restarted in the same directory.

## Supported commands

- PING
- SET
- GET
- DEL
- MSET
- MGET
- HSET
- HGET
- HDEL
- EXISTS
- HEXISTS

### **1. String-Based Commands**

| Command                        | Description                     | Example              | Response          |
| ------------------------------ | ------------------------------- | -------------------- | ----------------- |
| `PING`                         | Check if the server is running. | `PING`               | `+PONG`           |
| `SET key value`                | Store a key-value pair.         | `SET username Alice` | `+OK`             |
| `GET key`                      | Retrieve the value of a key.    | `GET username`       | `$5 Alice`        |
| `DEL key`                      | Delete a key.                   | `DEL username`       | `:1` (if deleted) |
| `MSET key1 val1 key2 val2 ...` | Set multiple key-value pairs.   | `MSET a 1 b 2`       | `+OK`             |
| `MGET key1 key2 ...`           | Retrieve multiple values.       | `MGET a b`           | `$1 1 $1 2`       |

---

### **2. Hash-Based Commands**

| Command                 | Description                             | Example                     | Response          |
| ----------------------- | --------------------------------------- | --------------------------- | ----------------- |
| `HSET hash field value` | Store a field-value pair inside a hash. | `HSET user:1001 name Alice` | `:1`              |
| `HGET hash field`       | Retrieve a value from a hash.           | `HGET user:1001 name`       | `$5 Alice`        |
| `HDEL hash field`       | Delete a field from a hash.             | `HDEL user:1001 name`       | `:1` (if deleted) |
| `HEXISTS hash field`    | Check if a field exists in a hash.      | `HEXISTS user:1001 name`    | `:1` (if exists)  |

---

### **3. Existence Checks**

| Command              | Description                        | Example                  | Response         |
| -------------------- | ---------------------------------- | ------------------------ | ---------------- |
| `EXISTS key`         | Check if a key exists.             | `EXISTS username`        | `:1` (if exists) |
| `HEXISTS hash field` | Check if a field exists in a hash. | `HEXISTS user:1001 name` | `:1` (if exists) |

---

## **Persistence**

- The store is **saved to a file (`dump.rdb`)** using the `encoding/gob` package **every 1 second**.
- When the server restarts, it **reloads data** from `dump.rdb`.
- This ensures data **persistence** across sessions.

---

## **Concurrency Handling**

- **Read operations** use `sync.RWMutex` **read locks (`RLock`)** for efficiency.
- **Write operations** use **exclusive locks (`Lock`)** to ensure safe modifications.
- This allows **multiple reads** at once while preventing data corruption during writes.

---

## **Example Usage**

### **Start the server**

```sh
go run server.go
```

### **Using the commands via CLI (or a Redis client)**

```sh
SET name Alice
GET name
MSET key1 value1 key2 value2
MGET key1 key2
HSET user:1 age 30
HGET user:1 age
EXISTS key1


```
