# Concurrency Interview Guide (SDE2)

Everything concurrency for interviews in one page: concepts from threads to distributed locks → the LeetCode concurrency set → 12 "make it thread-safe" design problems, all with runnable Python solutions.

Related: [syllabus](../test/INTERVIEW_SYLLABUS.md) · [LLD guide](lld-interview-guide.md) · [HLD guide](hld-interview-guide.md) (distributed locks, idempotency, transactions at scale)

**Code conventions:** Python 3.10+, standard library only. Every block runs as-is and ends with assertions. Tests use real threads, so a few use short sleeps.

## Index

- [1. What gets asked](#1-what-gets-asked)
- [2. Concepts](#2-concepts)
  - [2.1 Processes, threads, coroutines, and the GIL](#21-processes-threads-coroutines-and-the-gil)
  - [2.2 Race conditions and critical sections](#22-race-conditions-and-critical-sections)
  - [2.3 Synchronization primitives](#23-synchronization-primitives)
  - [2.4 Deadlock, livelock, starvation](#24-deadlock-livelock-starvation)
  - [2.5 Pessimistic vs optimistic concurrency](#25-pessimistic-vs-optimistic-concurrency)
  - [2.6 Thread pools and futures](#26-thread-pools-and-futures)
  - [2.7 asyncio concurrency](#27-asyncio-concurrency)
  - [2.8 Python ↔ Java cheat sheet](#28-python--java-cheat-sheet)
  - [2.9 Thread lifecycle, daemon threads, thread-local state](#29-thread-lifecycle-daemon-threads-thread-local-state)
  - [2.10 Memory model, atomic operations, CAS, lock-free](#210-memory-model-atomic-operations-cas-lock-free)
  - [2.11 Multiprocessing in Python](#211-multiprocessing-in-python)
  - [2.12 asyncio in depth](#212-asyncio-in-depth)
  - [2.13 How servers handle concurrent requests](#213-how-servers-handle-concurrent-requests)
  - [2.14 Concurrency across processes and servers](#214-concurrency-across-processes-and-servers)
  - [2.15 Common concurrency bugs (checklist)](#215-common-concurrency-bugs-checklist)
  - [2.16 Testing and debugging concurrent code](#216-testing-and-debugging-concurrent-code)
- [3. LeetCode concurrency set](#3-leetcode-concurrency-set)
- [4. Design + implement problems](#4-design--implement-problems)
- [5. Interview Q&A](#5-interview-qa)

---

## 1. What gets asked

- **Standalone concurrency round:** rare (some fintech/infra teams, Uber, Microsoft teams, Indian product companies).
- **Much more common:** inside an LLD/machine-coding round → *"Now make it thread-safe"* or *"What if two users book the same seat?"*
- **Inside HLD:** the same race, but across servers → DB conditional updates, distributed locks, idempotency.
- **Language questions:** GIL, `threading` vs `multiprocessing` vs `asyncio` (Python); `synchronized`/`volatile`/`ConcurrentHashMap` (Java).

**SDE2 bar:** spot the race, name the invariant, pick the right primitive, keep critical sections small, avoid deadlock, and know where in-process locks stop working (multiple processes/servers).

---

## 2. Concepts

### 2.1 Processes, threads, coroutines, and the GIL

| | Process | Thread | Coroutine (asyncio) |
|---|---|---|---|
| Memory | Separate | Shared within a process | Shared, single thread |
| Switch | OS, expensive | OS, cheaper | Only at `await` (cooperative) |
| Parallel CPU work in CPython | **Yes** | No (GIL) | No |
| Good for | CPU-bound work | Blocking I/O, simple concurrency | Lots of I/O waits (HTTP, DB) |
| Communication | Queues, pipes, shared memory | Shared objects + locks | Shared objects (fewer races, not zero) |

**Concurrency vs parallelism:** concurrency = tasks make progress in overlapping time (can be one core). Parallelism = tasks run at the same instant on different cores.

**GIL (Global Interpreter Lock), standard CPython:**
- Only one thread executes Python bytecode at a time.
- Released during blocking I/O and by many C extensions (NumPy) → threads still help for I/O.
- CPU-bound pure Python → use `multiprocessing` / `ProcessPoolExecutor`.
- **The GIL does NOT make your code thread-safe.** A thread can be switched out between bytecodes, so `counter += 1` (load, add, store) can lose updates.
- Python 3.13+ has an optional free-threaded build (no GIL); shared-state rules matter even more there.

### 2.2 Race conditions and critical sections

**Race condition:** the result depends on timing between threads.

**Two shapes you must recognize instantly:**
1. **Read-modify-write:** `x = x + 1`, `balance -= amount`.
2. **Check-then-act:** `if seat.free: seat.book()`, `if key not in cache: cache[key] = load()`.

**Critical section:** code that must run by one thread at a time to keep an **invariant** true. Protect the *whole* check+act, not each line separately.

```python
from threading import Lock, Thread

class Counter:
    def __init__(self):
        self.value = 0
        self._lock = Lock()

    def increment_unsafe(self):
        self.value += 1                 # load, add, store: can interleave

    def increment(self):
        with self._lock:                # whole read-modify-write is atomic
            self.value += 1

c = Counter()
threads = [Thread(target=lambda: [c.increment() for _ in range(10_000)]) for _ in range(8)]
for t in threads: t.start()
for t in threads: t.join()
assert c.value == 80_000
```

**Visibility:** in Java, a write by one thread may not be seen by another without synchronization (`volatile`, locks). In CPython, the GIL makes visibility a non-issue in practice, but atomicity is still your job.

**Thread-safe collections aren't enough for compound actions:**
```
# Java: two safe calls, still a race
if (!map.containsKey(k)) map.put(k, v);   // ✗
map.putIfAbsent(k, v);                     // ✓ atomic
map.computeIfAbsent(k, key -> load(key));  // ✓ atomic
```
Python: `dict.setdefault(k, v)` is atomic for builtin dicts under the GIL, but `if k not in d: d[k] = expensive()` is not.

### 2.3 Synchronization primitives

| Primitive | Python | What it does | Use for |
|---|---|---|---|
| Mutex | `threading.Lock` | One holder at a time | Protect shared state |
| Reentrant lock | `threading.RLock` | Same thread can re-acquire | Nested calls that take the same lock |
| Condition variable | `threading.Condition` | Wait until a predicate is true; others `notify` | Blocking queues, "wait for state" |
| Semaphore | `threading.Semaphore(n)` | Up to n holders | Limit concurrency (pools, rate of calls) |
| Bounded semaphore | `BoundedSemaphore` | Same, errors on extra release | Catch release bugs |
| Event | `threading.Event` | One-time flag; `wait()` until `set()` | Start signals, "first step done" |
| Barrier | `threading.Barrier(n)` | n threads wait until all arrive | Phases, H2O problem |
| Countdown latch | (Event + counter, or `Barrier`) | Wait until N things finish | Wait for N workers to be ready |
| Queue | `queue.Queue(maxsize)` | Thread-safe FIFO with blocking put/get | Producer-consumer |
| Atomic / CAS | No general one in Python; Java `AtomicInteger` | Lock-free single-variable updates | Counters, flags |
| Thread-local | `threading.local()` | Per-thread variable | Per-thread DB connection, request context |

**Condition variable rules (most common bug source):**
- Always wait **in a loop** on the predicate (`while not ready: cv.wait()` or `cv.wait_for(pred)`) — wakeups can be spurious, and another thread may have consumed the item first.
- Change the state **and** `notify` while holding the lock.
- `notify()` wakes one waiter; `notify_all()` wakes all (use it when waiters wait for different predicates).

### 2.4 Deadlock, livelock, starvation

**Deadlock:** threads wait for each other forever. Needs all 4 **Coffman conditions**:
1. Mutual exclusion (resources held exclusively)
2. Hold and wait (hold one, wait for another)
3. No preemption (can't take a lock away)
4. Circular wait (A waits for B, B waits for A)

**Break any one to prevent it:**
- **Lock ordering** (breaks circular wait) — always acquire locks in a global order, e.g., by account ID. **The standard answer.**
- **Try-lock with timeout** (breaks hold-and-wait): `lock.acquire(timeout=0.1)`; on failure release everything, back off with jitter, retry.
- **One coarser lock** (fewer locks = fewer cycles).
- Don't call unknown code (callbacks, I/O) while holding a lock.

**Livelock:** threads keep reacting to each other and make no progress (both back off and retry in sync). Fix: **randomized** backoff.

**Starvation:** a thread never gets a turn (e.g., writers starve under a stream of readers). Fix: fairness — writer preference, FIFO queues.

### 2.5 Pessimistic vs optimistic concurrency

| | Pessimistic | Optimistic |
|---|---|---|
| Idea | Lock first, then work | Work, then commit only if nothing changed |
| In code | `with lock:` | Version check / compare-and-set, retry on conflict |
| In DB | `SELECT ... FOR UPDATE` | `UPDATE t SET ..., version = version + 1 WHERE id = ? AND version = ?` |
| Best when | Conflicts are frequent | Conflicts are rare (most web apps) |
| Cost | Waiting, deadlock risk | Retries under contention |

Atomic conditional update (no read first) — often the best of both:
```sql
UPDATE seats SET status = 'BOOKED', booking_id = :b
WHERE show_id = :s AND seat_id = :seat AND status = 'AVAILABLE';
-- 1 row updated → you won; 0 rows → someone else did
```

### 2.6 Thread pools and futures

- Creating a thread per task is expensive and unbounded → use a **pool**: N worker threads pull tasks from a queue.
- `concurrent.futures.ThreadPoolExecutor` (I/O) / `ProcessPoolExecutor` (CPU).
- `submit()` returns a **Future**: `result(timeout)`, `exception()`, `cancel()`, `add_done_callback()`.
- `as_completed(futures)` yields futures as they finish; `wait(..., FIRST_COMPLETED)` for event loops over results.
- Size: I/O-bound → more threads than cores (bounded by downstream limits); CPU-bound → ≈ number of cores (processes).
- **Bound the queue** too, or a burst of tasks will use unbounded memory (backpressure).

### 2.7 asyncio concurrency

You use FastAPI, so expect this.
- One thread, one event loop; tasks switch **only at `await`**.
- No race between two lines with no `await` between them. **But** a check, then an `await`, then an act is a race:
  ```
  if key not in cache:              # task A checks
      cache[key] = await fetch(key) # A suspends; task B also sees "not in cache" → duplicate fetch
  ```
- Primitives: `asyncio.Lock`, `asyncio.Semaphore` (limit concurrent outbound calls), `asyncio.Event`, `asyncio.Queue`, `asyncio.gather`, `asyncio.wait_for` (timeout), `asyncio.TaskGroup` (3.11+).
- **Never block the loop:** `time.sleep`, `requests.get`, CPU-heavy work → use async libraries or `await asyncio.to_thread(fn)`.

```python
import asyncio

async def main():
    limit = asyncio.Semaphore(3)            # at most 3 concurrent "downstream calls"
    in_flight = peak = 0
    inflight_lock = asyncio.Lock()          # not strictly needed here; shows the API
    cache, cache_lock = {}, asyncio.Lock()
    fetches = 0

    async def call(i):
        nonlocal in_flight, peak
        async with limit:
            async with inflight_lock:
                in_flight += 1
                peak = max(peak, in_flight)
            await asyncio.sleep(0.01)
            async with inflight_lock:
                in_flight -= 1
        return i

    async def get(key):
        nonlocal fetches
        async with cache_lock:              # check + fetch + store as one unit
            if key not in cache:
                fetches += 1
                await asyncio.sleep(0.01)
                cache[key] = key.upper()
            return cache[key]

    results = await asyncio.gather(*(call(i) for i in range(10)))
    assert results == list(range(10)) and peak == 3
    assert await asyncio.gather(*(get("a") for _ in range(5))) == ["A"] * 5
    assert fetches == 1

asyncio.run(main())
```

Per-key single-flight (lock per key instead of one global lock) is the production version of `get`.

### 2.8 Python ↔ Java cheat sheet

| Concept | Python | Java |
|---|---|---|
| Mutex | `Lock`, `with lock:` | `synchronized`, `ReentrantLock` |
| Reentrant | `RLock` | `synchronized` / `ReentrantLock` (both reentrant) |
| Wait/notify | `Condition.wait/notify` | `Object.wait/notify`, `Condition.await/signal` |
| Semaphore | `Semaphore` | `Semaphore` |
| Read-write lock | none built in (see [4.12](#412-read-write-lock-from-scratch)) | `ReentrantReadWriteLock`, `StampedLock` |
| Latch / barrier | `Event`, `Barrier` | `CountDownLatch`, `CyclicBarrier` |
| Blocking queue | `queue.Queue` | `ArrayBlockingQueue`, `LinkedBlockingQueue` |
| Concurrent map | `dict` + lock | `ConcurrentHashMap` (fine-grained, lock-free reads) |
| Atomic counter | lock or `itertools.count()` | `AtomicInteger`, `LongAdder` |
| Visibility | GIL (practically) | `volatile`, happens-before |
| Pool | `ThreadPoolExecutor` | `ExecutorService` |
| Future | `concurrent.futures.Future` | `Future`, `CompletableFuture` |

### 2.9 Thread lifecycle, daemon threads, thread-local state

- **Lifecycle:** new → runnable → running → blocked/waiting (lock, I/O, `wait`, `sleep`) → terminated.
- `start()` begins execution; `join(timeout)` waits for it to finish. Always join (or have a shutdown path) — leaked threads hold resources.
- **Daemon threads** are killed abruptly when the main thread exits (no cleanup, no `finally`). Use for best-effort background work only; for anything that must finish (flushing logs, committing work) use a non-daemon thread + explicit shutdown.
- **Context switch:** the OS saves one thread's registers/stack pointer and loads another's. Costs microseconds plus cache misses → thousands of busy threads waste CPU on switching. That's why pools are bounded and why event loops scale to many more connections.
- **Thread stack memory:** each OS thread reserves stack memory (often MBs of virtual memory) → 10K threads is heavy; 10K asyncio tasks is cheap.
- **Thread-local storage:** `threading.local()` gives each thread its own copy of a variable (per-thread DB connection, request context). In asyncio use `contextvars` (task-local).
- **Exceptions in threads** don't propagate to the caller — they're printed and the thread dies. Use `Future`s (executor) to get exceptions back, or `threading.excepthook`.

```python
import threading
from concurrent.futures import ThreadPoolExecutor

local = threading.local()
seen = []
seen_lock = threading.Lock()

def handle(request_id):
    local.request_id = request_id               # visible only in this thread
    work()

def work():
    with seen_lock:
        seen.append((threading.current_thread().name, local.request_id))

threads = [threading.Thread(target=handle, args=(i,), name=f"t{i}") for i in range(3)]
for t in threads: t.start()
for t in threads: t.join()
assert sorted(seen) == [("t0", 0), ("t1", 1), ("t2", 2)]

with ThreadPoolExecutor(2) as pool:
    fut = pool.submit(lambda: 1 / 0)
    assert isinstance(fut.exception(), ZeroDivisionError)   # exceptions come back via the Future
```

### 2.10 Memory model, atomic operations, CAS, lock-free

**Three properties a correct concurrent program needs**
- **Atomicity:** an operation happens all at once or not at all from other threads' view.
- **Visibility:** a write by one thread becomes visible to others.
- **Ordering:** compilers and CPUs may reorder instructions; synchronization prevents harmful reordering.

**Java memory model (asked in Java-heavy interviews)**
- **Happens-before:** if action A happens-before B, B sees A's effects. Established by: unlocking a monitor → later locking it; writing a `volatile` → later reading it; `Thread.start()`; `Thread.join()`.
- `volatile`: visibility + ordering for one variable, **not** atomicity of compound operations (`count++` on a volatile still races).
- `synchronized`/locks: atomicity + visibility.
- Double-checked locking needs the instance field to be `volatile`, otherwise another thread can see a partially constructed object.

**Compare-and-swap (CAS)**
- CPU instruction: "set X to new **only if** X still equals expected; tell me if it worked". The basis of `AtomicInteger`, `ConcurrentHashMap`, lock-free queues.
- Retry loop: read → compute → CAS → if it failed (someone changed it), retry.
- **ABA problem:** value goes A → B → A; CAS thinks nothing changed. Fix with a version/stamp (`AtomicStampedReference`).
- **Lock-free** = some thread always makes progress (no blocking on locks). Hard to write correctly; in interviews, know the idea and use library structures.
- Under heavy contention, CAS loops spin and waste CPU; `LongAdder` (striped counters) beats `AtomicLong` for hot counters.

**Spinlock vs blocking lock**
- Spinlock: busy-waits in a loop — good only for very short critical sections on multi-core (no context switch).
- Blocking lock (mutex): the thread sleeps and the OS wakes it — better for longer waits.

**False sharing (bonus):** two threads updating different variables that sit on the same CPU cache line keep invalidating each other's cache → slow. Fix with padding (Java `@Contended`).

**CAS in Python terms** — there's no user-level CAS, but the *pattern* (optimistic retry on a version) is common in DBs and caches:

```python
from threading import Lock, Thread

class VersionedCell:
    """Simulates compare-and-set with a version. The lock stands in for the hardware's atomic instruction."""
    def __init__(self, value):
        self._value, self._version, self._lock = value, 0, Lock()

    def read(self):
        with self._lock:
            return self._value, self._version

    def compare_and_set(self, expected_version, new_value) -> bool:
        with self._lock:
            if self._version != expected_version:
                return False
            self._value, self._version = new_value, self._version + 1
            return True

def increment(cell: VersionedCell):
    while True:                                   # optimistic retry loop
        value, version = cell.read()
        if cell.compare_and_set(version, value + 1):
            return

cell = VersionedCell(0)
ts = [Thread(target=lambda: [increment(cell) for _ in range(1000)]) for _ in range(4)]
for t in ts: t.start()
for t in ts: t.join()
assert cell.read() == (4000, 4000)
```

### 2.11 Multiprocessing in Python

- Each process has its **own interpreter and GIL** → real parallelism for CPU-bound Python.
- Costs: process startup, memory per process, and **pickling** arguments/results to send them between processes (large data → expensive). Send small inputs; let workers load big data themselves; use `chunksize` for many small tasks.
- **Start methods:** `fork` (copy of the parent, fast; unsafe with threads), `spawn` (fresh interpreter; default on macOS/Windows; requires the `if __name__ == "__main__":` guard and picklable top-level functions), `forkserver`.
- Sharing state: prefer message passing (`multiprocessing.Queue`, return values). Shared memory exists (`Value`, `Array`, `shared_memory`) — needs its own locks. `Manager` objects are proxies (slow, simple).
- `ProcessPoolExecutor` mirrors `ThreadPoolExecutor` → easy switch between I/O-bound and CPU-bound work.

```python
from concurrent.futures import ProcessPoolExecutor
from multiprocessing import Process, Value

def is_prime(n: int) -> bool:
    if n < 2:
        return False
    i = 2
    while i * i <= n:
        if n % i == 0:
            return False
        i += 1
    return True

def add_many(counter, times):
    for _ in range(times):
        with counter.get_lock():                  # shared memory needs a lock too
            counter.value += 1

if __name__ == "__main__":                        # required with the spawn start method
    nums = list(range(10_000, 10_200))
    with ProcessPoolExecutor(max_workers=4) as ex:
        flags = list(ex.map(is_prime, nums, chunksize=50))
    assert sum(flags) == sum(is_prime(n) for n in nums)

    counter = Value("i", 0)
    procs = [Process(target=add_many, args=(counter, 500)) for _ in range(4)]
    for p in procs: p.start()
    for p in procs: p.join()
    assert counter.value == 2000
```

### 2.12 asyncio in depth

**Model:** one thread runs an **event loop**. Coroutines (`async def`) run until they hit an `await` on something not ready (network, sleep, queue), then yield control so the loop runs another ready task. Thousands of concurrent I/O operations on one thread.

**Key pieces**
- `asyncio.run(main())` — create the loop, run, close.
- `asyncio.create_task(coro)` — schedule concurrently (keep a reference, or use a TaskGroup).
- `await asyncio.gather(*coros)` — run concurrently, collect results (first exception propagates unless `return_exceptions=True`).
- `asyncio.TaskGroup()` (3.11+) — structured concurrency: if one task fails, the others are cancelled and the error propagates.
- **Timeouts:** `asyncio.timeout(seconds)` (3.11+) or `asyncio.wait_for(coro, timeout)`.
- **Cancellation:** `task.cancel()` raises `CancelledError` inside the task at its next `await`; clean up in `finally`; don't swallow `CancelledError`.
- **Blocking code:** `time.sleep`, `requests`, heavy CPU → freezes the whole loop. Use async libraries, `await asyncio.to_thread(fn)`, or a process pool.
- **Backpressure:** `asyncio.Queue(maxsize)`; `asyncio.Semaphore(n)` to cap concurrent outbound calls.
- **Races still exist** across `await` points (check → await → act).

```python
import asyncio
import time

async def fetch(i: int, delay: float) -> int:
    await asyncio.sleep(delay)                      # stands in for a network call
    return i

async def producer_consumer() -> list[int]:
    q: asyncio.Queue = asyncio.Queue(maxsize=5)     # bounded → producer waits when full
    out = []

    async def producer():
        for i in range(20):
            await q.put(i)
        for _ in range(3):
            await q.put(None)                       # one stop signal per consumer

    async def consumer():
        while (item := await q.get()) is not None:
            await asyncio.sleep(0.001)
            out.append(item)

    async with asyncio.TaskGroup() as tg:           # all tasks finish (or all cancelled) here
        tg.create_task(producer())
        for _ in range(3):
            tg.create_task(consumer())
    return sorted(out)

async def main():
    start = time.perf_counter()
    results = await asyncio.gather(*(fetch(i, 0.05) for i in range(10)))
    assert results == list(range(10)) and time.perf_counter() - start < 0.3   # concurrent, not 0.5 s

    try:
        async with asyncio.timeout(0.05):
            await fetch(1, 1.0)
        assert False
    except TimeoutError:
        pass

    cleaned = []
    async def worker():
        try:
            await asyncio.sleep(10)
        finally:
            cleaned.append("closed")                # runs on cancellation
    task = asyncio.create_task(worker())
    await asyncio.sleep(0)
    task.cancel()
    try:
        await task
    except asyncio.CancelledError:
        pass
    assert cleaned == ["closed"]

    blocking_result = await asyncio.to_thread(lambda: sum(range(1000)))   # off the event loop
    assert blocking_result == 499500
    assert await producer_consumer() == list(range(20))

asyncio.run(main())
```

### 2.13 How servers handle concurrent requests

| Model | How | Examples | Strengths | Weaknesses |
|---|---|---|---|---|
| Process per worker | N worker processes, each handles requests | Gunicorn sync workers, PHP-FPM | Isolation, uses all cores | Memory per process; one slow request blocks a worker |
| Thread per request (pool) | Worker threads from a pool | Tomcat, Spring MVC, Django under WSGI threads | Simple blocking code | Thread count caps concurrency; memory |
| Event loop | One thread, many connections, non-blocking I/O | Node.js, asyncio/Uvicorn, Nginx | Tens of thousands of connections | Any blocking call stalls everyone; CPU work needs offloading |
| Hybrid | Processes × event loops (× thread pool) | Uvicorn/Gunicorn workers for FastAPI | Cores + many connections | Per-process state isn't shared |

**FastAPI specifics**
- `async def` endpoint → runs on the event loop. Must only `await` async I/O (async DB driver, `httpx.AsyncClient`).
- `def` endpoint → FastAPI runs it in a **thread pool** (default ~40 threads) → blocking libraries are OK, concurrency capped by the pool.
- Calling a blocking function inside `async def` (e.g., `requests.get`) blocks the whole worker → wrap with `await asyncio.to_thread(...)` or switch library.
- `uvicorn --workers 4` → 4 processes, each with its own event loop, memory, caches, and **its own locks and connection pool**.

**Sizing with Little's law:** `concurrency = throughput × latency`.
- 1,000 req/s, each holding a DB connection 20 ms → 20 connections busy on average → pool of ~30 across all workers (with headroom).
- Total DB connections = processes × pool size per process → must stay under the DB's `max_connections`.
- More threads/connections than the bottleneck can serve only adds queueing and contention.

### 2.14 Concurrency across processes and servers

In-process locks protect memory **inside one process**. Production runs many processes on many machines, so shared state needs coordination in a shared system.

**1. Let the database serialize it (best default)**
- **Atomic conditional update:** `UPDATE ... SET qty = qty - 1 WHERE id = ? AND qty > 0` → check rows affected.
- **Unique constraints:** "one booking per seat", "one row per idempotency key" — the DB rejects duplicates, no lock needed.
- **Pessimistic row lock:** `SELECT ... FOR UPDATE` inside a transaction, then update. Lock rows in a consistent order to avoid deadlocks (DBs detect deadlocks and abort one transaction → retry).
- **Optimistic locking:** `version` column; `UPDATE ... WHERE id = ? AND version = ?` → 0 rows = conflict → retry.
- **Work queues in SQL:** `SELECT ... FOR UPDATE SKIP LOCKED LIMIT n` — each worker grabs different rows.
- **Advisory locks** (Postgres `pg_advisory_lock(key)`): app-defined named locks held by a session/transaction — e.g., "only one migration runner".
- **Isolation level:** at `SERIALIZABLE`, the DB aborts conflicting transactions; the app must retry.

```python
import sqlite3

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE seats (id TEXT PRIMARY KEY, status TEXT, booking TEXT, version INTEGER)")
db.execute("INSERT INTO seats VALUES ('A1', 'AVAILABLE', NULL, 0)")
db.execute("CREATE TABLE idempotency (key TEXT PRIMARY KEY, response TEXT)")

def book(seat: str, booking: str) -> bool:
    cur = db.execute(
        "UPDATE seats SET status = 'BOOKED', booking = ?, version = version + 1 "
        "WHERE id = ? AND status = 'AVAILABLE'", (booking, seat))
    db.commit()
    return cur.rowcount == 1                       # 1 → we won; 0 → someone else did

assert book("A1", "b1") is True
assert book("A1", "b2") is False                   # second writer loses, no double booking

def update_optimistic(seat: str, expected_version: int, new_status: str) -> bool:
    cur = db.execute("UPDATE seats SET status = ?, version = version + 1 WHERE id = ? AND version = ?",
                     (new_status, seat, expected_version))
    db.commit()
    return cur.rowcount == 1

_, version = db.execute("SELECT status, version FROM seats WHERE id = 'A1'").fetchone()
assert update_optimistic("A1", version, "CANCELLED") is True
assert update_optimistic("A1", version, "BOOKED") is False      # stale version → retry with a fresh read

def handle_payment(key: str) -> str:
    try:
        db.execute("INSERT INTO idempotency VALUES (?, ?)", (key, "charged"))
        db.commit()
        return "charged"
    except sqlite3.IntegrityError:                 # duplicate request → replay stored response
        return db.execute("SELECT response FROM idempotency WHERE key = ?", (key,)).fetchone()[0] + " (replayed)"

assert handle_payment("k1") == "charged" and handle_payment("k1") == "charged (replayed)"
```

**2. Single writer per key**
- Route all updates for one entity to one consumer: Kafka partition by `account_id`; one actor/goroutine per entity. No locks needed — ordering comes from the partition.

**3. Redis atomic operations**
- Single commands are atomic (`INCR`, `DECRBY`, `SETNX`, `ZADD`); multi-step logic in a **Lua script** runs atomically.
- Typical: rate limit counters, stock pre-decrement in flash sales, "first request wins" flags.

**4. Distributed locks (use sparingly)**
- Redis: `SET lock:{name} {random_token} NX PX 30000`; release only if the value still equals your token (Lua compare-and-delete).
- A paused process (GC, VM freeze) can outlive its lease → two holders. Protect the resource with **fencing tokens** (monotonic number checked by the storage) or use etcd/ZooKeeper leases.
- Prefer constraints/conditional updates over distributed locks when possible.

**5. Idempotency**
- Retries are everywhere (client, load balancer, queue redelivery). Make every write safe to repeat: idempotency keys, upserts, unique constraints, version checks.

### 2.15 Common concurrency bugs (checklist)

| Bug | Smell | Fix |
|---|---|---|
| Lost update | `x = read(); write(x + 1)` without a lock | Lock, atomic op, or conditional update |
| Check-then-act | `if free: take()` | Make check + act one atomic step |
| Partial update on failure | Mutating step 1, exception at step 2 | Validate first, commit last; or rollback |
| Deadlock | Two locks taken in different orders | Global lock order; timeouts |
| Holding a lock during I/O | Network call inside `with lock:` | Copy what you need, release, then do I/O |
| Calling callbacks under a lock | Listener re-enters and takes the same lock | Snapshot listeners, call outside the lock |
| Missed wakeup | `if not ready: cv.wait()` | `while not ready` / `wait_for(pred)` |
| Unbounded queue | Memory grows under load | Bounded queue + backpressure policy |
| Leaked resources | Exception skips `release()` | `with` / `try-finally` / context managers |
| Thread dies silently | Exception in a thread | Futures, `excepthook`, supervision |
| Blocking the event loop | Sync I/O in `async def` | Async libraries, `to_thread` |
| Per-process lock in a multi-worker app | Works locally, fails in prod | Shared coordination (DB/Redis) |

### 2.16 Testing and debugging concurrent code

- **Maximize overlap:** start threads behind a `Barrier` so they hit the critical section together.
- **Assert invariants,** not exact interleavings: totals conserved, no double booking, counts match.
- **Repeat** many iterations; run the test in a loop in CI.
- **Detect deadlocks:** `join(timeout)` then assert `not t.is_alive()`; dump all stacks with `faulthandler.dump_traceback_later(10)` or `py-spy dump --pid`.
- **Deterministic logic:** inject clocks and keep the core logic single-threaded and testable; add threads at the edges.
- **Tools:** Java `jstack` / thread dumps, ThreadSanitizer for C/C++/Go (`-race` in Go), lock contention profilers.

---

## 3. LeetCode concurrency set

Signatures follow LeetCode's Python versions. Each block includes a test harness.

### 3.1 LC 1114 — Print in Order

Three threads call `first`, `second`, `third` in any order; output must be first, second, third.

**Idea:** two `Event`s as "step done" signals.

```python
from threading import Event, Thread

class Foo:
    def __init__(self):
        self._first_done = Event()
        self._second_done = Event()

    def first(self, printFirst):
        printFirst()
        self._first_done.set()

    def second(self, printSecond):
        self._first_done.wait()
        printSecond()
        self._second_done.set()

    def third(self, printThird):
        self._second_done.wait()
        printThird()

out = []
foo = Foo()
threads = [Thread(target=foo.third, args=(lambda: out.append("third"),)),
           Thread(target=foo.second, args=(lambda: out.append("second"),)),
           Thread(target=foo.first, args=(lambda: out.append("first"),))]
for t in threads: t.start()
for t in threads: t.join(2)
assert out == ["first", "second", "third"]
```

### 3.2 LC 1115 — Print FooBar Alternately

**Idea:** two semaphores passing a "turn" back and forth. `foo` starts with 1 permit, `bar` with 0.

```python
from threading import Semaphore, Thread

class FooBar:
    def __init__(self, n):
        self.n = n
        self._foo, self._bar = Semaphore(1), Semaphore(0)

    def foo(self, printFoo):
        for _ in range(self.n):
            self._foo.acquire()
            printFoo()
            self._bar.release()

    def bar(self, printBar):
        for _ in range(self.n):
            self._bar.acquire()
            printBar()
            self._foo.release()

out = []
fb = FooBar(5)
ts = [Thread(target=fb.bar, args=(lambda: out.append("bar"),)),
      Thread(target=fb.foo, args=(lambda: out.append("foo"),))]
for t in ts: t.start()
for t in ts: t.join(2)
assert "".join(out) == "foobar" * 5
```

### 3.3 LC 1116 — Print Zero Even Odd

Output `0102030405...` with three threads (zero, even, odd).

**Idea:** `zero` prints 0 then hands the turn to `odd` or `even` depending on the next number; they hand it back to `zero`.

```python
from threading import Semaphore, Thread

class ZeroEvenOdd:
    def __init__(self, n):
        self.n = n
        self._zero, self._odd, self._even = Semaphore(1), Semaphore(0), Semaphore(0)

    def zero(self, printNumber):
        for i in range(1, self.n + 1):
            self._zero.acquire()
            printNumber(0)
            (self._odd if i % 2 else self._even).release()

    def even(self, printNumber):
        for i in range(2, self.n + 1, 2):
            self._even.acquire()
            printNumber(i)
            self._zero.release()

    def odd(self, printNumber):
        for i in range(1, self.n + 1, 2):
            self._odd.acquire()
            printNumber(i)
            self._zero.release()

out = []
z = ZeroEvenOdd(5)
ts = [Thread(target=getattr(z, m), args=(lambda x: out.append(str(x)),)) for m in ("even", "odd", "zero")]
for t in ts: t.start()
for t in ts: t.join(2)
assert "".join(out) == "0102030405"
```

### 3.4 LC 1117 — Building H2O

Hydrogen and oxygen threads arrive in any order; they must leave in groups of exactly 2 H + 1 O.

**Idea:** semaphores admit at most 2 H and 1 O into the "current molecule"; a `Barrier(3)` makes the three wait for each other. Permits are released only after they've left, so the next molecule can't mix in.

```python
from threading import Barrier, Semaphore, Thread

class H2O:
    def __init__(self):
        self._h, self._o = Semaphore(2), Semaphore(1)
        self._barrier = Barrier(3)

    def hydrogen(self, releaseHydrogen):
        with self._h:
            self._barrier.wait()
            releaseHydrogen()

    def oxygen(self, releaseOxygen):
        with self._o:
            self._barrier.wait()
            releaseOxygen()

out = []
h2o = H2O()
water = "OOHHHHHHO"   # 3 molecules' worth, arbitrary arrival order
ts = [Thread(target=h2o.hydrogen if c == "H" else h2o.oxygen,
             args=(lambda c=c: out.append(c),)) for c in water]
for t in ts: t.start()
for t in ts: t.join(2)
groups = ["".join(sorted(out[i:i + 3])) for i in range(0, len(out), 3)]
assert groups == ["HHO"] * 3
```

### 3.5 LC 1188 — Design Bounded Blocking Queue

`enqueue` blocks when full, `dequeue` blocks when empty, `size()`.

**Idea:** one lock, two conditions (`not_full`, `not_empty`). Wait in a loop on the predicate. A fuller version with timeouts and shutdown is in [4.2](#42-bounded-blocking-queue--producer-consumer).

```python
from collections import deque
from threading import Condition, Lock, Thread

class BoundedBlockingQueue:
    def __init__(self, capacity: int):
        self._items = deque()
        self._cap = capacity
        lock = Lock()
        self._not_full = Condition(lock)
        self._not_empty = Condition(lock)

    def enqueue(self, element: int) -> None:
        with self._not_full:
            self._not_full.wait_for(lambda: len(self._items) < self._cap)
            self._items.append(element)
            self._not_empty.notify()

    def dequeue(self) -> int:
        with self._not_empty:
            self._not_empty.wait_for(lambda: self._items)
            item = self._items.popleft()
            self._not_full.notify()
            return item

    def size(self) -> int:
        with self._not_full:
            return len(self._items)

q = BoundedBlockingQueue(2)
got = []
producer = Thread(target=lambda: [q.enqueue(i) for i in range(100)])
consumer = Thread(target=lambda: [got.append(q.dequeue()) for _ in range(100)])
producer.start(); consumer.start()
producer.join(5); consumer.join(5)
assert got == list(range(100)) and q.size() == 0
```

### 3.6 LC 1195 — Fizz Buzz Multithreaded

Four threads: `fizz`, `buzz`, `fizzbuzz`, `number`. Together they print the FizzBuzz sequence 1..n.

**Idea:** a shared counter `i` + one `Condition`. Each thread waits until `i` is "its" kind of number (or done), prints, increments, and `notify_all()` (waiters have different predicates).

```python
from threading import Condition, Thread

class FizzBuzz:
    def __init__(self, n: int):
        self.n, self._i = n, 1
        self._cv = Condition()

    def _run(self, mine, act):
        with self._cv:
            while True:
                self._cv.wait_for(lambda: self._i > self.n or mine(self._i))
                if self._i > self.n:
                    return
                act(self._i)
                self._i += 1
                self._cv.notify_all()

    def fizz(self, printFizz):
        self._run(lambda i: i % 3 == 0 and i % 5 != 0, lambda i: printFizz())

    def buzz(self, printBuzz):
        self._run(lambda i: i % 5 == 0 and i % 3 != 0, lambda i: printBuzz())

    def fizzbuzz(self, printFizzBuzz):
        self._run(lambda i: i % 15 == 0, lambda i: printFizzBuzz())

    def number(self, printNumber):
        self._run(lambda i: i % 3 != 0 and i % 5 != 0, printNumber)

out = []
fb = FizzBuzz(15)
ts = [Thread(target=fb.fizz, args=(lambda: out.append("fizz"),)),
      Thread(target=fb.buzz, args=(lambda: out.append("buzz"),)),
      Thread(target=fb.fizzbuzz, args=(lambda: out.append("fizzbuzz"),)),
      Thread(target=fb.number, args=(lambda x: out.append(str(x)),))]
for t in ts: t.start()
for t in ts: t.join(2)
expected = ["fizzbuzz" if i % 15 == 0 else "fizz" if i % 3 == 0 else "buzz" if i % 5 == 0 else str(i)
            for i in range(1, 16)]
assert out == expected
```

### 3.7 LC 1226 — The Dining Philosophers

5 philosophers, 5 forks; each needs both neighboring forks to eat. Naive "pick left, then right" deadlocks when all pick left at once.

**Fixes (know all three):**
1. **Lock ordering:** always pick the lower-numbered fork first → no cycle. *(used below)*
2. **Limit diners:** `Semaphore(4)` — at most 4 try at once, so one always gets both forks.
3. **Asymmetry:** odd philosophers pick left first, even pick right first.

```python
from threading import Lock, Thread

class DiningPhilosophers:
    def __init__(self):
        self._forks = [Lock() for _ in range(5)]

    def wantsToEat(self, philosopher, pickLeftFork, pickRightFork, eat, putLeftFork, putRightFork):
        left, right = philosopher, (philosopher + 1) % 5
        first, second = sorted((left, right))          # global order breaks the cycle
        with self._forks[first], self._forks[second]:
            pickLeftFork(); pickRightFork()
            eat()
            putLeftFork(); putRightFork()

dp = DiningPhilosophers()
meals = [0] * 5
def dine(p):
    for _ in range(50):
        dp.wantsToEat(p, *[lambda: None] * 2, lambda p=p: meals.__setitem__(p, meals[p] + 1),
                      *[lambda: None] * 2)
ts = [Thread(target=dine, args=(p,)) for p in range(5)]
for t in ts: t.start()
for t in ts: t.join(5)
assert not any(t.is_alive() for t in ts) and meals == [50] * 5
```

### 3.8 LC 1242 — Web Crawler Multithreaded

Crawl all URLs reachable from `startUrl` with the **same hostname**, using `htmlParser.getUrls(url)` (slow, blocking). Do it concurrently.

**Idea:** a thread pool runs the slow `getUrls` calls; the **main thread alone** owns the `seen` set (no lock needed) and submits new URLs as results arrive (`wait(FIRST_COMPLETED)`).

```python
import time
from concurrent.futures import FIRST_COMPLETED, ThreadPoolExecutor, wait
from urllib.parse import urlsplit

class Solution:
    def crawl(self, startUrl: str, htmlParser) -> list[str]:
        host = urlsplit(startUrl).hostname
        seen = {startUrl}
        with ThreadPoolExecutor(max_workers=8) as pool:
            pending = {pool.submit(htmlParser.getUrls, startUrl)}
            while pending:
                done, pending = wait(pending, return_when=FIRST_COMPLETED)
                for fut in done:
                    for url in fut.result():
                        if url not in seen and urlsplit(url).hostname == host:
                            seen.add(url)
                            pending.add(pool.submit(htmlParser.getUrls, url))
        return list(seen)

class FakeParser:
    graph = {
        "http://news.yahoo.com/news/topics/": ["http://news.yahoo.com/news", "http://news.google.com"],
        "http://news.yahoo.com/news": ["http://news.yahoo.com/us", "http://news.yahoo.com/news/topics/"],
        "http://news.yahoo.com/us": [],
        "http://news.google.com": ["http://news.yahoo.com/secret"],
    }
    def getUrls(self, url):
        time.sleep(0.01)
        return self.graph.get(url, [])

result = Solution().crawl("http://news.yahoo.com/news/topics/", FakeParser())
assert sorted(result) == ["http://news.yahoo.com/news", "http://news.yahoo.com/news/topics/",
                          "http://news.yahoo.com/us"]
```

**Follow-ups:** politeness per host (semaphore per host) · distributed crawl (HLD guide 23.2) · why not let workers update `seen`? You can, with a lock, but single-owner state is simpler and race-free.

---

## 4. Design + implement problems

### 4.1 Thread-safe singleton

**Problem:** two threads call `get_instance()` at the same time on first use → two instances.

**Options**
- **Module-level instance** (Python's simplest; imports run once per process under the import lock).
- **Eager init** at class definition.
- **Double-checked locking:** check without the lock (fast path), lock, check again, create.
  - In Java the field must be `volatile` or another thread can see a half-constructed object.

```python
from threading import Barrier, Lock, Thread

class ConfigRegistry:
    _instance = None
    _lock = Lock()
    created = 0

    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:
                    cls.created += 1
                    cls._instance = super().__new__(cls)
        return cls._instance

start = Barrier(20)
seen = []
def worker():
    start.wait()                         # release all threads at once to provoke the race
    seen.append(ConfigRegistry())
ts = [Thread(target=worker) for _ in range(20)]
for t in ts: t.start()
for t in ts: t.join(2)
assert ConfigRegistry.created == 1 and all(s is seen[0] for s in seen)
```

**Follow-up:** "Why are singletons discouraged?" Hidden global state, hard to test. Prefer creating one instance at startup and injecting it.

### 4.2 Bounded blocking queue / producer-consumer

**Problem:** producers generate work faster or slower than consumers process it. Need a buffer that blocks producers when full (**backpressure**) and blocks consumers when empty, plus clean **shutdown**.

**Design**
- `deque` + one `Lock` + two `Condition`s.
- `put(item, timeout)` / `get(timeout)` raise `TimeoutError` instead of hanging forever.
- `close()`: wakes everyone; `put` after close fails; `get` drains remaining items, then raises `Closed` → consumers exit their loop.

```python
from collections import deque
from threading import Condition, Lock, Thread

class Closed(Exception): ...

class BlockingQueue:
    def __init__(self, capacity: int):
        if capacity <= 0:
            raise ValueError("capacity must be positive")
        self._items, self._cap, self._closed = deque(), capacity, False
        self._lock = Lock()
        self._not_full = Condition(self._lock)
        self._not_empty = Condition(self._lock)

    def put(self, item, timeout: float | None = None) -> None:
        with self._not_full:
            if not self._not_full.wait_for(lambda: self._closed or len(self._items) < self._cap, timeout):
                raise TimeoutError("queue full")
            if self._closed:
                raise Closed()
            self._items.append(item)
            self._not_empty.notify()

    def get(self, timeout: float | None = None):
        with self._not_empty:
            if not self._not_empty.wait_for(lambda: self._items or self._closed, timeout):
                raise TimeoutError("queue empty")
            if not self._items:              # closed and fully drained
                raise Closed()
            item = self._items.popleft()
            self._not_full.notify()
            return item

    def close(self) -> None:
        with self._lock:
            self._closed = True
            self._not_full.notify_all()
            self._not_empty.notify_all()

q = BlockingQueue(5)
consumed, consumed_lock = [], Lock()

def producer(pid):
    for i in range(100):
        q.put(pid * 1000 + i)

def consumer():
    while True:
        try:
            item = q.get()
        except Closed:
            return
        with consumed_lock:
            consumed.append(item)

producers = [Thread(target=producer, args=(p,)) for p in range(3)]
consumers = [Thread(target=consumer) for _ in range(2)]
for t in producers + consumers: t.start()
for t in producers: t.join(5)
q.close()                                    # all produced -> let consumers drain and exit
for t in consumers: t.join(5)
assert sorted(consumed) == sorted(p * 1000 + i for p in range(3) for i in range(100))
try:
    BlockingQueue(1).get(timeout=0.01); assert False
except TimeoutError:
    pass
```

**Follow-ups**
- *Why `wait_for` (loop) and not a single `wait`?* Spurious wakeups; another consumer may take the item first.
- *Why two conditions?* Producers wait for "not full", consumers for "not empty"; a single condition would need `notify_all` and wake the wrong side.
- *Full-queue policy alternatives:* block (backpressure), drop newest/oldest, or reject with an error (load shedding).

### 4.3 Thread-safe LRU cache

**Problem:** LRU `get` *moves the key to the front*, so even reads mutate shared structure. Concurrent access corrupts the linked list/`OrderedDict`.

**Options**
1. **One lock around every operation.** Simple, correct. Bottleneck under heavy contention.
2. **Read-write lock?** Doesn't help: `get` is a write (recency update).
3. **Segmented (striped) cache:** N independent LRU segments, each with its own lock; `segment = hash(key) % N`. Concurrency ≈ N. Tradeoff: eviction is per segment (approximate global LRU).
4. Production caches (Caffeine in Java) buffer recency updates and apply them in batches.

```python
from collections import OrderedDict
from threading import Lock, Thread

class LockedLRU:
    def __init__(self, capacity: int):
        self._cap, self._data, self._lock = capacity, OrderedDict(), Lock()

    def get(self, key):
        with self._lock:
            if key not in self._data:
                return None
            self._data.move_to_end(key)
            return self._data[key]

    def put(self, key, value) -> None:
        with self._lock:
            self._data[key] = value
            self._data.move_to_end(key)
            if len(self._data) > self._cap:
                self._data.popitem(last=False)

    def __len__(self):
        with self._lock:
            return len(self._data)

class SegmentedLRU:
    def __init__(self, capacity: int, segments: int = 16):
        per = max(1, capacity // segments)
        self._segments = [LockedLRU(per) for _ in range(segments)]

    def _seg(self, key):
        return self._segments[hash(key) % len(self._segments)]

    def get(self, key):
        return self._seg(key).get(key)

    def put(self, key, value) -> None:
        self._seg(key).put(key, value)

    def __len__(self):
        return sum(len(s) for s in self._segments)

for cache in (LockedLRU(100), SegmentedLRU(128, segments=8)):
    def hammer(tid, cache=cache):
        for i in range(2000):
            key = (tid * 7 + i) % 300
            if cache.get(key) is None:
                cache.put(key, key * 2)
            else:
                assert cache.get(key) in (None, key * 2)   # may be evicted in between
    ts = [Thread(target=hammer, args=(t,)) for t in range(8)]
    for t in ts: t.start()
    for t in ts: t.join(10)
    assert len(cache) <= 128

lru = LockedLRU(2)
lru.put("a", 1); lru.put("b", 2); lru.get("a"); lru.put("c", 3)
assert lru.get("b") is None and lru.get("a") == 1
```

**Follow-up: cache stampede in-process** — many threads miss the same key and all call the slow loader. Fix with **per-key single-flight**: the first thread loads; others wait on that key's `Event`/`Future` and reuse its result.

### 4.4 Thread-safe rate limiter

**Problem:** token bucket `allow()` is read-modify-write on `tokens`; concurrent calls over-admit. One global lock serializes unrelated clients.

**Design**
- Each bucket has **its own lock** → clients don't block each other.
- The registry (`client → bucket`) has a small lock only for creation; use `setdefault` so two threads can't install two buckets for one client.

```python
from threading import Barrier, Lock, Thread
from time import monotonic

class TokenBucket:
    def __init__(self, capacity: int, rate: float, clock=monotonic):
        self._cap, self._rate, self._clock = capacity, rate, clock
        self._tokens, self._last = float(capacity), clock()
        self._lock = Lock()

    def try_acquire(self) -> bool:
        with self._lock:
            now = self._clock()
            self._tokens = min(self._cap, self._tokens + (now - self._last) * self._rate)
            self._last = now
            if self._tokens >= 1:
                self._tokens -= 1
                return True
            return False

class RateLimiter:
    def __init__(self, capacity: int, rate: float, clock=monotonic):
        self._args = (capacity, rate, clock)
        self._buckets: dict[str, TokenBucket] = {}
        self._registry_lock = Lock()

    def allow(self, client: str) -> bool:
        bucket = self._buckets.get(client)
        if bucket is None:
            with self._registry_lock:
                bucket = self._buckets.setdefault(client, TokenBucket(*self._args))
        return bucket.try_acquire()

frozen = [0.0]
rl = RateLimiter(capacity=50, rate=1, clock=lambda: frozen[0])   # no refill while time is frozen
start = Barrier(10)
allowed, count_lock = [0], Lock()

def burst():
    start.wait()
    ok = sum(rl.allow("same-client") for _ in range(20))
    with count_lock:
        allowed[0] += ok

ts = [Thread(target=burst) for _ in range(10)]
for t in ts: t.start()
for t in ts: t.join(5)
assert allowed[0] == 50                       # 200 attempts, exactly capacity admitted
```

**Follow-ups:** many servers → Redis + Lua (HLD guide 22.2) · idle buckets → evict by last access · fairness between clients → per-client buckets already isolate them.

### 4.5 Seat booking without double booking

**Problem:** `if all seats free: mark them booked` is check-then-act across several seats. Two users can both pass the check.

**In-process:** one lock around check + mark, **all-or-nothing** (validate every seat before changing any). For high throughput: a lock per show (different shows don't contend).

**Multi-server:** in-process locks don't help. Use the DB: a conditional `UPDATE ... WHERE status='AVAILABLE'` and check that the row count equals the number of seats, inside a transaction (see [HLD 22.9](hld-interview-guide.md#229-ticket-booking-bookmyshow--ticketmaster)).

```python
from collections import defaultdict
from threading import Barrier, Lock, Thread

class SeatUnavailable(Exception): ...

class ShowBooking:
    def __init__(self, seats):
        self._owner = {s: None for s in seats}
        self._lock = Lock()

    def book(self, user: str, seats: list[str]) -> None:
        with self._lock:
            taken = [s for s in seats if self._owner.get(s, "missing") is not None]
            if taken:
                raise SeatUnavailable(taken)          # nothing changed
            for s in seats:
                self._owner[s] = user

    def owner(self, seat):
        with self._lock:
            return self._owner[seat]

class BookingService:
    """Lock per show: bookings for different shows never wait on each other."""
    def __init__(self):
        self._shows: dict[str, ShowBooking] = {}

    def add_show(self, show_id, seats):
        self._shows[show_id] = ShowBooking(seats)

    def book(self, show_id, user, seats):
        self._shows[show_id].book(user, seats)

svc = BookingService()
svc.add_show("s1", ["A1", "A2", "A3"])
start = Barrier(50)
wins = defaultdict(int)
wins_lock = Lock()

def attempt(uid):
    start.wait()
    try:
        svc.book("s1", f"u{uid}", ["A2", "A3"] if uid % 2 else ["A1", "A2"])
        with wins_lock:
            wins[uid] += 1
    except SeatUnavailable:
        pass

ts = [Thread(target=attempt, args=(i,)) for i in range(50)]
for t in ts: t.start()
for t in ts: t.join(5)
assert sum(wins.values()) == 1                # A2 overlaps every request: exactly one winner
```

**Follow-ups:** holds with expiry (store `held_until`, treat expired as free) · fairness (a queue/waiting room) · why not a lock per seat? Multi-seat requests would need several locks → must take them in sorted order to avoid deadlock; a per-show lock is simpler and enough.

### 4.6 Thread pool from scratch

**Problem:** implement `submit(fn, *args) → Future` and `shutdown(wait=True)` with N worker threads.

**Design**
- Task queue (`queue.Queue`), N workers looping on `get()`.
- Each task carries a `concurrent.futures.Future`; the worker sets the result or exception.
- Shutdown: stop accepting tasks, push one **sentinel** (`None`) per worker *after* queued tasks so they drain first, then join.

```python
import queue
from concurrent.futures import Future
from threading import Lock, Thread, current_thread

class ThreadPool:
    def __init__(self, workers: int):
        self._tasks: queue.Queue = queue.Queue()
        self._lock = Lock()
        self._shutdown = False
        self._workers = [Thread(target=self._work, daemon=True, name=f"worker-{i}") for i in range(workers)]
        for w in self._workers:
            w.start()

    def submit(self, fn, *args, **kwargs) -> Future:
        with self._lock:                       # no submit can slip in after shutdown's sentinels
            if self._shutdown:
                raise RuntimeError("pool is shut down")
            fut = Future()
            self._tasks.put((fut, fn, args, kwargs))
            return fut

    def _work(self):
        while True:
            task = self._tasks.get()
            if task is None:
                return
            fut, fn, args, kwargs = task
            if not fut.set_running_or_notify_cancel():   # cancelled before it started
                continue
            try:
                fut.set_result(fn(*args, **kwargs))
            except BaseException as exc:
                fut.set_exception(exc)

    def shutdown(self, wait: bool = True) -> None:
        with self._lock:
            if self._shutdown:
                return
            self._shutdown = True
            for _ in self._workers:
                self._tasks.put(None)
        if wait:
            for w in self._workers:
                w.join()

pool = ThreadPool(4)
futures = [pool.submit(lambda x: (x * x, current_thread().name), i) for i in range(20)]
bad = pool.submit(lambda: 1 / 0)
assert [f.result(timeout=2)[0] for f in futures] == [i * i for i in range(20)]
assert len({f.result()[1] for f in futures}) <= 4
assert isinstance(bad.exception(timeout=2), ZeroDivisionError)
pool.shutdown()
try:
    pool.submit(print); assert False
except RuntimeError:
    pass
```

**Follow-ups:** bounded task queue + rejection policy (block caller / reject / run in caller thread) · dynamic sizing (core vs max threads) · per-task timeouts (`future.result(timeout)` on the caller side; truly killing a running Python thread isn't possible — design tasks to check a cancel flag).

### 4.7 Delayed task scheduler

**Problem:** `schedule(fn, delay)`, `schedule_every(fn, interval)`, `cancel(id)`; run each task at its time. Like `ScheduledExecutorService` / a cron runner.

**Design**
- Min-heap of `(run_at, task_id, fn, interval)`; a scheduler thread waits on a `Condition` **with a timeout = time until the earliest task**.
- New task earlier than the current head → `notify()` so the thread re-computes its wait.
- `cancel` is lazy: mark the ID; skip it when popped.
- Run tasks **outside the lock** (and ideally on a worker pool, so a slow task doesn't delay others).

```python
import heapq
import time
from itertools import count
from threading import Condition, Thread

class TaskScheduler:
    def __init__(self, clock=time.monotonic):
        self._clock = clock
        self._heap: list = []
        self._cv = Condition()
        self._ids = count(1)
        self._cancelled: set[int] = set()
        self._running = True
        self._thread = Thread(target=self._loop, daemon=True)
        self._thread.start()

    def schedule(self, fn, delay: float, interval: float | None = None) -> int:
        with self._cv:
            task_id = next(self._ids)
            heapq.heappush(self._heap, (self._clock() + delay, task_id, fn, interval))
            self._cv.notify()                     # head may have changed
            return task_id

    def schedule_every(self, fn, interval: float) -> int:
        return self.schedule(fn, 0, interval)

    def cancel(self, task_id: int) -> None:
        with self._cv:
            self._cancelled.add(task_id)

    def _next_due(self):
        """Wait until a task is due; return it, or None on shutdown. Lock is held."""
        while self._running:
            if not self._heap:
                self._cv.wait()
                continue
            run_at, task_id, fn, interval = self._heap[0]
            wait = run_at - self._clock()
            if wait > 0:
                self._cv.wait(wait)
                continue
            heapq.heappop(self._heap)
            if task_id in self._cancelled:
                self._cancelled.discard(task_id)
                continue
            if interval:
                heapq.heappush(self._heap, (run_at + interval, task_id, fn, interval))
            return fn
        return None

    def _loop(self):
        while True:
            with self._cv:
                fn = self._next_due()
            if fn is None:
                return
            try:
                fn()                              # outside the lock
            except Exception as exc:              # one bad task must not kill the scheduler
                print("task failed:", exc)

    def shutdown(self):
        with self._cv:
            self._running = False
            self._cv.notify_all()
        self._thread.join(2)

events = []
s = TaskScheduler()
s.schedule(lambda: events.append("b"), 0.10)
s.schedule(lambda: events.append("a"), 0.05)
c = s.schedule(lambda: events.append("c"), 0.15)
s.cancel(c)
tick = s.schedule_every(lambda: events.append("tick"), 0.04)
time.sleep(0.3)
s.cancel(tick)
s.shutdown()
assert events.index("a") < events.index("b") and "c" not in events
assert events.count("tick") >= 4
```

**Follow-ups:** persistence/restarts → store tasks in a DB, poll due ones (HLD guide 23.4) · fixed-rate vs fixed-delay (next run from scheduled time vs from completion) · many schedulers → claim tasks atomically (`SELECT ... FOR UPDATE SKIP LOCKED`).

### 4.8 Connection pool

**Problem:** creating DB connections is slow, and the DB allows a limited number. Reuse up to `max_size` connections; callers wait (with timeout) when all are busy; broken connections get replaced.

**Design**
- `Semaphore(max_size)` limits how many are **checked out** at once.
- `idle` deque (under a lock) holds connections ready for reuse; create lazily when idle is empty.
- Validate on borrow (`is_alive`), discard broken ones.
- Context manager guarantees return even if the caller raises (no leaks).

```python
import time
from collections import deque
from contextlib import contextmanager
from threading import Lock, Semaphore, Thread

class PoolTimeout(Exception): ...

class ConnectionPool:
    def __init__(self, factory, max_size: int, is_alive=lambda c: True):
        self._factory, self._is_alive = factory, is_alive
        self._permits = Semaphore(max_size)
        self._idle: deque = deque()
        self._lock = Lock()

    def acquire(self, timeout: float | None = None):
        if not self._permits.acquire(timeout=timeout):
            raise PoolTimeout("no connection available")
        try:
            while True:
                with self._lock:
                    conn = self._idle.popleft() if self._idle else None
                if conn is None:
                    return self._factory()           # create outside the lock (slow)
                if self._is_alive(conn):
                    return conn
                # broken idle connection: drop it and try the next one
        except BaseException:
            self._permits.release()                  # factory failed: give the permit back
            raise

    def release(self, conn, broken: bool = False) -> None:
        if not broken:
            with self._lock:
                self._idle.append(conn)
        self._permits.release()

    @contextmanager
    def connection(self, timeout: float | None = None):
        conn = self.acquire(timeout)
        try:
            yield conn
        except Exception:
            self.release(conn, broken=True)          # conservative: don't reuse after an error
            raise
        else:
            self.release(conn)

created, stats_lock = [0], Lock()
in_use, peak = [0], [0]

def factory():
    with stats_lock:
        created[0] += 1
        return {"id": created[0]}

pool = ConnectionPool(factory, max_size=2)

def query():
    with pool.connection(timeout=2) as conn:
        with stats_lock:
            in_use[0] += 1
            peak[0] = max(peak[0], in_use[0])
        time.sleep(0.02)
        with stats_lock:
            in_use[0] -= 1

ts = [Thread(target=query) for _ in range(8)]
for t in ts: t.start()
for t in ts: t.join(5)
assert peak[0] <= 2 and created[0] <= 2

with pool.connection() as c1, pool.connection() as c2:
    try:
        pool.acquire(timeout=0.01); assert False
    except PoolTimeout:
        pass
```

**Follow-ups:** idle timeout/max lifetime (close old connections) · min idle (pre-warm) · pool size math: `workers × pool_size ≤ DB max_connections` — every app process has its own pool (see [2.13](#213-how-servers-handle-concurrent-requests)) · async pools (`asyncpg.create_pool`).

### 4.9 In-memory pub-sub with concurrent subscribers

**Problem:** publishers push messages to a topic; each subscriber processes them on its own. A slow subscriber must **not** block the publisher or other subscribers.

**Design**
- Each subscriber gets its **own bounded queue + its own worker thread** (isolation).
- `publish` does a non-blocking `put_nowait` to each queue; on full → apply a policy (here: drop and count; alternatives: block, drop oldest).
- Callback exceptions are caught so one bad message doesn't kill the subscriber thread.
- `close()` sends a sentinel to each subscriber and joins.

```python
import queue
import time
from threading import Lock, Thread

_STOP = object()

class Subscriber:
    def __init__(self, name, handler, capacity):
        self.name, self._handler = name, handler
        self.queue: queue.Queue = queue.Queue(capacity)
        self.dropped = 0
        self.thread = Thread(target=self._run, daemon=True)
        self.thread.start()

    def _run(self):
        while True:
            msg = self.queue.get()
            if msg is _STOP:
                return
            try:
                self._handler(msg)
            except Exception as exc:
                print(f"{self.name} failed on {msg!r}: {exc}")

class PubSub:
    def __init__(self):
        self._topics: dict[str, list[Subscriber]] = {}
        self._lock = Lock()

    def subscribe(self, topic, name, handler, capacity=100) -> Subscriber:
        sub = Subscriber(name, handler, capacity)
        with self._lock:
            self._topics.setdefault(topic, []).append(sub)
        return sub

    def publish(self, topic, msg) -> None:
        with self._lock:
            subs = list(self._topics.get(topic, ()))   # snapshot; don't hold the lock while delivering
        for sub in subs:
            try:
                sub.queue.put_nowait(msg)
            except queue.Full:
                sub.dropped += 1                       # policy: drop for slow consumers

    def close(self) -> None:
        with self._lock:
            subs = [s for group in self._topics.values() for s in group]
        for s in subs:
            s.queue.put(_STOP)
        for s in subs:
            s.thread.join(2)

fast_seen, slow_seen = [], []
bus = PubSub()
bus.subscribe("orders", "fast", fast_seen.append)
slow = bus.subscribe("orders", "slow", lambda m: (time.sleep(0.01), slow_seen.append(m)), capacity=5)
started = time.monotonic()
for i in range(50):
    bus.publish("orders", i)
assert time.monotonic() - started < 0.2           # publisher never waited on the slow subscriber
bus.close()
assert fast_seen == list(range(50))
assert slow.dropped > 0 and len(slow_seen) + slow.dropped == 50
```

**Follow-ups:** guaranteed delivery → don't drop; persist to a log and let subscribers track offsets (see [LLD 10.10](lld-interview-guide.md#1010-notification--pub-sub-system)) · ordering per key with parallelism → several worker threads, route by `hash(key) % workers` · retries with backoff → re-enqueue with attempt count, DLQ after N.

### 4.10 Hit counter (concurrent)

**Problem:** `hit(timestamp)` and `get_hits(timestamp)` = hits in the last 300 seconds; many threads call `hit` concurrently. (LeetCode 362 + concurrency follow-up.)

**Design**
- 300 circular buckets: `times[i]`, `counts[i]` where `i = ts % 300`. If `times[i] != ts`, the bucket is stale → reset it.
- O(1) `hit`, O(300) `get_hits`, constant memory regardless of traffic.
- A lock protects the bucket update (read-modify-write). For extreme rates, stripe counters per thread and sum on read.

```python
from threading import Lock, Thread

class HitCounter:
    WINDOW = 300

    def __init__(self):
        self._times = [0] * self.WINDOW
        self._counts = [0] * self.WINDOW
        self._lock = Lock()

    def hit(self, timestamp: int) -> None:
        i = timestamp % self.WINDOW
        with self._lock:
            if self._times[i] != timestamp:
                self._times[i], self._counts[i] = timestamp, 0
            self._counts[i] += 1

    def get_hits(self, timestamp: int) -> int:
        with self._lock:
            return sum(c for t, c in zip(self._times, self._counts)
                       if timestamp - t < self.WINDOW)

hc = HitCounter()
ts = [Thread(target=lambda: [hc.hit(1) for _ in range(1000)]) for _ in range(8)]
for t in ts: t.start()
for t in ts: t.join(5)
assert hc.get_hits(1) == 8000
hc.hit(300)
assert hc.get_hits(300) == 8001
assert hc.get_hits(301) == 1          # hits at t=1 fell out of the window
```

**Follow-ups:** out-of-order timestamps (only reset if the new timestamp is newer) · per-second granularity too coarse → finer buckets · distributed → Redis `INCR` on `hits:{ts}` keys with TTL and sum, or a sliding-window counter.

### 4.11 Bank transfer without deadlock

**Problem:** `transfer(a, b, amt)` locks `a` then `b`. Concurrently `transfer(b, a, …)` locks `b` then `a` → **deadlock**.

**Fix:** always lock the two accounts in a **global order** (by account ID). Also validate inside the locks (balance can change between check and debit otherwise). Transfers to self → reject (or would double-lock a non-reentrant lock).

```python
import random
from threading import Lock, Thread

class InsufficientFunds(Exception): ...

class Account:
    def __init__(self, account_id: int, balance: int):
        self.id, self.balance = account_id, balance
        self.lock = Lock()

def transfer(src: Account, dst: Account, amount: int) -> None:
    if src is dst or amount <= 0:
        raise ValueError("invalid transfer")
    first, second = (src, dst) if src.id < dst.id else (dst, src)
    with first.lock, second.lock:
        if src.balance < amount:
            raise InsufficientFunds(src.id)
        src.balance -= amount
        dst.balance += amount

accounts = [Account(i, 1000) for i in range(4)]

def random_transfers():
    rng = random.Random()
    for _ in range(2000):
        a, b = rng.sample(accounts, 2)
        try:
            transfer(a, b, rng.randint(1, 50))
        except InsufficientFunds:
            pass

ts = [Thread(target=random_transfers) for _ in range(8)]
for t in ts: t.start()
for t in ts: t.join(10)
assert not any(t.is_alive() for t in ts)                 # no deadlock
assert sum(a.balance for a in accounts) == 4000          # money conserved
assert all(a.balance >= 0 for a in accounts)
```

**Follow-ups**
- *Locks across services/DB?* Same rule: `SELECT ... FROM accounts WHERE id IN (a, b) ORDER BY id FOR UPDATE`.
- *Alternative without ordering:* `acquire(timeout=…)` on the second lock; on failure release the first, back off randomly, retry.
- *Audit trail?* Ledger entries for each transfer (HLD guide 23.3).

### 4.12 Read-write lock from scratch

**Problem:** many readers can safely read together; writers need exclusive access. Python has no built-in RW lock.

**Design (writer-preferring, to avoid writer starvation)**
- State: `readers` (active count), `writer` (bool), `waiting_writers`.
- Reader enters when there's no active writer **and no waiting writer**.
- Writer enters when no active writer and `readers == 0`.
- One `Condition`; `notify_all` on release (readers and writers wait on different predicates).

```python
import time
from contextlib import contextmanager
from threading import Condition, Lock, Thread

class ReadWriteLock:
    def __init__(self):
        self._cv = Condition()
        self._readers = 0
        self._writer = False
        self._waiting_writers = 0

    def acquire_read(self):
        with self._cv:
            self._cv.wait_for(lambda: not self._writer and self._waiting_writers == 0)
            self._readers += 1

    def release_read(self):
        with self._cv:
            self._readers -= 1
            if self._readers == 0:
                self._cv.notify_all()

    def acquire_write(self):
        with self._cv:
            self._waiting_writers += 1
            try:
                self._cv.wait_for(lambda: not self._writer and self._readers == 0)
            finally:
                self._waiting_writers -= 1
            self._writer = True

    def release_write(self):
        with self._cv:
            self._writer = False
            self._cv.notify_all()

    @contextmanager
    def read(self):
        self.acquire_read()
        try:
            yield
        finally:
            self.release_read()

    @contextmanager
    def write(self):
        self.acquire_write()
        try:
            yield
        finally:
            self.release_write()

rw = ReadWriteLock()
data = {"x": 0, "y": 0}                 # invariant: x == y whenever nobody is writing
state, state_lock = {"readers": 0, "writers": 0, "max_readers": 0, "bad": 0}, Lock()

def reader():
    for _ in range(200):
        with rw.read():
            with state_lock:
                state["readers"] += 1
                state["max_readers"] = max(state["max_readers"], state["readers"])
                if state["writers"]:
                    state["bad"] += 1
            if data["x"] != data["y"]:
                state["bad"] += 1
            time.sleep(0.0001)
            with state_lock:
                state["readers"] -= 1

def writer():
    for _ in range(50):
        with rw.write():
            with state_lock:
                state["writers"] += 1
                if state["writers"] > 1 or state["readers"]:
                    state["bad"] += 1
            data["x"] += 1
            time.sleep(0.0001)
            data["y"] += 1
            with state_lock:
                state["writers"] -= 1

ts = [Thread(target=reader) for _ in range(6)] + [Thread(target=writer) for _ in range(2)]
for t in ts: t.start()
for t in ts: t.join(20)
assert state["bad"] == 0 and data == {"x": 100, "y": 100}
assert state["max_readers"] > 1          # readers really did overlap
```

**Follow-ups:** reader-preferring vs writer-preferring vs fair (FIFO ticket) · upgrading read → write deadlocks if two readers both try (disallow it, or release-then-acquire and re-validate) · when *not* to use one: short critical sections — a plain mutex is often faster.

---

## 5. Interview Q&A

**Q: Does the GIL make Python code thread-safe?**
No. It prevents simultaneous bytecode execution, but threads switch between bytecodes, so compound operations (`x += 1`, check-then-act) still race.

**Q: Threads vs processes vs asyncio for an API that calls 3 external services per request?**
asyncio (or threads): it's I/O-bound. Use `asyncio.gather` to call them concurrently, a semaphore to limit outbound concurrency, and timeouts on each call. Processes only for CPU-heavy work.

**Q: What's a race condition? Give a real-world example.**
Two Kafka consumers (or two retries) processing the same price update; or two requests populating the same cache key. Fix: idempotent writes keyed by ID + version, single-flight for cache fills.

**Q: How do you prevent deadlocks?**
Global lock ordering; hold locks briefly; no I/O or callbacks under a lock; try-lock with timeout + randomized backoff as a fallback.

**Q: Mutex vs semaphore?**
A mutex allows one holder and is meant to be released by the thread that acquired it. A semaphore allows N concurrent holders; used to limit concurrency (pools, outbound calls).

**Q: Why wait on a condition in a `while` loop?**
Spurious wakeups, and another thread can consume the state between the notify and your wakeup. Re-check the predicate.

**Q: `notify` vs `notify_all`?**
`notify` when any one waiter can make progress and all wait for the same predicate. `notify_all` when waiters wait for different predicates (FizzBuzz, RW lock), or when a state change may let several proceed (close/shutdown).

**Q: Optimistic or pessimistic locking for a booking system?**
High contention on the same rows (hot event) → pessimistic or atomic conditional updates. Normal low-contention updates (user profile) → optimistic version checks.

**Q: Your in-process lock works locally but double bookings happen in prod. Why?**
Prod runs several workers/containers; each has its own lock. Move the guarantee to shared state: DB conditional update / unique constraint, or a distributed lock with fencing tokens.

**Q: How do you test concurrent code?**
Start many threads behind a `Barrier` to maximize overlap; assert invariants (counts conserved, no double booking); join with timeouts to detect deadlocks; run many iterations; inject clocks to avoid sleeps; stress tests in CI.

**Q: What is thread starvation, and how did your RW lock avoid it?**
A thread never gets the resource. Writer-preferring: new readers wait while a writer is waiting, so a stream of readers can't block writers forever.

**Q: What's a thread-safe way to lazily initialize something expensive?**
Double-checked locking, or a per-key `Future`/`Event` (single-flight) so only one thread does the work and the others wait for its result.
