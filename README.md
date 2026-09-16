# 🏛️ System Design & Software Architecture Chronicles
### *Deep-Dive, Research-Backed Engineering Series for Production Practitioners*

[![Status: Active](https://img.shields.io/badge/Status-Active%20%7C%20Topic%2001%20Ready-success.svg)](#-system-design-topics-index)
[![Audience: Beginner to 15+ Yrs](https://img.shields.io/badge/Audience-Beginner%20to%2015%2B%20Yrs%20Experience-orange.svg)](#-about-this-repository)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

An open-source repository of in-depth software architecture and system design series. Each topic is structured as a self-contained, publication-ready collection of essays written for software engineers across the entire career continuum—from **beginners building their first production systems to staff architects and tech leads with 10+ and 15+ years of experience managing enterprise complexity**.

---

## 🧭 Repository Architecture: Clean Domain Separation

To keep distinct System Design subjects modular and prevent cross-topic coupling, every topic lives in its own dedicated, self-contained root folder:

```text
Mediumblogs/
├── README.md                                  # Master index across all System Design topics
├── LICENSE                                    # MIT License
├── .gitignore
│
├── 01-filestructure/                          # 📁 Topic 01: File Structure & Codebase Architecture
│   ├── README.md                              # Topic roadmap & overview
│   └── articles/                              # 📖 Publication-ready Markdown essays
│       ├── README.md
│       └── 01-your-folder-structure-is-a-message.md
│
├── 02-caching/                                # ⚡ Topic 02: Distributed Caching (Planned)
├── 03-database-sharding/                      # 💾 Topic 03: Partitioning & Scaling (Planned)
└── 04-event-driven/                           # 📨 Topic 04: Event-Driven Systems (Planned)
```

---

## 📚 System Design Topics Index

| Topic # | Topic / Series Name | Description | Status | Direct Link |
| :---: | :--- | :--- | :---: | :---: |
| **01** | **File Structure & Codebase Architecture** | Conway's Law, directory decay, forcing functions, and defining module boundaries before external shocks break your system. | ✅ Active (Part 1 Ready) | [Explore Topic 01](01-filestructure/) |
| **02** | **Distributed Caching & Cache Invalidation** | Cache-aside, write-through, stampede mitigation, consistent hashing, and distributed cache coherence under high QPS. | ⏳ Planned | *Coming soon* |
| **03** | **Database Partitioning & High Availability** | Horizontal sharding, read replicas, replication lag, distributed transactions, and split-brain resolution. | ⏳ Planned | *Coming soon* |
| **04** | **Event-Driven Architecture & Messaging** | Message brokers, idempotency, outbox pattern, dead-letter queues, and exactly-once processing semantics. | ⏳ Planned | *Coming soon* |
| **05** | **Modular Monoliths vs. Microservices** | The inflection point where distributed complexity pays for itself, and how to decouple without network latency. | ⏳ Planned | *Coming soon* |

---

## 🚀 Quick Navigation for Topic 01 (File Structure)

- **Read Part 1 on GitHub**: [01-your-folder-structure-is-a-message.md](01-filestructure/articles/01-your-folder-structure-is-a-message.md)
- **Topic 01 Overview & Roadmap**: [01-filestructure/README.md](01-filestructure/README.md)

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).
