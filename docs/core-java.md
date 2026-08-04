# Core Java — Deep Dives (story format)

This page expands selected high‑priority Core Java topics in a story-style format: background/history, the original problem, evolution, when to use, how to use (examples), common pitfalls, and a model interview answer with follow-ups.

## JMM and happens-before

Background / history
- The Java Memory Model (JMM) was formalized to define how threads interact through memory: what guarantees the JVM provides for visibility and ordering of reads/writes. Before the JMM (and proper specification), reasoning about multithreaded Java was error-prone across JVMs and architectures.

Problem it solved
- Without a formal model, Java programs could exhibit surprising reordering and visibility issues on modern CPUs and optimizing compilers. The JMM specifies happens-before rules so developers can reason about correct synchronization.

Evolution
- The JMM was introduced with Java 5, providing well-defined semantics for volatile, synchronized, final fields, and thread start/join. Later JVM and hardware optimizations respect these semantics.

When to care / use
- Always for concurrent code correctness. Use happens-before reasoning when you need to ensure one thread's write is visible to another (e.g., flags, publication of objects, initialization safety).

How to use (short examples)
- Example of broken publication:

```java
// BrokenPublisher.java — incorrect without synchronization or volatile
class BrokenPublisher {
    static Helper helper; // non-volatile

    static class Helper { int value = 42; }

    static void writer() {
        helper = new Helper(); // write to helper
    }

    static void reader() {
        Helper h = helper; // may see partially constructed object
        if (h != null) System.out.println(h.value);
    }
}
```

- Fix 1: use volatile

```java
volatile static Helper helper;
// writer and reader unchanged — volatile write/read establish happens-before
```

- Fix 2: use synchronized for publication

```java
synchronized(staticLock) { helper = new Helper(); }
synchronized(staticLock) { Helper h = helper; }
```

Common pitfalls
- Assuming writes are immediately visible without synchronization or volatile.
- Believing volatile provides atomicity for compound operations (it does not).

Model interview answer (short)
- "The JMM defines happens-before relationships which guarantee ordering and visibility across threads. Actions like a volatile write-to-read, unlock-to-lock (synchronized), and thread start-to-run establish happens-before. Use volatile for simple visibility flags and synchronized/locks for compound operations and invariant protection."

Follow-ups to probe depth
- Show a code example that demonstrates reordering (out-of-order writes) and how volatile fixes it.
- Explain final fields and safe publication semantics.

---

## volatile vs synchronized — semantics and pitfalls

Background
- volatile and synchronized both affect visibility but have different guarantees. volatile is lighter-weight (no mutual exclusion) and provides visibility and ordering; synchronized provides mutual exclusion plus visibility.

Original problem & evolution
- Early Java had only synchronized; volatile semantics were ambiguous across JVMs until JMM clarity in Java 5. Now volatile provides a happens-before guarantee for reads/writes to that variable.

When to use
- Use volatile for single-write-many-read flags and for state that is updated atomically (with primitive reads/writes) and where no compound action is required. Use synchronized (or locks) for composite operations or when you must guard invariants.

How to use (examples)
- volatile example (visibility flag):

```java
volatile boolean running = true;
// thread A: running = false;
// thread B: while (running) { /* loop will see update */ }
```

- Not atomic: volatile does not make increment atomic:

```java
volatile int counter;
// counter++ is read-modify-write — still races
```

- synchronized example (atomicity and mutual exclusion):

```java
synchronized void increment() { counter++; }
```

Pitfalls
- Overusing synchronized causes contention; overusing volatile for non-atomic compound operations causes subtle bugs.
- Using volatile for lazy initialization of complex objects without ensuring safe publication leads to seeing partially constructed state.

Model interview answer (short)
- "volatile guarantees visibility and ordering for individual reads/writes, but not atomicity for compound actions. synchronized provides mutual exclusion and also establishes happens-before between unlock and subsequent lock. Use volatile for simple flags and synchronized/locks for protecting invariants or compound actions."

Follow-ups
- Give an example where volatile is insufficient and show a correct pattern (like using AtomicInteger or synchronized).

---

## Double-checked locking (DCL) and safe lazy initialization

Background
- DCL is an optimization to avoid synchronization on every access when performing lazy initialization of a singleton or expensive resource. Historically error-prone before volatile semantics were well-defined.

Problem it solved
- Avoid costly synchronization on the fast path while ensuring the resource is initialized once safely.

Evolution
- Pre-Java 5 DCL could fail due to reordering (constructor writes and reference assignment reordered). With JMM and volatile semantics (Java 5+), DCL can be implemented correctly by marking the instance volatile.

When to use
- When you need lazy initialization with low contention and low overhead once initialized. Prefer static initializers or enum singletons if eager init is acceptable.

How to use (example — correct pattern)

```java
public class LazySingleton {
    private static volatile LazySingleton instance;

    private LazySingleton() { /* expensive setup */ }

    public static LazySingleton getInstance() {
        LazySingleton result = instance;
        if (result == null) { // first check (no locking)
            synchronized (LazySingleton.class) {
                result = instance;
                if (result == null) { // second check (with lock)
                    instance = result = new LazySingleton();
                }
            }
        }
        return result;
    }
}
```

