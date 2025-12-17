<div align="center">

# 🔥 MemKV

### A High-Performance Redis-Compatible In-Memory Database Engine

[![Go Version](https://img.shields.io/badge/Go-1.21+-00ADD8?style=for-the-badge&logo=go&logoColor=white)](https://golang.org/)
[![Redis Compatible](https://img.shields.io/badge/Redis-Compatible-DC382D?style=for-the-badge&logo=redis&logoColor=white)](https://redis.io/)
[![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20macOS-lightgrey?style=for-the-badge)](/)

<p align="center">
  <img src="memkv.png" alt="MemKV Logo" width="400"/>
</p>

*A Redis-inspired in-memory key-value database built from scratch in Go, featuring custom data structures, probabilistic algorithms, and high-performance I/O multiplexing.*

</div>

---

## 📖 Overview

**MemKV** is a high-performance, in-memory key-value database that implements the **RESP (Redis Serialization Protocol)**, making it fully compatible with `redis-cli` and standard Redis client libraries.

This project demonstrates deep understanding of:
- Systems programming and low-level networking
- Custom data structure implementation
- Probabilistic data structures and algorithms
- Event-driven architecture design

---

## ✨ Key Features

### 🚀 High-Performance I/O
- **Single-threaded event-loop architecture** eliminates thread synchronization overhead
- **Platform-specific I/O multiplexing**: `epoll` (Linux) and `kqueue` (macOS)
- **Non-blocking sockets** for efficient handling of thousands of concurrent connections

### 🔌 Redis Protocol Compatibility
- Full **RESP (Redis Serialization Protocol)** implementation
- Works out-of-the-box with `redis-cli` and any Redis client library
- Supports 30+ Redis commands across 6 data type categories

### 🏗️ Custom Data Structures (Built from Scratch)
| Data Structure | Description | Time Complexity |
|----------------|-------------|-----------------|
| **Skip List** | Multi-level linked list for sorted sets | O(log n) insert/delete/search |
| **ZSet** | Sorted set combining Skip List + Hash Map | O(log n) rank queries |
| **Geohash** | Spatial indexing using bit interleaving | O(1) encode/decode |

### 📊 Probabilistic Data Structures
| Data Structure | Use Case | Space Efficiency |
|----------------|----------|------------------|
| **Scalable Bloom Filter** | Set membership testing with auto-scaling | ~1.44 bytes per element |
| **Count-Min Sketch** | Frequency estimation in data streams | Sub-linear space |

### 🛡️ Production-Ready Features
- **Graceful shutdown** handling with proper connection cleanup
- **Atomic operations** for thread-safe state management
- **Configurable** host, port, and connection limits

---

## 🏛️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Client Requests                          │
│                    (redis-cli / Redis clients)                   │
└─────────────────────────────┬───────────────────────────────────┘
                              │ RESP Protocol
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      TCP Server (Non-blocking)                   │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │              I/O Multiplexer (epoll/kqueue)               │  │
│  │                    Event Loop (Single Thread)              │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                       Command Processor                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │ RESP Parser │──│  Evaluator  │──│    Response Encoder     │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                        Storage Layer                             │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐ ┌────────┐  │
│  │  String  │ │   Set    │ │  ZSet    │ │  Bloom  │ │  CMS   │  │
│  │  (Hash)  │ │(HashMap) │ │(SkipList)│ │ Filter  │ │ Sketch │  │
│  └──────────┘ └──────────┘ └──────────┘ └─────────┘ └────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Technology Stack

| Category | Technologies |
|----------|--------------|
| **Language** | Go 1.21+ |
| **Protocol** | RESP (Redis Serialization Protocol) |
| **Networking** | Raw syscalls, Non-blocking I/O |
| **I/O Multiplexing** | epoll (Linux), kqueue (macOS) |
| **Hashing** | MurmurHash3 (128-bit) |
| **Algorithms** | Skip List, Geohash, Bloom Filter, Count-Min Sketch |

---

## 📋 Supported Commands

### General
| Command | Description | Example |
|---------|-------------|---------|
| `PING` | Test server connectivity | `PING` → `PONG` |

### String Operations
| Command | Description | Example |
|---------|-------------|---------|
| `SET` | Set key to hold string value | `SET mykey "Hello"` |
| `GET` | Get the value of key | `GET mykey` |
| `DEL` | Delete one or more keys | `DEL mykey` |
| `INCR` | Increment integer value by 1 | `INCR counter` |
| `EXPIRE` | Set key expiration (seconds) | `EXPIRE mykey 60` |
| `TTL` | Get remaining time to live | `TTL mykey` |

### Set Operations
| Command | Description | Example |
|---------|-------------|---------|
| `SADD` | Add members to set | `SADD myset "a" "b"` |
| `SREM` | Remove members from set | `SREM myset "a"` |
| `SMEMBERS` | Get all members | `SMEMBERS myset` |
| `SISMEMBER` | Check if member exists | `SISMEMBER myset "a"` |
| `SCARD` | Get set cardinality | `SCARD myset` |
| `SPOP` | Remove and return random member | `SPOP myset` |
| `SRAND` | Get random member | `SRAND myset` |

### Sorted Set Operations (Skip List)
| Command | Description | Example |
|---------|-------------|---------|
| `ZADD` | Add members with scores | `ZADD leaderboard 100 "player1"` |
| `ZRANK` | Get rank of member (0-indexed) | `ZRANK leaderboard "player1"` |
| `ZSCORE` | Get score of member | `ZSCORE leaderboard "player1"` |
| `ZREM` | Remove members | `ZREM leaderboard "player1"` |
| `ZCARD` | Get sorted set cardinality | `ZCARD leaderboard` |

### Geospatial Operations (Geohash)
| Command | Description | Example |
|---------|-------------|---------|
| `GEOADD` | Add geospatial items | `GEOADD locations 13.361389 52.519444 "Berlin"` |
| `GEODIST` | Distance between two members | `GEODIST locations "Berlin" "Paris" km` |
| `GEOHASH` | Get geohash strings | `GEOHASH locations "Berlin"` |
| `GEOPOS` | Get coordinates | `GEOPOS locations "Berlin"` |
| `GEOSEARCH` | Search within radius | `GEOSEARCH locations FROMMEMBER "Berlin" BYRADIUS 100 km` |

### Bloom Filter Operations
| Command | Description | Example |
|---------|-------------|---------|
| `BF.RESERVE` | Create filter with capacity/error rate | `BF.RESERVE myfilter 0.01 1000` |
| `BF.MADD` | Add multiple items | `BF.MADD myfilter "item1" "item2"` |
| `BF.EXISTS` | Check if item may exist | `BF.EXISTS myfilter "item1"` |
| `BF.MEXISTS` | Check multiple items | `BF.MEXISTS myfilter "item1" "item2"` |
| `BF.INFO` | Get filter information | `BF.INFO myfilter` |

### Count-Min Sketch Operations
| Command | Description | Example |
|---------|-------------|---------|
| `CMS.INITBYDIM` | Create by width × depth | `CMS.INITBYDIM mycms 1000 5` |
| `CMS.INITBYPROB` | Create by error rate/probability | `CMS.INITBYPROB mycms 0.001 0.01` |
| `CMS.INCRBY` | Increment item count | `CMS.INCRBY mycms "item1" 5` |
| `CMS.QUERY` | Query item frequency | `CMS.QUERY mycms "item1"` |

---

## 🚀 Getting Started

### Prerequisites
- Go 1.21 or higher
- Linux or macOS operating system

### Installation

```bash
# Clone the repository
git clone https://github.com/datblue21/Redis-Database.git
cd Redis-Database

# Build the project
go build -o memkv ./cmd/main.go
```

### Running the Server

```bash
# Start with default settings (0.0.0.0:8081)
./memkv

# Or with custom host and port
./memkv -host 127.0.0.1 -port 6379
```

### Connecting with redis-cli

```bash
# Connect to MemKV server
redis-cli -p 8081

# Test connection
127.0.0.1:8081> PING
PONG

# Try some commands
127.0.0.1:8081> SET greeting "Hello, MemKV!"
OK
127.0.0.1:8081> GET greeting
"Hello, MemKV!"
```

---

## 💡 Usage Examples

### Working with Sorted Sets (Leaderboard)

```bash
# Add players with scores
ZADD leaderboard 1500 "Alice"
ZADD leaderboard 2100 "Bob"
ZADD leaderboard 1800 "Charlie"

# Get rankings
ZRANK leaderboard "Bob"      # Returns: 2 (highest score = highest rank)
ZSCORE leaderboard "Alice"   # Returns: 1500
```

### Geospatial Queries (Find Nearby Locations)

```bash
# Add cities with coordinates
GEOADD cities -122.4194 37.7749 "San Francisco"
GEOADD cities -118.2437 34.0522 "Los Angeles"
GEOADD cities -73.9857 40.7484 "New York"

# Calculate distance
GEODIST cities "San Francisco" "Los Angeles" km   # Returns: ~559 km

# Find cities within radius
GEOSEARCH cities FROMMEMBER "San Francisco" BYRADIUS 600 km
```

### Bloom Filter (Duplicate Detection)

```bash
# Create a bloom filter for URL deduplication
BF.RESERVE visited_urls 0.001 1000000

# Add visited URLs
BF.MADD visited_urls "https://example.com" "https://google.com"

# Check if URL was visited
BF.EXISTS visited_urls "https://example.com"   # Returns: 1 (probably yes)
BF.EXISTS visited_urls "https://new-site.com"  # Returns: 0 (definitely no)
```

### Count-Min Sketch (Frequency Counting)

```bash
# Create sketch for counting page views
CMS.INITBYPROB pageviews 0.001 0.01

# Increment view counts
CMS.INCRBY pageviews "/home" 100
CMS.INCRBY pageviews "/about" 50
CMS.INCRBY pageviews "/home" 25

# Query approximate counts
CMS.QUERY pageviews "/home"   # Returns: ~125
```

---

## 🔬 Data Structure Deep Dive

### Skip List Implementation

The Skip List provides O(log n) average time complexity for insert, delete, and search operations. Our implementation features:

- **32 levels maximum** for handling large datasets
- **Span tracking** for O(log n) rank queries
- **Backward pointers** for reverse traversal

```
Level 3: HEAD ────────────────────────────────────► NULL
Level 2: HEAD ──────────► [5] ──────────────────► NULL  
Level 1: HEAD ──► [3] ──► [5] ──► [7] ──────────► NULL
Level 0: HEAD ──► [3] ──► [5] ──► [7] ──► [9] ──► NULL
```

### Geohash Encoding

52-bit precision geohash providing ~0.6 meter accuracy:

- **Bit interleaving** technique for 2D to 1D mapping
- **Neighbor calculation** via bit manipulation
- **Haversine formula** for accurate distance calculations

### Scalable Bloom Filter

Auto-scaling bloom filter with error rate tightening:

- **Error tightening ratio**: 0.5× per new layer
- **Growth factor**: 2× capacity per expansion
- **MurmurHash3** for uniform hash distribution

---

## 🗺️ Roadmap

- [ ] HyperLogLog for cardinality estimation
- [ ] Morris Counter for approximate counting
- [ ] Cuckoo Filter for better deletion support
- [ ] Approximate LRU eviction policy
- [ ] Approximate LFU eviction policy
- [ ] Persistence (RDB/AOF snapshots)
- [ ] Cluster mode support

---

## 📚 References

- [Redis Protocol Specification (RESP)](https://redis.io/docs/reference/protocol-spec/)
- [Skip Lists: A Probabilistic Alternative to Balanced Trees](https://15721.courses.cs.cmu.edu/spring2018/papers/08-oltpindexes1/pugh-skiplists-cacm1990.pdf)
- [Scalable Bloom Filters](https://gsd.di.uminho.pt/members/cbm/ps/dbloom.pdf)
- [Count-Min Sketch](http://dimacs.rutgers.edu/~graham/pubs/papers/cm-full.pdf)
- [Geohash Algorithm](https://en.wikipedia.org/wiki/Geohash)

---

<div align="center">

**Built with ❤️ in Go**

*If you found this project helpful, please consider giving it a ⭐*

</div>
