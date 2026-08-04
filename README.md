# Senior Java Interview Playbook

This repository collects in-depth Senior- and Lead-level Java interview topics, questions, follow-ups, and model answers. The main README organizes questions into broad categories; each question links to a subpage that contains the full solution, follow-up questions, and suggested answers.

Add new questions by creating or updating files under the `docs/` directory and adding a link from the table below.

## How links work
- Main README links point to subpages in `docs/`.
- Each question on a subpage uses a stable heading so the link can target it (e.g. `docs/core-java.md#jmm-happens-before`).

---

## Core Java — Top Categories (links go to docs/core-java.md)

This repository focuses first on Core Java topics commonly asked for Senior / Lead roles (banks and large service companies, 2024–2026). Click a question to open the detailed story-style page (background → original problem → evolution → when to use → how to use + examples → pitfalls → model answer).

### Concurrency & Memory Model
- [Java Memory Model (JMM) and happens-before](docs/core-java.md#jmm-happens-before)
- [volatile vs synchronized — semantics and pitfalls](docs/core-java.md#volatile-vs-synchronized)
- [Double-checked locking and safe lazy initialization](docs/core-java.md#double-checked-locking)
- [Locks: synchronized, ReentrantLock, ReadWriteLock, StampedLock](docs/core-java.md#locks-overview)
- [CAS, Atomic classes and ABA problem](docs/core-java.md#cas-atomic-aba)
- [ThreadLocal: usage and memory-leaks with thread pools](docs/core-java.md#threadlocal-memory-leaks)
- [Producer–Consumer patterns and BlockingQueue choices](docs/core-java.md#producer-consumer)
- [ForkJoinPool and work-stealing](docs/core-java.md#forkjoin-workstealing)
- [CompletableFuture: composition and context propagation](docs/core-java.md#completablefuture)
- [Deadlock, livelock and starvation detection and mitigation](docs/core-java.md#deadlock-livelock-starvation)
- [Concurrent caches: design to minimize contention](docs/core-java.md#concurrent-cache)
- [Virtual threads (Project Loom): design and migration concerns](docs/core-java.md#virtual-threads)

### JVM Internals & Garbage Collection
- [JVM memory layout: heap, metaspace, stack, code cache, direct/native memory](docs/core-java.md#jvm-memory-layout)
- [Heap vs native memory pressure — how to tell in production](docs/core-java.md#heap-vs-native)
- [GC comparison: G1, Parallel, CMS, ZGC, Shenandoah](docs/core-java.md#gcs-comparison)
- [Interpreting GC logs: promotion failures, long pauses, allocation storms](docs/core-java.md#interpreting-gc-logs)
- [Investigating OutOfMemoryError: tools & workflow](docs/core-java.md#oom-investigation)
- [Escape analysis, scalar replacement and JIT impacts](docs/core-java.md#escape-analysis)
- [Class loading and parent delegation model; custom classloaders](docs/core-java.md#classloading)
- [Tuning survivor spaces, tenuring and MaxGCPauseMillis](docs/core-java.md#survivor-tenuring)
- [JIT and tiered compilation: startup vs steady-state](docs/core-java.md#jit-tiered)
- [Reference types: strong, soft, weak, phantom](docs/core-java.md#reference-types)
- [JVM tuning in containers (cgroup awareness)](docs/core-java.md#jvm-containers)

### Collections & Data Structures
- [HashMap internals: hashing, bins, resizing, treeification](docs/core-java.md#hashmap-internals)
- [Defending against hash-flooding attacks](docs/core-java.md#hash-flooding)
- [ConcurrentHashMap internals (Java 8+)](docs/core-java.md#concurrenthashmap)
- [Designing an LRU cache with concurrency](docs/core-java.md#lru-cache)
- [ArrayList vs LinkedList vs ArrayDeque — tradeoffs](docs/core-java.md#list-comparisons)
- [CopyOnWriteArrayList semantics and use-cases](docs/core-java.md#copyonwrite)
- [PriorityQueue internals and comparator pitfalls](docs/core-java.md#priorityqueue)
- [Boxed primitives vs primitive collections — performance impact](docs/core-java.md#boxed-primitives)
- [ConcurrentSkipListMap vs ConcurrentHashMap — ordered vs unordered maps](docs/core-java.md#skiplist-vs-chm)
- [LinkedHashMap LRU ordering and concurrency limitations](docs/core-java.md#linkedhashmap-lru)

### Language & Core API Features
- [Generics deep-dive: type erasure and wildcards](docs/core-java.md#generics)
- [Overloading vs overriding and bridge methods](docs/core-java.md#overload-override-bridge)
- [Reflection vs MethodHandle/VarHandle — trade-offs](docs/core-java.md#reflection-vs-methodhandle)
- [Records, sealed classes, pattern matching — adoption considerations](docs/core-java.md#records-sealed)
- [equals(), hashCode(), compareTo() contracts and common bugs](docs/core-java.md#equals-hashcode)
- [Lambdas and method references: allocation and serialization concerns](docs/core-java.md#lambdas)
- [Streams: lazy evaluation, stateful ops and parallelStream pitfalls](docs/core-java.md#streams)
- [Optional: correct use vs anti-patterns](docs/core-java.md#optional)
- [try-with-resources and suppressed exceptions patterns](docs/core-java.md#try-with-resources)
- [Default and static interface methods: binary compatibility issues](docs/core-java.md#default-static-methods)
- [Type inference improvements and generics pitfalls](docs/core-java.md#type-inference)

---

## Contributing
- To add or expand a question, edit or add files under `docs/` and update links in this README.
- Each question file should use H2 headings for stable links.

Maintainers: @wildoctopus