Why volatile matters
- volatile prevents reordering of object construction and assignment so that other threads never see a half-constructed object.

Pitfalls & alternatives
- DCL adds complexity — consider static-holder idiom or enums:

```java
// static holder idiom
public class HolderSingleton {
  private static class Holder { static final HolderSingleton I = new HolderSingleton(); }
  public static HolderSingleton get() { return Holder.I; }
}
```

Model interview answer (short)
- "DCL reduces synchronization overhead by checking before and after acquiring a lock. In Java 5+, mark the instance volatile to prevent reorderings; otherwise, it can expose a partially constructed object. Simpler alternatives are the static holder idiom or enum singletons."

Follow-ups
- Ask candidate to explain what reordering could happen without volatile and how it leads to observing a partially constructed object.

---

## CAS, Atomic classes and the ABA problem

Background
- Compare-and-swap (CAS) is a lock-free atomic instruction used to build non-blocking algorithms. Java provides Atomic* classes built on CAS and more flexible VarHandles since Java 9.

Problem it solved
- CAS enables high-throughput low-latency concurrent updates without locks, avoiding context-switch and blocking overhead.

Evolution
- Java concurrency utilities (java.util.concurrent.atomic, ConcurrentHashMap) used CAS heavily; newer VarHandle API gives more flexible memory semantics and supports advanced algorithms.

When to use
- Use CAS/atomic types for simple lock-free counters, flags, and building blocks of more complex non-blocking data structures. Prefer higher-level concurrent collections when available unless you need custom behavior.

How to use (code examples)
- Atomic counter:

```java
import java.util.concurrent.atomic.AtomicInteger;

AtomicInteger counter = new AtomicInteger(0);
int newVal = counter.incrementAndGet();
```

- Typical CAS loop (using AtomicReference):

```java
AtomicReference<Node> head = new AtomicReference<>();

void push(Node node) {
    Node oldHead;
    do {
        oldHead = head.get();
        node.next = oldHead;
    } while (!head.compareAndSet(oldHead, node));
}
```

ABA problem
- ABA occurs when a location changes A→B→A; a CAS that checks A may succeed incorrectly because value returned to A but state changed in between, breaking correctness for some algorithms.

Mitigations
- Use stamped references (AtomicStampedReference) or mark bits (AtomicMarkableReference) that carry a version or tag to detect changes. Use hazard pointers or safe memory reclamation where required.

Model interview answer (short)
- "CAS is a hardware primitive exposed via Atomic classes; it enables lock-free algorithms. The ABA problem can break CAS-based correctness; mitigate it with version stamps (AtomicStampedReference) or higher-level concurrency utilities."

Follow-ups
- Ask to implement a lock-free stack and explain where ABA might manifest and how they would fix it.

---

## ThreadLocal: usage and memory-leaks with thread pools

Background
- ThreadLocal allows per-thread state without synchronization and was introduced for cases like per-thread SimpleDateFormat instances or transaction contexts.

Problem it solved
- Avoids synchronization for thread-confined mutable state and simplifies APIs that need per-thread context.

Evolution
- ThreadLocal is powerful but dangerous in pooled-thread environments, because threads are reused and ThreadLocal values can outlive the intended scope causing memory leaks.

When to use
- Use for short-lived per-thread state tied to the lifetime of a task when you can guarantee cleanup. Prefer passing explicit context where possible.

How it works (brief)
- Each Thread has a ThreadLocalMap that maps ThreadLocal instances to values. Keys are held with weak references; values are strong references. If the ThreadLocal key is GCed, the value can remain until cleanup, leaking memory.

Example and leak scenario

```java
private static final ThreadLocal<SimpleDateFormat> TL = ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));

// In a thread-pool worker
try {
   String s = TL.get().format(new Date());
   // process
} finally {
   // must call TL.remove(); otherwise value stays attached to the worker thread
}
```

Mitigations
- Always call remove() in a finally block after use in pooled threads.
- Avoid storing large object graphs in ThreadLocal.
- Prefer explicit context objects passed as method arguments or use frameworks that manage scope explicitly.

Pitfalls
- In frameworks (web containers), forgetting to clear ThreadLocal after request processing causes cross-request leakage.
- Relying on ThreadLocal as global storage causes subtle, hard-to-find bugs when threads are reused.

Model interview answer (short)
- "ThreadLocal stores values per-thread using a ThreadLocalMap. In thread pools, threads are reused so ThreadLocal values can leak if not removed; always clean up in finally or avoid ThreadLocal in pooled environments."

Follow-ups
- Ask the candidate how to detect ThreadLocal leaks in production and tools/methods for remediation.

---

If you want, I will now commit this file to the repository and then expand more questions from the Concurrency group or move to the next category. Which should I do next: commit these changes to the default branch, or create a feature branch?