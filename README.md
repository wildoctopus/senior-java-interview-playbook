# Senior Java Interview Playbook

This repository collects in-depth Senior- and Lead-level Java interview topics, questions, follow-ups, and model answers. The main README organizes questions into broad categories; each question links to a subpage that contains the full solution, follow-up questions, and suggested answers.

Add new questions by creating or updating files under the `docs/` directory and adding a link from the table below.

## How links work
- Main README links point to subpages in `docs/`.
- Each question on a subpage should use a stable heading so the link can target it (e.g. `docs/core-java.md#concurrency-volatile-vs-synchronized`).

---

## Table of Contents
| Category | Questions |
|---|---|
| Core Java | - [Concurrency: volatile vs synchronized](docs/core-java.md#concurrency-volatile-vs-synchronized)<br>- [Immutability and design: when to use](docs/core-java.md#immutability-and-design-when-to-use) |
| JVM & Garbage Collection | - [GC tuning: Choosing a collector](docs/jvm-gc.md#gc-tuning-choosing-a-collector) |
| Spring & Spring Boot | - [Transaction management: propagation levels](docs/spring.md#transaction-management-propagation-levels) |
| System Design | - [Design a cache for high throughput](docs/system-design.md#design-a-cache-for-high-throughput) |
| Coding Problems | - [Concurrent data structures: producer/consumer](docs/coding-problems.md#producer-consumer-with-blocking-queue) |
| Performance & Memory Tuning | - [Heap vs off-heap: trade-offs](docs/performance.md#heap-vs-off-heap-trade-offs) |
| Distributed Systems | - [Consensus basics: leader election](docs/distributed-systems.md#consensus-basics-leader-election) |
| Databases & Persistence | - [Indexing strategies for OLTP](docs/databases.md#indexing-strategies-for-oltp) |
| Testing & Observability | - [Chaos engineering: safe experiments](docs/testing.md#chaos-engineering-safe-experiments) |
| Leadership & Architecture | - [Technical roadmap: aligning teams](docs/leadership.md#technical-roadmap-aligning-teams) |

---

## Contributing
- Create or update files under `docs/` with the question, in-depth answer, follow-up questions and model answers.
- Use headings (H2/H3) for each question so links from the main README can target them.
- Follow the example in `docs/core-java.md`.

---

Maintainers: @wildoctopus
