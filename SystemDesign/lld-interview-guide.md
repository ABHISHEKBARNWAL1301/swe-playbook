# LLD Interview Guide (SDE2)

Everything for the low-level design and machine-coding rounds in one book: how the round works → OOP, UML, SOLID, modeling techniques and design patterns → practice problems placed right after the concepts they use → concurrency (threads, locks, async, thread-safe designs).

Related: [HLD guide](hld-interview-guide.md) (system design, GenAI)

**Code conventions:** Python 3.11+, standard library only. Every code block runs as-is (copy into a `.py` file) and ends with assertions. Money is in integer minor units (paise/cents) — never floats. Clocks are injected so tests don't sleep (concurrency chapters use real threads, so a few tests use short sleeps).

## Contents

- [1. How the round is graded](#1-how-the-round-is-graded)
- [2. The 6-step approach](#2-the-6-step-approach)
- [3. OOP in depth](#3-oop-in-depth)
  - [3.1 Classes, objects, attributes, methods](#31-classes-objects-attributes-methods)
  - [3.2 Encapsulation](#32-encapsulation)
  - [3.3 Abstraction: abstract classes and protocols](#33-abstraction-abstract-classes-and-protocols)
  - [3.4 Inheritance](#34-inheritance)
  - [3.5 Polymorphism](#35-polymorphism)
  - [3.6 Composition, aggregation, delegation](#36-composition-aggregation-delegation)
  - [3.7 Dataclasses, value objects, entities, enums](#37-dataclasses-value-objects-entities-enums)
  - [3.8 Useful dunder methods](#38-useful-dunder-methods)
  - [3.9 Exceptions as part of the design](#39-exceptions-as-part-of-the-design)
  - [3.10 Overloading, overriding and binding](#310-overloading-overriding-and-binding)
  - [3.11 Shallow vs deep copy](#311-shallow-vs-deep-copy)
  - [3.12 OOP questions from Java and C++ interviews](#312-oop-questions-from-java-and-c-interviews)
- [4. Class relationships and UML](#4-class-relationships-and-uml)
  - [4.1 Relationships (strongest → weakest coupling)](#41-relationships-strongest--weakest-coupling)
  - [4.2 Class diagram example (parking lot)](#42-class-diagram-example-parking-lot)
  - [4.3 Sequence diagram example (booking)](#43-sequence-diagram-example-booking)
- [5. SOLID with code](#5-solid-with-code)
  - [5.1 S — Single Responsibility Principle](#51-s--single-responsibility-principle)
  - [5.2 O — Open/Closed Principle](#52-o--openclosed-principle)
  - [5.3 L — Liskov Substitution Principle](#53-l--liskov-substitution-principle)
  - [5.4 I — Interface Segregation Principle](#54-i--interface-segregation-principle)
  - [5.5 D — Dependency Inversion Principle](#55-d--dependency-inversion-principle)
- [6. Other design principles](#6-other-design-principles)
- [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)
  - [7.1 State machines with a transition table](#71-state-machines-with-a-transition-table)
  - [7.2 Interval overlap (bookings, meetings, rentals)](#72-interval-overlap-bookings-meetings-rentals)
  - [7.3 Allocation with fast lookup](#73-allocation-with-fast-lookup)
  - [7.4 Validate → compute → commit](#74-validate--compute--commit)
  - [7.5 IDs, time, and money](#75-ids-time-and-money)
  - [7.6 Thread-safety in LLD answers](#76-thread-safety-in-lld-answers)
- [8. Design patterns](#8-design-patterns)
  - [8.1 Creational](#81-creational)
  - [8.2 Structural](#82-structural)
  - [8.3 Behavioral](#83-behavioral)
  - [8.4 Other patterns that appear in LLD answers](#84-other-patterns-that-appear-in-lld-answers)
  - [8.5 Pattern quick-pick table](#85-pattern-quick-pick-table)
- [9. Machine-coding: layout, errors, tests](#9-machine-coding-layout-errors-tests)
  - [9.1 Project layout (90-minute round)](#91-project-layout-90-minute-round)
  - [9.2 Repository example](#92-repository-example)
  - [9.3 Testing in the round](#93-testing-in-the-round)
- [10. Problem: Tic-tac-toe and Snake & Ladder (game modeling)](#10-problem-tic-tac-toe-and-snake--ladder-game-modeling)
- [11. Problem: Chess](#11-problem-chess)
- [12. Problem: Snake game](#12-problem-snake-game)
- [13. Problem: Library management](#13-problem-library-management)
- [14. Problem: Meeting room scheduler / calendar](#14-problem-meeting-room-scheduler--calendar)
- [15. Problem: Hotel booking](#15-problem-hotel-booking)
- [16. Problem: Car / bike rental](#16-problem-car--bike-rental)
- [17. Problem: Parking lot](#17-problem-parking-lot)
- [18. Problem: Splitwise (expense sharing)](#18-problem-splitwise-expense-sharing)
- [19. Problem: Elevator system](#19-problem-elevator-system)
- [20. Problem: Rate limiter](#20-problem-rate-limiter)
- [21. Problem: Shopping cart + coupons](#21-problem-shopping-cart--coupons)
- [22. Problem: Vending machine](#22-problem-vending-machine)
- [23. Problem: Text editor with undo and redo](#23-problem-text-editor-with-undo-and-redo)
- [24. Problem: ATM](#24-problem-atm)
- [25. Problem: Food ordering (Zomato / Swiggy)](#25-problem-food-ordering-zomato--swiggy)
- [26. Problem: Ride sharing (Uber-style LLD)](#26-problem-ride-sharing-uber-style-lld)
- [27. Problem: Customer issue resolution system](#27-problem-customer-issue-resolution-system)
- [28. Problem: Task tracker (Jira-style)](#28-problem-task-tracker-jira-style)
- [29. Problem: Online auction](#29-problem-online-auction)
- [30. Problem: Cricket scoreboard](#30-problem-cricket-scoreboard)
- [31. Problem: Logging framework](#31-problem-logging-framework)
- [32. Problem: Notification / pub-sub system](#32-problem-notification--pub-sub-system)
- [33. Problem: In-memory file system](#33-problem-in-memory-file-system)
- [34. Problem: Stack Overflow / Q&A](#34-problem-stack-overflow--qa)
- [35. Problem: LRU cache and LFU cache (with TTL)](#35-problem-lru-cache-and-lfu-cache-with-ttl)
- [36. Problem: Key-value store with transactions](#36-problem-key-value-store-with-transactions)
- [37. Problem: Task scheduler / job scheduler](#37-problem-task-scheduler--job-scheduler)
- [38. Follow-up questions in every LLD round](#38-follow-up-questions-in-every-lld-round)
- [39. Operating systems: processes, threads and scheduling](#39-operating-systems-processes-threads-and-scheduling)
  - [39.1 Programs, processes and threads](#391-programs-processes-and-threads)
  - [39.2 CPU scheduling](#392-cpu-scheduling)
  - [39.3 Interrupts, system calls and priority inversion](#393-interrupts-system-calls-and-priority-inversion)
  - [39.4 Deadlock handling in operating systems](#394-deadlock-handling-in-operating-systems)
- [40. Operating systems: memory management](#40-operating-systems-memory-management)
  - [40.1 Virtual memory and paging](#401-virtual-memory-and-paging)
  - [40.2 Page replacement](#402-page-replacement)
  - [40.3 Stack, heap, allocation and garbage collection](#403-stack-heap-allocation-and-garbage-collection)
  - [40.4 Stories from production: memory](#404-stories-from-production-memory)
- [41. Operating systems: file systems, I/O and IPC](#41-operating-systems-file-systems-io-and-ipc)
  - [41.1 Files, inodes and links](#411-files-inodes-and-links)
  - [41.2 The page cache and durability](#412-the-page-cache-and-durability)
  - [41.3 I/O models: blocking, non-blocking, multiplexing, async](#413-io-models-blocking-non-blocking-multiplexing-async)
  - [41.4 Inter-process communication (IPC)](#414-inter-process-communication-ipc)
  - [41.5 Memory-mapped files (mmap)](#415-memory-mapped-files-mmap)
  - [41.6 How servers evolved: Apache, Nginx and the event loop](#416-how-servers-evolved-apache-nginx-and-the-event-loop)
- [42. Concurrency: what gets asked](#42-concurrency-what-gets-asked)
- [43. Concurrency concepts](#43-concurrency-concepts)
  - [43.1 Processes, threads, coroutines, and the GIL](#431-processes-threads-coroutines-and-the-gil)
  - [43.2 Kernel threads, green threads and virtual threads](#432-kernel-threads-green-threads-and-virtual-threads)
  - [43.3 Race conditions and critical sections](#433-race-conditions-and-critical-sections)
  - [43.4 Synchronization primitives](#434-synchronization-primitives)
  - [43.5 Deadlock, livelock, starvation](#435-deadlock-livelock-starvation)
  - [43.6 Pessimistic vs optimistic concurrency](#436-pessimistic-vs-optimistic-concurrency)
  - [43.7 Thread pools and futures](#437-thread-pools-and-futures)
  - [43.8 asyncio concurrency](#438-asyncio-concurrency)
  - [43.9 Python ↔ Java cheat sheet](#439-python--java-cheat-sheet)
  - [43.10 Java concurrency utilities interviewers ask about](#4310-java-concurrency-utilities-interviewers-ask-about)
  - [43.11 Thread lifecycle, daemon threads, thread-local state](#4311-thread-lifecycle-daemon-threads-thread-local-state)
  - [43.12 Memory model, atomic operations, CAS, lock-free](#4312-memory-model-atomic-operations-cas-lock-free)
  - [43.13 Multiprocessing in Python](#4313-multiprocessing-in-python)
  - [43.14 asyncio in depth](#4314-asyncio-in-depth)
  - [43.15 Common concurrency bugs (checklist)](#4315-common-concurrency-bugs-checklist)
  - [43.16 Testing and debugging concurrent code](#4316-testing-and-debugging-concurrent-code)
- [44. Problem: LC 1114 — Print in Order](#44-problem-lc-1114--print-in-order)
- [45. Problem: LC 1115 — Print FooBar Alternately](#45-problem-lc-1115--print-foobar-alternately)
- [46. Problem: LC 1116 — Print Zero Even Odd](#46-problem-lc-1116--print-zero-even-odd)
- [47. Problem: LC 1117 — Building H2O](#47-problem-lc-1117--building-h2o)
- [48. Problem: LC 1188 — Design Bounded Blocking Queue](#48-problem-lc-1188--design-bounded-blocking-queue)
- [49. Problem: LC 1195 — Fizz Buzz Multithreaded](#49-problem-lc-1195--fizz-buzz-multithreaded)
- [50. Problem: LC 1226 — The Dining Philosophers](#50-problem-lc-1226--the-dining-philosophers)
- [51. Problem: LC 1242 — Web Crawler Multithreaded](#51-problem-lc-1242--web-crawler-multithreaded)
- [52. Problem: Print 1 to N with K threads in turn](#52-problem-print-1-to-n-with-k-threads-in-turn)
- [53. Problem: Thread-safe singleton](#53-problem-thread-safe-singleton)
- [54. Problem: Bounded blocking queue / producer-consumer](#54-problem-bounded-blocking-queue--producer-consumer)
- [55. Problem: Thread-safe LRU cache](#55-problem-thread-safe-lru-cache)
- [56. Problem: Thread-safe rate limiter](#56-problem-thread-safe-rate-limiter)
- [57. Problem: Thread pool from scratch](#57-problem-thread-pool-from-scratch)
- [58. Problem: Delayed task scheduler](#58-problem-delayed-task-scheduler)
- [59. Problem: Connection pool](#59-problem-connection-pool)
- [60. Problem: In-memory pub-sub with concurrent subscribers](#60-problem-in-memory-pub-sub-with-concurrent-subscribers)
- [61. Problem: Hit counter (concurrent)](#61-problem-hit-counter-concurrent)
- [62. Problem: Bank transfer without deadlock](#62-problem-bank-transfer-without-deadlock)
- [63. Problem: Read-write lock from scratch](#63-problem-read-write-lock-from-scratch)
- [64. Problem: Movie ticket booking (BookMyShow)](#64-problem-movie-ticket-booking-bookmyshow)
- [65. Problem: Wallet / payment service](#65-problem-wallet--payment-service)
- [66. Concurrency interview Q&A](#66-concurrency-interview-qa)

---

## 1. How the round is graded

Two formats:
- **LLD discussion (45–60 min):** requirements, class diagram, key methods, some code, extensibility questions. Commonly reported at large product companies (Amazon, Microsoft, Uber, and sometimes Google at senior levels).
- **Machine coding (90–120 min):** working, runnable code; then a review where they add a feature or ask "what if". Commonly reported at Indian product companies and fintechs (Flipkart, Swiggy, PhonePe, Razorpay) and at Atlassian and Uber India.

Interview formats change and differ by team, so treat these as tendencies, not rules — ask your recruiter which format to expect.

| Area | What good looks like |
|---|---|
| Requirements | You ask questions and write down what's in and out of scope |
| Modeling | Right entities; each class has one clear job; no god class |
| Correctness | Invariants hold; invalid input and invalid state transitions are rejected; failed operations change nothing |
| Extensibility | A new type (vehicle, payment, discount) = a new class, not edits to `if/elif` chains |
| Patterns | Used where they solve a real problem; you can name the problem |
| Code quality | Clear names, small methods, custom exceptions, type hints, no global mutable state |
| Concurrency awareness | You can say what breaks with two concurrent users and how to fix it |
| Working code | It runs; you demo it |

**Biggest SDE2 mistakes:** coding before clarifying; one giant `Manager` class; patterns forced where they don't help; inheritance for everything; code that doesn't run at the end; ignoring edge cases (full lot, duplicate request, invalid state).

---

## 2. The 6-step approach

1. **Clarify (5 min):** actors, core use cases, rules, limits. Write "Out of scope: payments, auth, persistence, UI."
2. **Entities and ownership (5 min):** nouns → classes. Decide **who owns each piece of mutable state** (one owner per invariant).
3. **Public API (5 min):** methods on the main service — your contract. Inputs, outputs, errors.
4. **Variation points → patterns:** what changes independently? (pricing, assignment, discount, notification channel, state) → Strategy / State / Observer / Factory.
5. **Code:** models → service happy path → validation → edge cases. Keep it runnable at every step.
6. **Demo + discuss:** one success, one rejection, one edge case. Then: concurrency, persistence, scale, tests.

**Invariant-first habit:** before coding, write 1–3 rules that must always hold. Example (parking): "a spot holds at most one vehicle; a vehicle has at most one active ticket." Then every method:
- **validates everything first**, and only then mutates (so a failure leaves state unchanged);
- updates all related state **together** in the one owning class.

---

## 3. OOP in depth

### 3.1 Classes, objects, attributes, methods

- **Class:** a blueprint (data + behavior). **Object/instance:** one concrete thing made from it.
- **Instance attributes** (`self.x`) are per object. **Class attributes** are shared by all instances — never use a mutable class attribute as per-object state.
- **Instance methods** get `self`; **class methods** get `cls` (alternative constructors); **static methods** get neither (helpers that belong to the class's namespace).
- **Identity vs equality:** `a is b` (same object) vs `a == b` (equal values, `__eq__`).

```python
class Account:
    bank_name = "DemoBank"                 # class attribute: shared
    _next_id = 1

    def __init__(self, owner: str, balance: int = 0):
        self.id = Account._next_id         # instance attributes
        Account._next_id += 1
        self.owner = owner
        self._balance = balance

    @classmethod
    def with_bonus(cls, owner: str) -> "Account":   # alternative constructor
        return cls(owner, balance=500)

    @staticmethod
    def is_valid_amount(amount: int) -> bool:        # helper, no self/cls
        return isinstance(amount, int) and amount > 0

a, b = Account("asha"), Account.with_bonus("ravi")
assert (a.id, b.id) == (1, 2) and b._balance == 500
assert Account.is_valid_amount(10) and not Account.is_valid_amount(-1)
assert a.bank_name == b.bank_name == "DemoBank"
```

**Mutable class attribute bug (classic interview question):**
```python
class Bad:
    items = []                 # shared by ALL instances
    def add(self, x): self.items.append(x)

class Good:
    def __init__(self): self.items = []   # one list per instance
    def add(self, x): self.items.append(x)

b1, b2 = Bad(), Bad(); b1.add(1)
assert b2.items == [1]                   # surprise: leaked across objects
g1, g2 = Good(), Good(); g1.add(1)
assert g2.items == []
```
Same family: `def f(x, acc=[])` — default arguments are evaluated once, so the list is shared across calls. Use `acc=None` and inside: `if acc is None: acc = []`.

### 3.2 Encapsulation

- The object **owns its state** and exposes operations that keep it valid. Outsiders can't put it into an invalid state.
- Python has no `private`; conventions: `_name` (internal), `__name` (name-mangled, rarely needed).
- **Properties** expose read access (or validated writes) without exposing the field.
- Don't return internal mutable collections — return copies or read-only views.

```python
class BankAccount:
    def __init__(self, balance: int = 0):
        if balance < 0:
            raise ValueError("negative opening balance")
        self._balance = balance
        self._history: list[tuple[str, int]] = []

    @property
    def balance(self) -> int:              # read-only from outside
        return self._balance

    @property
    def history(self) -> tuple:            # a copy, not the internal list
        return tuple(self._history)

    def withdraw(self, amount: int) -> None:
        if amount <= 0:
            raise ValueError("amount must be positive")
        if amount > self._balance:
            raise ValueError("insufficient funds")   # validate before mutating
        self._balance -= amount
        self._history.append(("withdraw", amount))

acct = BankAccount(100)
acct.withdraw(30)
assert acct.balance == 70 and acct.history == (("withdraw", 30),)
try:
    acct.balance = 10**9
    assert False
except AttributeError:
    pass                                    # no setter → can't bypass rules
```

### 3.3 Abstraction: abstract classes and protocols

- Callers depend on **what** something does, not **how**.
- `abc.ABC` + `@abstractmethod`: a base class that can't be instantiated until subclasses implement the methods. Good when you also share code.
- `typing.Protocol`: structural typing ("anything with a `pay(amount)` method") — no inheritance needed. Good for loose coupling.
- In Python, a plain **callable** is often the simplest abstraction for a one-method strategy.

```python
from abc import ABC, abstractmethod
from typing import Protocol

class PaymentMethod(ABC):
    @abstractmethod
    def pay(self, amount: int) -> str: ...

    def pay_with_receipt(self, amount: int) -> str:     # shared behavior in the base
        return f"receipt:{self.pay(amount)}"

class Upi(PaymentMethod):
    def pay(self, amount): return f"upi:{amount}"

try:
    PaymentMethod()
    assert False
except TypeError:
    pass                                            # abstract → can't instantiate

class Notifier(Protocol):
    def send(self, user: str, msg: str) -> None: ...

class SmsNotifier:                                   # no inheritance from Notifier
    def __init__(self): self.sent = []
    def send(self, user, msg): self.sent.append((user, msg))

def notify_all(n: Notifier, users):                 # depends on the shape only
    for u in users:
        n.send(u, "hi")

sms = SmsNotifier()
notify_all(sms, ["a", "b"])
assert Upi().pay_with_receipt(5) == "receipt:upi:5" and len(sms.sent) == 2
```

**Abstract class vs interface (Java/C++ angle):** an abstract class can hold state and shared code; a class can extend only one (Java). An interface defines a contract; a class can implement many. Python's ABC plays both roles; Protocol is the closest to a pure interface.

### 3.4 Inheritance

- "**is-a**" with **substitutability**: a subclass must work anywhere the parent works (Liskov).
- `super()` calls the parent's implementation (follows the **MRO** — method resolution order).
- Multiple inheritance exists in Python (mixins), but keep it to small, independent mixins.
- **When not to inherit:** to reuse a few fields or helpers, to model roles that change at runtime (a user who becomes an admin), or variants that combine (weekend + truck pricing → subclass explosion). Use composition instead.

```python
class Vehicle:
    wheels = 4
    def __init__(self, plate: str):
        self.plate = plate
    def describe(self) -> str:
        return f"{type(self).__name__}({self.plate}, {self.wheels} wheels)"

class Bike(Vehicle):
    wheels = 2

class Truck(Vehicle):
    wheels = 6
    def __init__(self, plate: str, load_tons: int):
        super().__init__(plate)            # reuse parent initialization
        self.load_tons = load_tons
    def describe(self):
        return super().describe() + f" load={self.load_tons}t"

assert Bike("B1").describe() == "Bike(B1, 2 wheels)"
assert Truck("T1", 10).describe() == "Truck(T1, 6 wheels) load=10t"
assert [c.__name__ for c in Truck.__mro__] == ["Truck", "Vehicle", "object"]
```

### 3.5 Polymorphism

- **Subtype polymorphism:** one call site, many implementations (`shape.area()`).
- **Duck typing:** Python cares that the method exists, not the type.
- **Operator overloading** via dunder methods (`__add__`, `__lt__`, `__eq__`).
- Replaces `if type == ...` chains → new behavior = new class.

```python
import math

class Shape:
    def area(self) -> float:
        raise NotImplementedError

class Circle(Shape):
    def __init__(self, r): self.r = r
    def area(self): return math.pi * self.r ** 2

class Rect(Shape):
    def __init__(self, w, h): self.w, self.h = w, h
    def area(self): return self.w * self.h

shapes = [Circle(1), Rect(2, 3)]
assert round(sum(s.area() for s in shapes), 2) == 9.14      # no if/else on type
```

### 3.6 Composition, aggregation, delegation

- **Composition (has-a, owns):** the part lives and dies with the whole (an `Order` owns its `OrderLine`s).
- **Aggregation (has-a, shares):** the part exists independently (a `Team` has `Player`s who exist without it).
- **Delegation:** an object forwards work to a component (`Car.start()` → `self.engine.start()`).
- **Composition over inheritance:** assemble behavior from small parts you can swap at runtime.

```python
class PetrolEngine:
    def start(self): return "vroom"

class ElectricEngine:
    def start(self): return "hum"

class Car:
    def __init__(self, engine):            # composed, swappable
        self.engine = engine
    def start(self):
        return self.engine.start()         # delegation

car = Car(PetrolEngine())
assert car.start() == "vroom"
car.engine = ElectricEngine()              # change behavior without a subclass
assert car.start() == "hum"
```

### 3.7 Dataclasses, value objects, entities, enums

- **Entity:** has an identity that persists while attributes change (User #42 changes email, still the same user). Equality by ID.
- **Value object:** defined only by its values, immutable (Money(500, "INR"), Location(lat, lng), DateRange). Equality by value. Use `@dataclass(frozen=True)`.
- **Enums** for fixed sets (statuses, types) — no magic strings.
- `field(default_factory=list)` for mutable defaults. `order=True` gives comparisons. `slots=True` saves memory.

```python
from dataclasses import dataclass, field
from enum import Enum

@dataclass(frozen=True)
class Money:
    amount: int                  # minor units
    currency: str = "INR"

    def __add__(self, other: "Money") -> "Money":
        if self.currency != other.currency:
            raise ValueError("currency mismatch")
        return Money(self.amount + other.amount, self.currency)

class OrderStatus(Enum):
    PLACED = "placed"
    SHIPPED = "shipped"

@dataclass
class Order:                     # entity: mutable, identified by id
    id: int
    status: OrderStatus = OrderStatus.PLACED
    lines: list[Money] = field(default_factory=list)

    def __eq__(self, other):
        return isinstance(other, Order) and other.id == self.id
    __hash__ = object.__hash__

assert Money(100) + Money(50) == Money(150)          # value equality
assert len({Money(1), Money(1)}) == 1                # frozen → hashable
o1, o2 = Order(1), Order(1, OrderStatus.SHIPPED)
assert o1 == o2                                      # same identity
try:
    Money(1).amount = 5
    assert False
except Exception:
    pass                                             # immutable
```

### 3.8 Useful dunder methods

| Method | Purpose |
|---|---|
| `__repr__` / `__str__` | Debug / user-facing text |
| `__eq__` + `__hash__` | Equality and use as dict keys / in sets (must agree: equal objects → equal hashes) |
| `__lt__` (+ `functools.total_ordering`) | Sorting, heaps |
| `__len__`, `__iter__`, `__contains__`, `__getitem__` | Make a collection behave like one |
| `__enter__` / `__exit__` | Context managers (`with`) for resources/locks |
| `__call__` | Object usable as a function (strategy objects) |

```python
import heapq
from functools import total_ordering

@total_ordering
class Task:
    def __init__(self, priority: int, name: str):
        self.priority, self.name = priority, name
    def __eq__(self, other): return self.priority == other.priority
    def __lt__(self, other): return self.priority < other.priority
    def __repr__(self): return f"Task({self.priority}, {self.name!r})"

class Playlist:
    def __init__(self, songs): self._songs = list(songs)
    def __len__(self): return len(self._songs)
    def __iter__(self): return iter(self._songs)
    def __contains__(self, s): return s in self._songs

heap = [Task(3, "c"), Task(1, "a"), Task(2, "b")]
heapq.heapify(heap)
assert heapq.heappop(heap).name == "a" and Task(1, "x") <= Task(2, "y")
p = Playlist(["x", "y"])
assert len(p) == 2 and "y" in p and list(p) == ["x", "y"]
```

### 3.9 Exceptions as part of the design

- Define a small hierarchy: `class DomainError(Exception)`, then `NotFound`, `InvalidState`, `InsufficientFunds`…
- Raise specific errors; don't return `None`/`False` for failures (callers forget to check).
- Validate at the service boundary; inside, trust your invariants.
- Never swallow exceptions silently; convert to domain errors at boundaries (e.g., a provider's HTTP error → `PaymentFailed`).

```python
class DomainError(Exception): ...
class NotFound(DomainError): ...
class InvalidState(DomainError): ...

def find(store: dict, key):
    try:
        return store[key]
    except KeyError:
        raise NotFound(f"{key} not found") from None

try:
    find({}, "x")
except DomainError as e:            # callers can catch the whole family
    assert isinstance(e, NotFound)
```

### 3.10 Overloading, overriding and binding

- **Overriding:** a subclass provides its own version of a method the parent defines (same name, same signature). Which version runs is decided **at runtime** from the object's actual type — *dynamic binding* (runtime polymorphism).
- **Overloading:** several methods with the same name but **different parameter lists** in one class (`add(int, int)`, `add(double, double)`). Java/C++ pick one **at compile time** from the argument types — *static binding*.
- Python has no overloading by signature: a second `def add` simply replaces the first. Use default arguments, `*args`, or `functools.singledispatch` (dispatch on the first argument's type).

```python
from functools import singledispatch

class Animal:
    def speak(self): return "..."

class Dog(Animal):
    def speak(self): return "woof"          # override → chosen at runtime

# static type says Animal; dynamic type decides
def talk(a: Animal): return a.speak()
assert talk(Dog()) == "woof"

@singledispatch
def area(shape): raise TypeError("unknown shape")

@area.register
def _(r: tuple): return r[0] * r[1]           # rectangle as (w, h)

@area.register
def _(radius: float): return round(3.14159 * radius * radius, 2)

assert area((2, 3)) == 6 and area(1.0) == 3.14
```

### 3.11 Shallow vs deep copy

A classic bug source: copying an object that holds other mutable objects.

```python
import copy

order = {"id": 1, "items": [{"sku": "tee", "qty": 1}]}

shallow = copy.copy(order)           # new outer dict, SAME inner list
shallow["items"][0]["qty"] = 5
assert order["items"][0]["qty"] == 5  # original changed too!

order = {"id": 1, "items": [{"sku": "tee", "qty": 1}]}
deep = copy.deepcopy(order)          # copies everything recursively
deep["items"][0]["qty"] = 5
assert order["items"][0]["qty"] == 1
```
In Java the same question appears as "how does `clone()` behave?"; in C++ as "what does the default copy constructor do with a pointer member?" — the answer is the same idea: it copies the reference/pointer, not what it points to.

### 3.12 OOP questions from Java and C++ interviews

Even if you code in Python, interviewers often ask these. Short, correct answers are enough.

**Abstract class vs interface (Java)**
- Abstract class: can have state (fields), constructors, implemented and abstract methods; a class extends only **one**.
- Interface: a contract; a class implements **many**. Since Java 8 interfaces can have `default` and `static` methods, so the difference is mostly "state + single inheritance" vs "contract + multiple".
- Rule of thumb: interface for a capability (`Comparable`, `Runnable`), abstract class for a shared base with real code.

**Access modifiers**

| | Java | C++ | Python |
|---|---|---|---|
| Everyone | `public` | `public` | default |
| Subclasses (+ same package in Java) | `protected` | `protected` | `_name` convention |
| Only this class | `private` | `private` | `__name` (name mangling) |
| Same package only | (no modifier) | — | — |

**`final` / `sealed`:** `final` class can't be subclassed, `final` method can't be overridden, `final` variable can't be reassigned. Java 17 `sealed` classes allow only listed subclasses (useful for closed hierarchies like `Shape permits Circle, Square`).

**C++ virtual functions and the vtable**
- A method is overridable at runtime only if declared `virtual`. Each class with virtual methods has a **vtable** (table of function pointers); each object carries a hidden pointer to its class's vtable. A virtual call = follow the pointer, look up the slot, jump. That's the mechanism behind runtime polymorphism.
- **Virtual destructor:** if you delete a derived object through a base pointer and the base destructor isn't `virtual`, the derived destructor never runs → leak. Rule: any class meant for inheritance has a virtual destructor.

**The diamond problem (multiple inheritance)**

```text
        Device
       /      \
   Printer   Scanner
       \      /
     MultiFunction      → which Device.power_on() does MultiFunction inherit? Is Device's state stored twice?
```
- C++: without `virtual` inheritance, `Device` is stored twice; `virtual` inheritance shares one copy.
- Java: avoids it by allowing multiple inheritance of interfaces only (and forces you to override clashing default methods).
- Python: uses the **MRO** (C3 linearization) — a single, predictable order: `MultiFunction → Printer → Scanner → Device`.

```python
class Device:
    def power_on(self): return "device"
class Printer(Device):
    def power_on(self): return "printer>" + super().power_on()
class Scanner(Device):
    def power_on(self): return "scanner>" + super().power_on()
class MultiFunction(Printer, Scanner):
    pass

assert [c.__name__ for c in MultiFunction.__mro__] == ["MultiFunction", "Printer", "Scanner", "Device", "object"]
# super() follows the MRO, Device runs once
assert MultiFunction().power_on() == "printer>scanner>device"
```

**Constructors, destructors and RAII**
- C++ **RAII** (resource acquisition is initialization): acquire a resource in the constructor, release it in the destructor; when the object goes out of scope, cleanup is guaranteed. `std::lock_guard`, `std::unique_ptr` work this way.
- Python's equivalent is the **context manager** (`with open(...)`, `with lock:`) — don't rely on `__del__`, its timing isn't guaranteed.
- Java uses try-with-resources (`AutoCloseable`).

**Composition vs inheritance, once more:** if an interviewer asks "why not just subclass?", the strongest answers are: subclass explosion when variations combine, fragile base classes (a parent change breaks children), and LSP violations. See [4. Class relationships and UML](#4-class-relationships-and-uml) and [5. SOLID with code](#5-solid-with-code).

---

## 4. Class relationships and UML

### 4.1 Relationships (strongest → weakest coupling)

| Relationship | Meaning | Example | UML arrow |
|---|---|---|---|
| **Inheritance (generalization)** | is-a | `Car` is a `Vehicle` | solid line, hollow triangle `<\|--` |
| **Realization** | implements an interface | `Upi` implements `PaymentMethod` | dashed line, hollow triangle `<\|..` |
| **Composition** | owns; part dies with the whole | `Order` ◆— `OrderLine` | filled diamond `*--` |
| **Aggregation** | has; part lives independently | `Team` ◇— `Player` | hollow diamond `o--` |
| **Association** | knows about / uses long-term | `Driver` — `Car` | plain line `--` |
| **Dependency** | uses temporarily (parameter, local) | `ReportService` uses `Formatter` | dashed arrow `..>` |

**Multiplicity:** `1`, `0..1`, `*`, `1..*` on each end (one `Order` has `1..*` lines).

### 4.2 Class diagram example (parking lot)

On a whiteboard, boxes with the important fields and methods, and labelled lines between them, are all anyone expects:

```text
┌──────────────────────────┐ 1      * ┌──────────────────┐
│ ParkingLot               │◆─────────│ Spot             │
│ - spots: list[Spot]      │  owns    │ + id: str        │
│ - tickets: dict          │          │ + size: SpotSize │
│ + park(vehicle) → Ticket │          │ + floor: int     │
│ + exit(ticket_id) → int  │          └──────────────────┘
└──────────────────────────┘
      │ 1                │ uses
      │ ◇ active         ▼
      │ *       ┌─────────────────────────┐
┌─────▼──────┐  │ «interface»             │
│ Ticket     │  │ PricingStrategy         │
│ + id: int  │  │ + price(vehicle, secs)  │
│ + plate    │  └─────────────────────────┘
│ + spot_id  │              △ implements
│ + entered  │              ┊
└────────────┘     ┌────────┴───────┐
                   │ HourlyPricing  │
                   └────────────────┘
```

- `◆──` composition: spots don't exist without the lot.
- `◇──` aggregation: active tickets are tracked by the lot but are their own objects.
- `──▶ uses`: dependency on an interface, so pricing can be swapped.
- `△ ┊` realization: `HourlyPricing` implements `PricingStrategy`.

### 4.3 Sequence diagram example (booking)

Time runs downwards; each arrow is a call (solid) or a return (dashed):

```text
User        BookingService      SeatRepository     PaymentGateway
 │ hold(show, seats) │                  │                  │
 │──────────────────►│ hold_all_or_none(seats)             │
 │                   │─────────────────►│                  │
 │                   │◄ ─ ─ hold_id ─ ─ │                  │
 │◄ ─ ─ hold_id ─ ─ ─│                  │                  │
 │ confirm(hold_id)  │                  │                  │
 │──────────────────►│ charge(amount, key=hold_id)         │
 │                   │────────────────────────────────────►│
 │                   │◄ ─ ─ ─ ─ ─ ─ ─ ─ success ─ ─ ─ ─ ─ ─│
 │                   │ mark_booked(hold_id)                │
 │                   │─────────────────►│                  │
 │◄ ─ ─ booking ─ ─ ─│                  │                  │
```

In interviews, a quick text version is fine: `ParkingLot 1──* Spot`, `ParkingLot ──> PricingStrategy`.

---

## 5. SOLID with code

### 5.1 S — Single Responsibility Principle

**A class should have one reason to change.** "Reason to change" = one stakeholder/concern (business rules, formatting, persistence, notification).

```python
# BAD: business rules, formatting and persistence in one class.
class InvoiceBad:
    def __init__(self, items): self.items = items
    def total(self): return sum(p * q for p, q in self.items)
    def to_html(self): return f"<b>{self.total()}</b>"        # presentation
    def save(self, db): db.append(self.total())                 # persistence

# GOOD: each class changes for one reason.
class Invoice:
    def __init__(self, items): self.items = items
    def total(self) -> int: return sum(p * q for p, q in self.items)

class InvoiceHtmlRenderer:
    def render(self, invoice: Invoice) -> str: return f"<b>{invoice.total()}</b>"

class InvoiceRepository:
    def __init__(self): self.rows = []
    def save(self, invoice: Invoice) -> None: self.rows.append(invoice.total())

inv = Invoice([(100, 2), (50, 1)])
repo = InvoiceRepository(); repo.save(inv)
assert InvoiceHtmlRenderer().render(inv) == "<b>250</b>" and repo.rows == [250]
```

### 5.2 O — Open/Closed Principle

**Open for extension, closed for modification:** add behavior by adding code, not by editing tested code.

```python
from abc import ABC, abstractmethod

# BAD: every new customer type edits this function.
def discount_bad(kind: str, amount: int) -> int:
    if kind == "regular": return 0
    elif kind == "premium": return amount // 10
    elif kind == "vip": return amount // 5
    raise ValueError(kind)

# GOOD: new types are new classes; the checkout code never changes.
class DiscountPolicy(ABC):
    @abstractmethod
    def discount(self, amount: int) -> int: ...

class Regular(DiscountPolicy):
    def discount(self, amount): return 0

class Premium(DiscountPolicy):
    def discount(self, amount): return amount // 10

class Vip(DiscountPolicy):              # added later without touching checkout()
    def discount(self, amount): return amount // 5

def checkout(amount: int, policy: DiscountPolicy) -> int:
    return amount - policy.discount(amount)

assert checkout(1000, Premium()) == 900 and checkout(1000, Vip()) == 800
```

### 5.3 L — Liskov Substitution Principle

**Subtypes must be usable wherever the base type is expected without breaking correctness.** Subclasses must not strengthen preconditions, weaken postconditions, or throw unexpected errors.

```python
# BAD: Square breaks Rectangle's contract (setting width changes height).
class Rectangle:
    def __init__(self, w, h): self.w, self.h = w, h
    def set_width(self, w): self.w = w
    def area(self): return self.w * self.h

class SquareBad(Rectangle):
    def __init__(self, s): super().__init__(s, s)
    def set_width(self, w): self.w = self.h = w

def stretch(r: Rectangle):
    r.set_width(10)
    return r.area()            # caller expects 10 * original height

assert stretch(Rectangle(2, 3)) == 30
assert stretch(SquareBad(3)) == 100          # violates the expectation (30)

# GOOD: don't model Square as a mutable Rectangle; share a smaller abstraction.
class Shape:
    def area(self) -> int: raise NotImplementedError

class Rect(Shape):
    def __init__(self, w, h): self.w, self.h = w, h
    def area(self): return self.w * self.h

class Square(Shape):
    def __init__(self, s): self.s = s
    def area(self): return self.s * self.s

assert [s.area() for s in (Rect(2, 3), Square(3))] == [6, 9]
```

Other classic LSP smells: `Penguin(Bird)` with `fly()` raising; a `ReadOnlyList(list)` whose `append` raises. Fix by splitting the abstraction (`Bird` vs `FlyingBird`).

### 5.4 I — Interface Segregation Principle

**Clients shouldn't depend on methods they don't use.** Prefer several small interfaces over one fat one.

```python
from abc import ABC, abstractmethod

# BAD: a basic printer is forced to implement scan/fax.
class MachineBad(ABC):
    @abstractmethod
    def print(self, doc): ...
    @abstractmethod
    def scan(self, doc): ...
    @abstractmethod
    def fax(self, doc): ...

# GOOD: small role interfaces, combine as needed.
class Printer(ABC):
    @abstractmethod
    def print(self, doc): ...

class Scanner(ABC):
    @abstractmethod
    def scan(self, doc): ...

class BasicPrinter(Printer):
    def print(self, doc): return f"printed {doc}"

class OfficeMachine(Printer, Scanner):
    def print(self, doc): return f"printed {doc}"
    def scan(self, doc): return f"scanned {doc}"

assert BasicPrinter().print("a") == "printed a"
assert OfficeMachine().scan("b") == "scanned b"
```

### 5.5 D — Dependency Inversion Principle

**High-level modules depend on abstractions, not concrete low-level modules.** Inject dependencies (constructor injection) → swappable and testable.

```python
from typing import Protocol

class EmailSender(Protocol):
    def send(self, to: str, body: str) -> None: ...

# BAD: OrderService creates a concrete SMTP client inside → can't test without SMTP.
# class OrderService:
#     def __init__(self): self.mailer = SmtpMailer("smtp.prod")

# GOOD: depend on the abstraction, receive it from outside.
class OrderService:
    def __init__(self, mailer: EmailSender):
        self._mailer = mailer
    def place(self, user_email: str, order_id: int) -> None:
        self._mailer.send(user_email, f"order {order_id} placed")

class FakeMailer:
    def __init__(self): self.outbox = []
    def send(self, to, body): self.outbox.append((to, body))

fake = FakeMailer()
OrderService(fake).place("a@x.com", 7)
assert fake.outbox == [("a@x.com", "order 7 placed")]
```

**DIP vs DI vs IoC:** DIP is the principle (depend on abstractions). Dependency injection is a technique (pass dependencies in). Inversion of control is the broader idea (a framework/container wires things and calls your code).

---

## 6. Other design principles

| Principle | Meaning | In practice |
|---|---|---|
| **DRY** | Each piece of knowledge has one source | One validation function, not five copies. But don't merge code that only *looks* similar (it may change for different reasons). |
| **KISS** | Simplest design that works | A dict + lock beats a custom concurrent structure in a 90-min round. |
| **YAGNI** | Don't build what isn't required yet | No plugin system "for later". Leave a seam (interface) instead. |
| **Composition over inheritance** | Combine small parts | Strategy objects instead of subclass trees. |
| **High cohesion, low coupling** | Related things together; few dependencies between modules | A `Cart` owns cart logic; it doesn't reach into payment internals. |
| **Law of Demeter** | Talk only to immediate friends | `order.total()`, not `order.cart.items[0].product.price.amount`. |
| **Tell, don't ask** | Tell an object to do something instead of pulling its data and deciding outside | `account.withdraw(50)` rather than `if account.balance >= 50: account.balance -= 50`. |
| **Fail fast** | Detect invalid input/state early with clear errors | Validate in constructors and at service entry. |
| **Separation of concerns** | Different concerns in different layers | Parsing input ≠ business logic ≠ storage. |
| **Program to an interface** | Depend on contracts | Accept `PaymentMethod`, not `RazorpayClient`. |

---

## 7. Modeling techniques that come up in every problem

### 7.1 State machines with a transition table

```python
from enum import Enum, auto

class Status(Enum):
    CREATED = auto(); PAID = auto(); SHIPPED = auto(); DELIVERED = auto(); CANCELLED = auto()

ALLOWED = {
    Status.CREATED: {Status.PAID, Status.CANCELLED},
    Status.PAID: {Status.SHIPPED, Status.CANCELLED},
    Status.SHIPPED: {Status.DELIVERED},
    Status.DELIVERED: set(),
    Status.CANCELLED: set(),
}

def transition(current: Status, new: Status) -> Status:
    if new not in ALLOWED[current]:
        raise ValueError(f"{current.name} -> {new.name} not allowed")
    return new

s = transition(Status.CREATED, Status.PAID)
try:
    transition(s, Status.DELIVERED); assert False
except ValueError:
    pass
```

### 7.2 Interval overlap (bookings, meetings, rentals)

Two half-open intervals `[a1, a2)` and `[b1, b2)` overlap **iff `a1 < b2 and b1 < a2`**. Back-to-back (`a2 == b1`) doesn't overlap. Use half-open intervals everywhere to avoid off-by-one bugs.

### 7.3 Allocation with fast lookup

- Smallest free spot of size ≥ k → a min-heap (or sorted list) per size.
- Nearest available driver → geo cell index; for interviews, a scan with `min(..., key=distance)` is fine; mention the index.
- Free items per type → `dict[type, set[id]]` for O(1) pick/release.

### 7.4 Validate → compute → commit

```
def operation(...):
    1. validate all inputs and preconditions (raise on failure)
    2. compute the result (pure, may raise)
    3. commit all state changes together (no raising after this point)
```
This makes every failure leave state unchanged — the most common correctness bug in machine-coding rounds.

### 7.5 IDs, time, and money

- IDs: `itertools.count(1)` per service, or `uuid4()`.
- Time: inject `clock=time.monotonic` (durations) or `datetime` provider (calendar dates); tests pass a fake.
- Money: integers in minor units; when splitting, distribute the remainder explicitly so parts sum to the total.

```python
def split_evenly(total: int, n: int) -> list[int]:
    base, extra = divmod(total, n)
    return [base + 1 if i < extra else base for i in range(n)]

assert split_evenly(1000, 3) == [334, 333, 333] and sum(split_evenly(1000, 3)) == 1000
```

### 7.6 Thread-safety in LLD answers

- Name the race ("two gates pick the same free spot").
- Simplest fix: one lock in the owning service around check + act.
- Better throughput: per-entity locks (per show, per account) — acquire multiple in a fixed order.
- Multi-server: in-process locks don't work → DB conditional updates / unique constraints.
- Details and patterns: [concurrency part](#42-concurrency-what-gets-asked).

---

## 8. Design patterns

For each: **intent → when it shows up in LLD → code**. All blocks run.

### 8.1 Creational

**Singleton** — exactly one instance (logger registry, config). In most designs the better answer is to create one object at startup and pass it to whoever needs it (dependency injection): it's just as "single", and tests can pass a fake. In Python, a module-level instance (`config = Config()` in `config.py`) is the idiomatic singleton, because a module is imported once. Interviewers still ask for the thread-safe class version; it's built and tested in [53. Problem: Thread-safe singleton](#53-problem-thread-safe-singleton).

**Factory method / simple factory** — create the right subtype from input; callers don't know concrete classes.

```python
from abc import ABC, abstractmethod

class Notification(ABC):
    @abstractmethod
    def send(self, msg: str) -> str: ...

class Email(Notification):
    def send(self, msg): return f"email:{msg}"

class Sms(Notification):
    def send(self, msg): return f"sms:{msg}"

class NotificationFactory:
    _registry: dict[str, type[Notification]] = {"email": Email, "sms": Sms}

    @classmethod
    def register(cls, kind: str, klass: type[Notification]) -> None:
        cls._registry[kind] = klass                 # extend without editing create()

    @classmethod
    def create(cls, kind: str) -> Notification:
        try:
            return cls._registry[kind]()
        except KeyError:
            raise ValueError(f"unknown channel {kind}") from None

class Push(Notification):
    def send(self, msg): return f"push:{msg}"

NotificationFactory.register("push", Push)
assert NotificationFactory.create("push").send("hi") == "push:hi"
```

**Abstract factory** — create *families* of related objects that must match (UI theme: dark button + dark checkbox; cloud provider: AWS storage + AWS queue).

```python
class DarkButton:
    def render(self): return "dark-button"
class DarkCheckbox:
    def render(self): return "dark-checkbox"
class LightButton:
    def render(self): return "light-button"
class LightCheckbox:
    def render(self): return "light-checkbox"

class DarkTheme:
    def button(self): return DarkButton()
    def checkbox(self): return DarkCheckbox()

class LightTheme:
    def button(self): return LightButton()
    def checkbox(self): return LightCheckbox()

def build_form(theme):                    # never mixes families
    return [theme.button().render(), theme.checkbox().render()]

assert build_form(DarkTheme()) == ["dark-button", "dark-checkbox"]
```

**Builder** — construct complex objects step by step with many optional parts; validate at `build()`.

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class HttpRequest:
    method: str
    url: str
    headers: tuple
    body: str | None
    timeout: float

class RequestBuilder:
    def __init__(self, url: str):
        self._url, self._method, self._headers = url, "GET", []
        self._body, self._timeout = None, 5.0

    def method(self, m: str): self._method = m; return self
    def header(self, k: str, v: str): self._headers.append((k, v)); return self
    def body(self, b: str): self._body = b; return self
    def timeout(self, t: float): self._timeout = t; return self

    def build(self) -> HttpRequest:
        if self._method == "GET" and self._body is not None:
            raise ValueError("GET cannot have a body")
        return HttpRequest(self._method, self._url, tuple(self._headers), self._body, self._timeout)

req = RequestBuilder("/orders").method("POST").header("Idempotency-Key", "k1").body("{}").build()
assert req.method == "POST" and req.headers == (("Idempotency-Key", "k1"),)
```

**Prototype** — create new objects by copying an existing configured one (`copy.deepcopy`), e.g., cloning a document template or a game unit.

```python
import copy

class Template:
    def __init__(self, sections): self.sections = sections
    def clone(self): return copy.deepcopy(self)

base = Template(["header", "footer"])
doc = base.clone(); doc.sections.append("body")
assert base.sections == ["header", "footer"]           # original untouched
```

### 8.2 Structural

**Adapter** — make an incompatible interface fit the one your code expects (wrapping third-party SDKs).

```python
class LegacySmsApi:                          # third-party, can't change
    def send_text(self, phone_number, text_body): return f"legacy:{phone_number}:{text_body}"

class SmsSender:                             # what our code expects
    def send(self, to: str, msg: str) -> str: raise NotImplementedError

class LegacySmsAdapter(SmsSender):
    def __init__(self, api: LegacySmsApi): self._api = api
    def send(self, to, msg): return self._api.send_text(phone_number=to, text_body=msg)

assert LegacySmsAdapter(LegacySmsApi()).send("99", "hi") == "legacy:99:hi"
```

**Decorator** — add responsibilities by wrapping an object with the same interface; stack wrappers (pizza toppings, logging/retry/caching around a service).

```python
class DataSource:
    def __init__(self): self.calls = 0
    def read(self, key): self.calls += 1; return f"value:{key}"

class CachingSource:                         # same interface, wraps another source
    def __init__(self, inner): self.inner, self.cache = inner, {}
    def read(self, key):
        if key not in self.cache:
            self.cache[key] = self.inner.read(key)
        return self.cache[key]

class LoggingSource:
    def __init__(self, inner): self.inner, self.log = inner, []
    def read(self, key):
        self.log.append(key)
        return self.inner.read(key)

raw = DataSource()
src = LoggingSource(CachingSource(raw))      # stack: logging → caching → raw
src.read("a"); src.read("a")
assert raw.calls == 1 and src.log == ["a", "a"]
```

**Facade** — one simple entry point over a complex subsystem.

```python
class Inventory:
    def reserve(self, sku): return f"reserved {sku}"
class Payments:
    def charge(self, amt): return f"charged {amt}"
class Shipping:
    def schedule(self, sku): return f"shipping {sku}"

class CheckoutFacade:
    def __init__(self):
        self.inv, self.pay, self.ship = Inventory(), Payments(), Shipping()
    def buy(self, sku, amount):              # callers don't orchestrate 3 subsystems
        return [self.inv.reserve(sku), self.pay.charge(amount), self.ship.schedule(sku)]

assert CheckoutFacade().buy("tee", 500)[1] == "charged 500"
```

**Proxy** — a stand-in that controls access: lazy loading, access control, caching, remote calls.

```python
class Image:
    def __init__(self, path):
        self.path = path
        self.loaded = True                   # imagine expensive disk read
    def show(self): return f"showing {self.path}"

class LazyImage:                             # virtual proxy: load on first use
    def __init__(self, path): self.path, self._real = path, None
    def show(self):
        if self._real is None:
            self._real = Image(self.path)
        return self._real.show()

class ProtectedImage:                        # protection proxy
    def __init__(self, img, user_role): self.img, self.role = img, user_role
    def show(self):
        if self.role != "admin":
            raise PermissionError("admins only")
        return self.img.show()

lazy = LazyImage("a.png")
assert lazy._real is None and lazy.show() == "showing a.png"
try:
    ProtectedImage(lazy, "guest").show(); assert False
except PermissionError:
    pass
```

**Composite** — treat a tree of objects uniformly (file/folder, menu/submenu, org chart).

```python
class File:
    def __init__(self, name, size): self.name, self._size = name, size
    def size(self): return self._size

class Folder:
    def __init__(self, name): self.name, self.children = name, []
    def add(self, node): self.children.append(node); return self
    def size(self): return sum(c.size() for c in self.children)   # same method as File

root = Folder("root").add(File("a", 10)).add(Folder("sub").add(File("b", 5)))
assert root.size() == 15
```

**Bridge** — split an abstraction from its implementation so both vary independently (shapes × renderers, notifications × channels) instead of a class per combination.

```python
class Renderer:
    def circle(self, r): raise NotImplementedError
class SvgRenderer(Renderer):
    def circle(self, r): return f"<circle r={r}>"
class CanvasRenderer(Renderer):
    def circle(self, r): return f"ctx.arc({r})"

class Circle:                                # abstraction holds an implementation
    def __init__(self, r, renderer: Renderer): self.r, self.renderer = r, renderer
    def draw(self): return self.renderer.circle(self.r)

assert Circle(2, SvgRenderer()).draw() == "<circle r=2>"
assert Circle(2, CanvasRenderer()).draw() == "ctx.arc(2)"
```

**Flyweight** — share immutable intrinsic state among many objects to save memory (glyphs in an editor, tree types in a game, seat types in a stadium).

```python
from functools import lru_cache

class SeatType:                              # shared, immutable
    def __init__(self, name, price): self.name, self.price = name, price

@lru_cache(maxsize=None)
def seat_type(name: str, price: int) -> SeatType:
    return SeatType(name, price)

seats = [(row, col, seat_type("GOLD", 300)) for row in range(100) for col in range(100)]
assert len({id(t) for _, _, t in seats}) == 1   # 10,000 seats share one SeatType
```

### 8.3 Behavioral

**Strategy** — interchangeable algorithms behind one interface (pricing, discount, assignment, split type, rate-limit algorithm). In Python a callable often suffices.

```python
from typing import Callable

PricingStrategy = Callable[[int], int]           # hours -> amount

def hourly(rate): return lambda hours: hours * rate
def flat(amount): return lambda hours: amount
# strategies compose
def capped(inner, cap): return lambda hours: min(cap, inner(hours))

class ParkingFee:
    def __init__(self, strategy: PricingStrategy): self.strategy = strategy
    def fee(self, hours): return self.strategy(hours)

assert ParkingFee(hourly(40)).fee(3) == 120
assert ParkingFee(capped(hourly(40), 100)).fee(3) == 100
```

**Observer** — publishers notify subscribers about events without knowing them (notifications, UI updates, stock alerts).

```python
from collections import defaultdict

class EventBus:
    def __init__(self): self._subs = defaultdict(list)
    def subscribe(self, event: str, handler) -> None: self._subs[event].append(handler)
    def unsubscribe(self, event: str, handler) -> None: self._subs[event].remove(handler)
    def publish(self, event: str, payload) -> None:
        for handler in list(self._subs[event]):   # copy: handlers may unsubscribe
            handler(payload)

bus, got = EventBus(), []
email = lambda p: got.append(("email", p))
bus.subscribe("order_shipped", email)
bus.subscribe("order_shipped", lambda p: got.append(("sms", p)))
bus.publish("order_shipped", 42)
bus.unsubscribe("order_shipped", email)
bus.publish("order_shipped", 43)
assert got == [("email", 42), ("sms", 42), ("sms", 43)]
```

**State** — behavior depends on the current state; each state is an object that handles actions and decides the next state (vending machine, ATM, order lifecycle, traffic light). Replaces big `if state == ...` blocks in every method.

```python
class InvalidAction(Exception): ...

class DocState:
    def submit(self, doc): raise InvalidAction(f"{type(self).__name__}: submit")
    def approve(self, doc): raise InvalidAction(f"{type(self).__name__}: approve")
    def reject(self, doc): raise InvalidAction(f"{type(self).__name__}: reject")

class Draft(DocState):
    def submit(self, doc): doc.state = InReview()

class InReview(DocState):
    def approve(self, doc): doc.state = Published()
    def reject(self, doc): doc.state = Draft()

class Published(DocState):
    pass

class Document:
    def __init__(self): self.state = Draft()
    def submit(self): self.state.submit(self)
    def approve(self): self.state.approve(self)
    def reject(self): self.state.reject(self)

d = Document(); d.submit(); d.reject(); d.submit(); d.approve()
assert isinstance(d.state, Published)
try:
    d.submit(); assert False
except InvalidAction:
    pass
```
Lighter alternative for simple lifecycles: an enum + a transition table (`ALLOWED[current] = {next states}`) — used in several problems below. Use full State classes when each state has different *behavior*, not just different allowed transitions.

**Command** — wrap an action as an object (with `execute`/`undo`): undo/redo, macro recording, job queues.

```python
class Editor:
    def __init__(self): self.text = ""

class InsertCommand:
    def __init__(self, editor, s): self.editor, self.s = editor, s
    def execute(self): self.editor.text += self.s
    def undo(self): self.editor.text = self.editor.text[: -len(self.s)]

class History:
    def __init__(self): self.done, self.undone = [], []
    def run(self, cmd): cmd.execute(); self.done.append(cmd); self.undone.clear()
    def undo(self):
        cmd = self.done.pop(); cmd.undo(); self.undone.append(cmd)
    def redo(self):
        cmd = self.undone.pop(); cmd.execute(); self.done.append(cmd)

ed, h = Editor(), History()
h.run(InsertCommand(ed, "Hello")); h.run(InsertCommand(ed, " World"))
h.undo(); assert ed.text == "Hello"
h.redo(); assert ed.text == "Hello World"
```

**Chain of Responsibility** — pass a request along handlers until one handles it (log levels, approval workflows, middleware, ATM note dispensing).

```python
class Handler:
    def __init__(self, nxt=None): self.nxt = nxt
    def handle(self, request):
        return self.nxt.handle(request) if self.nxt else "rejected"

class AuthCheck(Handler):
    def handle(self, req):
        return "401" if not req.get("user") else super().handle(req)

class RateCheck(Handler):
    def handle(self, req):
        return "429" if req.get("count", 0) > 100 else super().handle(req)

class Endpoint(Handler):
    def handle(self, req): return "200"

pipeline = AuthCheck(RateCheck(Endpoint()))
assert pipeline.handle({}) == "401"
assert pipeline.handle({"user": "u", "count": 500}) == "429"
assert pipeline.handle({"user": "u"}) == "200"
```

**Template method** — fixed algorithm skeleton in a base class; subclasses fill in steps.

```python
from abc import ABC, abstractmethod

class Report(ABC):
    def generate(self) -> str:                 # the fixed skeleton
        return "\n".join([self.header(), *self.rows(), self.footer()])
    def header(self): return "REPORT"
    def footer(self): return "END"
    @abstractmethod
    def rows(self) -> list[str]: ...

class SalesReport(Report):
    def rows(self): return ["tee,2", "mug,1"]

assert SalesReport().generate() == "REPORT\ntee,2\nmug,1\nEND"
```

**Iterator** — traverse a collection without exposing its internals (paginated results, tree traversal).

```python
class Paginated:
    def __init__(self, fetch_page): self.fetch_page = fetch_page
    def __iter__(self):
        page = 0
        while items := self.fetch_page(page):
            yield from items
            page += 1

data = list(range(7))
pages = Paginated(lambda p: data[p * 3:(p + 1) * 3])
assert list(pages) == data
```

**Mediator** — objects communicate through a central mediator instead of referencing each other (chat room, air traffic control, UI form widgets).

```python
class ChatRoom:
    def __init__(self): self.users = {}
    def join(self, user): self.users[user.name] = user; user.room = self
    def send(self, sender, to, msg): self.users[to].inbox.append((sender, msg))

class User:
    def __init__(self, name): self.name, self.inbox, self.room = name, [], None
    # no direct reference to `to`
    def send(self, to, msg): self.room.send(self.name, to, msg)

room, a, b = ChatRoom(), User("a"), User("b")
room.join(a); room.join(b); a.send("b", "hi")
assert b.inbox == [("a", "hi")]
```

**Memento** — capture and restore an object's state without exposing internals (undo snapshots, game save).

```python
class TextBox:
    def __init__(self): self._text = ""
    def type(self, s): self._text += s
    def save(self): return ("memento", self._text)          # opaque snapshot
    def restore(self, m): self._text = m[1]
    @property
    def text(self): return self._text

box = TextBox(); box.type("hello"); snap = box.save(); box.type(" world")
box.restore(snap)
assert box.text == "hello"
```

**Visitor** — add operations over a fixed set of element types without changing them (exporting a document tree to HTML/Markdown, computing taxes over different item types).

```python
class Book:
    def __init__(self, price): self.price = price
    def accept(self, v): return v.visit_book(self)

class Food:
    def __init__(self, price): self.price = price
    def accept(self, v): return v.visit_food(self)

class TaxVisitor:                       # new operation = new visitor, items unchanged
    def visit_book(self, b): return b.price * 5 // 100
    def visit_food(self, f): return 0

items = [Book(1000), Food(500)]
assert sum(i.accept(TaxVisitor()) for i in items) == 50
```

### 8.4 Other patterns that appear in LLD answers

- **Repository:** a collection-like interface over storage (`OrderRepository.get/save/find_by_user`). Services don't know if it's a dict or a DB.
- **Specification / filter objects:** composable predicates for search (`ByAuthor("x") & Available()`).
- **Null object:** a do-nothing implementation instead of `None` checks (`NoDiscount`, `NullLogger`).
- **Object pool:** reuse expensive objects (DB connections) — built in [59. Problem: Connection pool](#59-problem-connection-pool).
- **Producer–consumer, thread pool, read-write lock:** concurrency patterns — see the [concurrency part](#42-concurrency-what-gets-asked).

```python
class Spec:
    def __init__(self, pred): self.pred = pred
    def __call__(self, x): return self.pred(x)
    def __and__(self, other): return Spec(lambda x: self(x) and other(x))
    def __or__(self, other): return Spec(lambda x: self(x) or other(x))
    def __invert__(self): return Spec(lambda x: not self(x))

books = [{"author": "a", "available": True}, {"author": "a", "available": False}, {"author": "b", "available": True}]
by_a = Spec(lambda b: b["author"] == "a")
available = Spec(lambda b: b["available"])
assert [b for b in books if (by_a & available)(b)] == [books[0]]
assert len([b for b in books if (~by_a | ~available)(b)]) == 2
```

### 8.5 Pattern quick-pick table

| If the problem says… | Reach for |
|---|---|
| "different pricing / discount / assignment / split rules" | Strategy |
| "notify users / update display when X changes" | Observer |
| "machine/lifecycle with states; actions valid only in some states" | State (or enum + transition table) |
| "create objects by type string / config" | Factory (+ registry) |
| "many optional parameters" | Builder |
| "undo/redo, queue actions" | Command (+ Memento) |
| "request passes through levels/handlers" | Chain of Responsibility |
| "tree of items treated uniformly" | Composite |
| "wrap a third-party API" | Adapter |
| "add features on top without subclassing" | Decorator |
| "simplify a complex subsystem" | Facade |
| "only one instance" | Singleton (or inject one instance) |

---

## 9. Machine-coding: layout, errors, tests

### 9.1 Project layout (90-minute round)

```
app/
  models/          # dataclasses + enums (Order, Item, OrderStatus)
  strategies/      # interchangeable policies (pricing, assignment)
  repositories/    # in-memory stores behind small interfaces
  services/        # business logic; enforces invariants
  exceptions.py    # DomainError, NotFound, InvalidState, ...
  main.py          # demo driver / command parser
tests/
  test_services.py
```

- In-memory repositories behind an interface → "swap for a DB" is one class.
- Parse commands in `main.py`; keep business logic free of I/O and parsing.
- If the problem gives a command format (`ADD_USER u1 ...`), write a tiny dispatcher: `{"ADD_USER": service.add_user, ...}`.

### 9.2 Repository example

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass

@dataclass
class User:
    id: str
    name: str

class UserRepository(ABC):
    @abstractmethod
    def get(self, user_id: str) -> User: ...
    @abstractmethod
    def save(self, user: User) -> None: ...

class InMemoryUserRepository(UserRepository):
    def __init__(self): self._rows: dict[str, User] = {}
    def get(self, user_id):
        if user_id not in self._rows:
            raise KeyError(user_id)
        return self._rows[user_id]
    def save(self, user): self._rows[user.id] = user

repo = InMemoryUserRepository(); repo.save(User("u1", "Asha"))
assert repo.get("u1").name == "Asha"
```

### 9.3 Testing in the round

```python
import unittest

class Wallet:
    def __init__(self): self.balance = 0
    def add(self, amt):
        if amt <= 0: raise ValueError("positive only")
        self.balance += amt

class WalletTest(unittest.TestCase):
    def test_add(self):
        w = Wallet(); w.add(10)
        self.assertEqual(w.balance, 10)

    def test_rejects_non_positive_and_keeps_state(self):
        w = Wallet()
        with self.assertRaises(ValueError):
            w.add(0)
        self.assertEqual(w.balance, 0)

result = unittest.TextTestRunner(verbosity=0).run(unittest.defaultTestLoader.loadTestsFromTestCase(WalletTest))
assert result.wasSuccessful()
```

What to test: each strategy alone; invalid transitions; boundaries (exact hour, empty cart, full lot); **failed operations leave state unchanged**; idempotent repeats.

---

## 10. Problem: Tic-tac-toe and Snake & Ladder (game modeling)

**How often asked:** common · **Builds on:** [3. OOP in depth](#3-oop-in-depth), [4. Class relationships and UML](#4-class-relationships-and-uml), [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)

**Game modeling pattern:** `Game` owns board, players, turn order, status; `Player` is data; moves are validated before changing anything; win check is separate; randomness (dice) is injected so tests are deterministic.

**Tic-tac-toe (N×N, O(1) win check):** keep counters per row, column and both diagonals (+1 for X, −1 for O); a counter reaching ±N means a win.

```python
class TicTacToe:
    def __init__(self, n: int = 3, players=("X", "O")):
        self.n, self.players = n, players
        self.board = [[None] * n for _ in range(n)]
        self._rows, self._cols = [0] * n, [0] * n
        self._diag = self._anti = 0
        self.turn, self.moves, self.winner = 0, 0, None

    def play(self, r: int, c: int) -> str | None:
        """Returns the winner, 'DRAW', or None if the game continues."""
        if self.winner:
            raise ValueError("game over")
        if not (0 <= r < self.n and 0 <= c < self.n) or self.board[r][c]:
            raise ValueError("invalid move")
        player = self.players[self.turn]
        self.board[r][c] = player
        d = 1 if self.turn == 0 else -1
        self._rows[r] += d; self._cols[c] += d
        if r == c: self._diag += d
        if r + c == self.n - 1: self._anti += d
        self.moves += 1
        if self.n in (abs(self._rows[r]), abs(self._cols[c]), abs(self._diag), abs(self._anti)):
            self.winner = player
        elif self.moves == self.n * self.n:
            self.winner = "DRAW"
        self.turn ^= 1
        return self.winner

g = TicTacToe()
for move in [(0, 0), (1, 0), (1, 1), (2, 0)]:
    assert g.play(*move) is None
assert g.play(2, 2) == "X"

g = TicTacToe()
for r, c in [(0, 0), (0, 1), (0, 2), (1, 1), (1, 0), (1, 2), (2, 1), (2, 0)]:
    assert g.play(r, c) is None
assert g.play(2, 2) == "DRAW"
```

**Snake & Ladder:** board 1..100; snakes `head → tail`, ladders `bottom → top`; players take turns in order; roll moves forward; a roll that passes 100 means no move; landing on a snake/ladder jumps; first to exactly 100 wins.

```python
import random
from collections import deque

class SnakeLadderGame:
    def __init__(self, players: list[str], snakes: dict[int, int], ladders: dict[int, int],
                 size: int = 100, dice=None):
        if len(set(players)) != len(players) or len(players) < 2:
            raise ValueError("need 2+ unique players")
        for head, tail in snakes.items():
            if not (1 < tail < head < size):
                raise ValueError(f"bad snake {head}->{tail}")
        for bottom, top in ladders.items():
            if not (1 < bottom < top < size):
                raise ValueError(f"bad ladder {bottom}->{top}")
        if set(snakes) & set(ladders):
            raise ValueError("a cell can't start both a snake and a ladder")
        self.size = size
        self.jumps = {**snakes, **ladders}
        self.positions = {p: 0 for p in players}
        self.turns = deque(players)
        self.dice = dice or (lambda: random.randint(1, 6))
        self.winner: str | None = None

    def play_turn(self) -> tuple[str, int, int]:
        if self.winner:
            raise RuntimeError("game over")
        player = self.turns[0]
        roll = self.dice()
        target = self.positions[player] + roll
        if target <= self.size:                     # overshoot = stay
            self.positions[player] = self.jumps.get(target, target)
        if self.positions[player] == self.size:
            self.winner = player
        else:
            self.turns.rotate(-1)
        return player, roll, self.positions[player]

rolls = iter([1, 6, 3, 4, 2])
g = SnakeLadderGame(["ann", "bob"], snakes={9: 2}, ladders={4: 96}, dice=lambda: next(rolls))
assert g.play_turn() == ("ann", 1, 1)
assert g.play_turn() == ("bob", 6, 6)
assert g.play_turn() == ("ann", 3, 96)               # ladder 4 → 96
assert g.play_turn() == ("bob", 4, 10)
assert g.play_turn() == ("ann", 2, 98)
rolls2 = iter([5, 1, 2])
g.dice = lambda: next(rolls2)
assert g.play_turn() == ("bob", 5, 15)
assert g.play_turn() == ("ann", 1, 99)
g.play_turn()                                         # bob 15 → 17
g.dice = lambda: 1
assert g.play_turn() == ("ann", 1, 100) and g.winner == "ann"
```

**Follow-ups:** multiple dice / "6 gives another turn" (rule strategy) · undo (Command) · AI player for tic-tac-toe (strategy `choose_move`, minimax) · chess: `Piece` subclasses generate candidate moves; `Board.is_legal(move)` also checks the king isn't left in check — candidate move ≠ legal move.

---

## 11. Problem: Chess

**How often asked:** common · **Builds on:** [3.5 Polymorphism](#35-polymorphism), [3.4 Inheritance](#34-inheritance), [7.4 Validate → compute → commit](#74-validate--compute--commit)

Chess is the classic "show me polymorphism" problem. Nobody expects castling and en passant in 45 minutes; they expect a clean way for each piece to describe its own moves, and the one rule that ties them together: **a move is legal only if it doesn't leave your own king in check.**

**Clarify:** two players, standard board; validate moves; detect check, checkmate and stalemate. Usually out of scope at first: castling, en passant, promotion, timers, draw by repetition — mention them as extensions.

**Entities**
- `Color` · `Piece` (abstract) → `King`, `Queen`, `Rook`, `Bishop`, `Knight`, `Pawn`. Each knows which squares it attacks and moves to.
- `Board` — a dict from square to piece (sparse: only occupied squares).
- `Game` — whose turn, move history, legality, status.

**The key design idea:** most pieces are "a set of directions, and whether they slide". Rook = 4 straight directions, sliding; bishop = 4 diagonals, sliding; queen = both; king = all 8, one step; knight = 8 jumps. One shared `attacks()` handles all of them, and only the pawn (which moves and captures differently) overrides it.

```python
from enum import Enum

class Color(Enum):
    WHITE = "white"
    BLACK = "black"

    @property
    def other(self) -> "Color":
        return Color.BLACK if self is Color.WHITE else Color.WHITE

Square = tuple[int, int]                     # (file 0–7 = a–h, rank 0–7 = 1–8)
ORTH = ((1, 0), (-1, 0), (0, 1), (0, -1))
DIAG = ((1, 1), (1, -1), (-1, 1), (-1, -1))

class Piece:
    directions: tuple = ()
    slides = False

    def __init__(self, color: Color):
        self.color = color

    def attacks(self, board: dict, sq: Square) -> list[Square]:
        out = []
        for df, dr in self.directions:
            f, r = sq[0] + df, sq[1] + dr
            while 0 <= f < 8 and 0 <= r < 8:
                target = board.get((f, r))
                if target is None or target.color is not self.color:
                    out.append((f, r))
                if target is not None or not self.slides:
                    break                     # blocked, or a one-step piece
                f, r = f + df, r + dr
        return out

    def moves(self, board: dict, sq: Square) -> list[Square]:
        return self.attacks(board, sq)

class Rook(Piece):   directions, slides = ORTH, True
class Bishop(Piece): directions, slides = DIAG, True
class Queen(Piece):  directions, slides = ORTH + DIAG, True
class King(Piece):   directions = ORTH + DIAG
class Knight(Piece): directions = ((1, 2), (2, 1), (-1, 2), (-2, 1), (1, -2), (2, -1), (-1, -2), (-2, -1))

class Pawn(Piece):
    def _step(self) -> int:
        return 1 if self.color is Color.WHITE else -1

    def attacks(self, board, sq):
        out = []
        for df in (-1, 1):
            t = (sq[0] + df, sq[1] + self._step())
            p = board.get(t)
            if p is not None and p.color is not self.color:
                out.append(t)
        return out

    def moves(self, board, sq):
        out, one = self.attacks(board, sq), (sq[0], sq[1] + self._step())
        if 0 <= one[1] < 8 and board.get(one) is None:
            out.append(one)
            two = (sq[0], sq[1] + 2 * self._step())
            start_rank = 1 if self.color is Color.WHITE else 6
            if sq[1] == start_rank and board.get(two) is None:
                out.append(two)
        return out

class IllegalMove(Exception): ...

def parse(name: str) -> Square:
    return ord(name[0]) - ord("a"), int(name[1]) - 1

class Game:
    BACK_RANK = (Rook, Knight, Bishop, Queen, King, Bishop, Knight, Rook)

    def __init__(self):
        self.board: dict[Square, Piece] = {}
        for f, cls in enumerate(self.BACK_RANK):
            self.board[(f, 0)], self.board[(f, 7)] = cls(Color.WHITE), cls(Color.BLACK)
            self.board[(f, 1)], self.board[(f, 6)] = Pawn(Color.WHITE), Pawn(Color.BLACK)
        self.turn, self.history = Color.WHITE, []

    @staticmethod
    def _after(board: dict, frm: Square, to: Square) -> dict:
        new = dict(board)
        new[to] = new.pop(frm)                 # captures by overwriting
        return new

    @staticmethod
    def in_check(board: dict, color: Color) -> bool:
        king = next(sq for sq, p in board.items() if isinstance(p, King) and p.color is color)
        return any(king in p.attacks(board, sq) for sq, p in board.items() if p.color is not color)

    def legal_moves(self, color: Color) -> list[tuple[Square, Square]]:
        return [(sq, to) for sq, p in self.board.items() if p.color is color
                for to in p.moves(self.board, sq)
                if not self.in_check(self._after(self.board, sq, to), color)]

    def move(self, frm: str, to: str) -> str:
        f, t = parse(frm), parse(to)
        piece = self.board.get(f)
        if piece is None or piece.color is not self.turn:
            raise IllegalMove(f"no {self.turn.value} piece on {frm}")
        if t not in piece.moves(self.board, f):
            raise IllegalMove(f"{type(piece).__name__} can't move {frm}→{to}")
        after = self._after(self.board, f, t)
        if self.in_check(after, self.turn):
            raise IllegalMove("that would leave your king in check")
        self.board, self.turn = after, self.turn.other
        self.history.append((frm, to))
        return self.status()

    def status(self) -> str:
        if self.legal_moves(self.turn):
            return "CHECK" if self.in_check(self.board, self.turn) else "ONGOING"
        return "CHECKMATE" if self.in_check(self.board, self.turn) else "STALEMATE"

g = Game()
# blocked by own pawn; not black's turn
for bad in [("e1", "e2"), ("e7", "e5")]:
    try:
        g.move(*bad); raise AssertionError("should be illegal")
    except IllegalMove:
        pass
assert g.move("f2", "f3") == "ONGOING"
assert g.move("e7", "e5") == "ONGOING"
assert g.move("g2", "g4") == "ONGOING"
# fool's mate, the shortest possible game
assert g.move("d8", "h4") == "CHECKMATE"
assert len(g.history) == 4
```

**Why each piece class, and not one big `if piece_type == ...`?** Adding a fairy piece (a "camel" that jumps 3+1) is one new subclass with its directions. Rules that involve the whole game — check, castling rights, en passant — live in `Game`, because a single piece can't know them.

**Extensions, and where each goes**
- *Castling:* a `Game` rule — king and rook haven't moved (track it in history), squares between are empty, and the king doesn't pass through an attacked square.
- *Promotion:* `Game.move` takes an optional piece type when a pawn reaches the last rank.
- *Undo:* the move history already exists; store captured pieces with each move (Command pattern) — see [23. Problem: Text editor with undo and redo](#23-problem-text-editor-with-undo-and-redo).
- *Timers:* a `Clock` per player, injected so tests don't sleep.
- *Performance:* recomputing check by scanning every piece is fine here; engines use bitboards (64-bit integers per piece type), which an LLD round never expects.

---

## 12. Problem: Snake game

**How often asked:** occasional (LeetCode 353, "Design Snake Game") · **Builds on:** [7.3 Allocation with fast lookup](#73-allocation-with-fast-lookup)

The snake is a queue: the head is added at the front each move, and the tail leaves from the back — unless it just ate. The only subtle bug is the order of operations: **the tail moves before you check whether the head hits the body**, so moving into the square the tail is leaving is allowed.

- Body as a `deque` (O(1) at both ends) plus a `set` of occupied cells (O(1) collision checks).
- Food appears in a fixed order (as LeetCode gives it).

```python
from collections import deque

class SnakeGame:
    DIRS = {"U": (-1, 0), "D": (1, 0), "L": (0, -1), "R": (0, 1)}

    def __init__(self, width: int, height: int, food: list[list[int]]):
        self.w, self.h = width, height
        self.food = deque(tuple(f) for f in food)
        self.body = deque([(0, 0)])            # head at the left
        self.cells = {(0, 0)}
        self.score = 0

    def move(self, direction: str) -> int:
        dr, dc = self.DIRS[direction]
        head = (self.body[0][0] + dr, self.body[0][1] + dc)
        if not (0 <= head[0] < self.h and 0 <= head[1] < self.w):
            return -1                          # hit a wall
        if self.food and self.food[0] == head:
            self.food.popleft()
            self.score += 1                    # grows: the tail stays
        else:
            self.cells.discard(self.body.pop())   # the tail leaves first
        if head in self.cells:
            return -1                          # bit itself
        self.body.appendleft(head)
        self.cells.add(head)
        return self.score

game = SnakeGame(3, 2, [[1, 2], [0, 1]])
assert [game.move(d) for d in "RDRUL"] == [0, 0, 1, 1, 2]
assert game.move("U") == -1

full = SnakeGame(2, 2, [[0, 1], [1, 1], [1, 0]])
# the snake now fills the whole grid
assert [full.move(d) for d in "RDL"] == [1, 2, 3]
# moving into the cell the tail just left is fine
assert full.move("U") == 3
```

**Follow-ups:** random food placement on a free cell (keep a list of free cells, swap-remove for O(1)) · a game loop with a tick and keyboard input (separate the model from the loop, so this class stays testable) · two snakes (collision checks against both bodies).

---

## 13. Problem: Library management

**How often asked:** common · **Builds on:** [3. OOP in depth](#3-oop-in-depth), [4. Class relationships and UML](#4-class-relationships-and-uml), [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)

**Clarify:** catalog of books with multiple physical copies; members borrow (limit per member), return, reserve when none available; fines for late returns; search by title/author.

**Entities**
- `Book(isbn, title, author)` — catalog information. `BookCopy(id, isbn, status, held_for, hold_until)` — the physical item (**key modeling point: book ≠ copy**).
- `Member(id, name, max_loans, fines)` · `Loan(id, copy_id, member_id, due, returned_on)`.
- Reservation queue per ISBN (FIFO). `FinePolicy` strategy. Observer callback to notify members.

**Invariants:** a copy is AVAILABLE, ON_HOLD (for one member), or BORROWED (one open loan); a member's open loans ≤ max; members with unpaid fines can't borrow.

```python
from collections import defaultdict, deque
from dataclasses import dataclass
from datetime import date, timedelta
from enum import Enum, auto
from itertools import count

class CopyStatus(Enum):
    AVAILABLE = auto()
    ON_HOLD = auto()
    BORROWED = auto()

class LibraryError(Exception): ...

@dataclass(frozen=True)
class Book:
    isbn: str
    title: str
    author: str

@dataclass
class BookCopy:
    id: int
    isbn: str
    status: CopyStatus = CopyStatus.AVAILABLE
    held_for: str | None = None
    hold_until: date | None = None

@dataclass
class Member:
    id: str
    name: str
    max_loans: int = 3
    fines: int = 0

@dataclass
class Loan:
    id: int
    copy_id: int
    member_id: str
    due: date
    returned_on: date | None = None

def per_day_fine(rate: int = 10, cap: int = 500):
    return lambda days_late: min(cap, max(0, days_late) * rate)

class Library:
    LOAN_DAYS, HOLD_DAYS = 14, 3

    def __init__(self, fine_policy=per_day_fine(), notify=lambda member_id, msg: None):
        self.books: dict[str, Book] = {}
        self.copies: dict[int, BookCopy] = {}
        self.members: dict[str, Member] = {}
        self.loans: dict[int, Loan] = {}
        self._open_loan_by_copy: dict[int, int] = {}
        self._reservations: dict[str, deque] = defaultdict(deque)
        self._fine, self._notify = fine_policy, notify
        self._copy_ids, self._loan_ids = count(1), count(1)

    def add_book(self, book: Book, copies: int = 1) -> list[int]:
        self.books[book.isbn] = book
        ids = []
        for _ in range(copies):
            c = BookCopy(next(self._copy_ids), book.isbn)
            self.copies[c.id] = c
            ids.append(c.id)
        return ids

    def add_member(self, m: Member) -> None:
        self.members[m.id] = m

    def search(self, title: str = "", author: str = "") -> list[Book]:
        t, a = title.lower(), author.lower()
        return [b for b in self.books.values() if t in b.title.lower() and a in b.author.lower()]

    def _usable_by(self, c: BookCopy, member_id: str, today: date) -> bool:
        if c.status is CopyStatus.AVAILABLE:
            return True
        if c.status is CopyStatus.ON_HOLD:
            # expired holds are free
            return c.held_for == member_id or c.hold_until < today
        return False

    def checkout(self, member_id: str, isbn: str, today: date) -> Loan:
        m = self.members.get(member_id)
        if m is None or isbn not in self.books:
            raise LibraryError("unknown member or book")
        if m.fines:
            raise LibraryError("pay fines first")
        open_loans = sum(1 for l in self.loans.values() if l.member_id == member_id and not l.returned_on)
        if open_loans >= m.max_loans:
            raise LibraryError("loan limit reached")
        candidates = [c for c in self.copies.values() if c.isbn == isbn and self._usable_by(c, member_id, today)]
        if not candidates:
            raise LibraryError("no copy available; reserve instead")
        # own hold first
        copy = min(candidates, key=lambda c: (c.held_for != member_id, c.id))
        if member_id in self._reservations[isbn]:
            self._reservations[isbn].remove(member_id)
        copy.status, copy.held_for, copy.hold_until = CopyStatus.BORROWED, None, None
        loan = Loan(next(self._loan_ids), copy.id, member_id, today + timedelta(days=self.LOAN_DAYS))
        self.loans[loan.id] = loan
        self._open_loan_by_copy[copy.id] = loan.id
        return loan

    def return_copy(self, copy_id: int, today: date) -> int:
        loan_id = self._open_loan_by_copy.pop(copy_id, None)
        if loan_id is None:
            raise LibraryError("copy is not on loan")
        loan = self.loans[loan_id]
        loan.returned_on = today
        fine = self._fine((today - loan.due).days)
        self.members[loan.member_id].fines += fine
        copy = self.copies[copy_id]
        queue_ = self._reservations[copy.isbn]
        if queue_:
            nxt = queue_.popleft()
            copy.status, copy.held_for = CopyStatus.ON_HOLD, nxt
            copy.hold_until = today + timedelta(days=self.HOLD_DAYS)
            self._notify(nxt, f"'{self.books[copy.isbn].title}' is ready for pickup")
        else:
            copy.status = CopyStatus.AVAILABLE
        return fine

    def reserve(self, member_id: str, isbn: str, today: date) -> int:
        if any(self._usable_by(c, member_id, today) for c in self.copies.values() if c.isbn == isbn):
            raise LibraryError("a copy is available; check it out")
        q = self._reservations[isbn]
        if member_id in q:
            raise LibraryError("already reserved")
        q.append(member_id)
        return len(q)

    def pay_fine(self, member_id: str, amount: int) -> None:
        m = self.members[member_id]
        if not 0 < amount <= m.fines:
            raise LibraryError("invalid amount")
        m.fines -= amount

msgs = []
lib = Library(notify=lambda mid, msg: msgs.append((mid, msg)))
lib.add_book(Book("111", "Clean Code", "Robert Martin"), copies=1)
lib.add_member(Member("m1", "Asha")); lib.add_member(Member("m2", "Ravi"))
d0 = date(2025, 1, 1)
loan = lib.checkout("m1", "111", d0)
assert lib.search(author="martin")[0].isbn == "111"
try:
    lib.checkout("m2", "111", d0); assert False
except LibraryError:
    pass
assert lib.reserve("m2", "111", d0) == 1
fine = lib.return_copy(loan.copy_id, d0 + timedelta(days=17))     # 3 days late
assert fine == 30 and lib.members["m1"].fines == 30
assert msgs == [("m2", "'Clean Code' is ready for pickup")]
try:
    # fines + held for m2
    lib.checkout("m1", "111", d0 + timedelta(days=17)); assert False
except LibraryError:
    pass
loan2 = lib.checkout("m2", "111", d0 + timedelta(days=18))
assert lib.copies[loan2.copy_id].status is CopyStatus.BORROWED
lib.pay_fine("m1", 30)
assert lib.members["m1"].fines == 0
```

**Follow-ups:** renewals (extend `due` if nobody reserved) · e-books (copy without physical status, concurrent loan licenses) · search at scale (inverted index) · two librarians checking out the last copy (lock around checkout or a conditional DB update).

#### The modelling decision that decides this interview

Most weak answers have a `Book` with a `status` field. Then the library owns two copies of *Clean Code*, one is borrowed, and `Book.status` can't say what's true. Splitting **catalogue entry** (`Book`: what the title is) from **physical item** (`BookCopy`: where this copy is) fixes that, and it's the same split you'll use again and again: product vs inventory unit, flight vs seat, movie vs show. Say it out loud early; interviewers listen for it.

Reservations are where the second bug hides: when a copy is returned and someone is waiting, the copy must go to **that member** (`ON_HOLD` for a few days), not back to the shelf where the next walk-in could take it.

---

## 14. Problem: Meeting room scheduler / calendar

**How often asked:** common · **Builds on:** [3. OOP in depth](#3-oop-in-depth), [4. Class relationships and UML](#4-class-relationships-and-uml), [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)

**Clarify:** rooms with capacity; book `[start, end)` for N attendees; pick a room automatically; cancel; list a room's bookings.

**Key idea:** keep each room's bookings **sorted by start**. A new interval `[s, e)` is free iff the booking just before it ends ≤ s and the booking just after starts ≥ e → `bisect`, O(log n) check.

**Room choice strategy:** smallest room with enough capacity that's free (saves big rooms).

```python
import bisect
from dataclasses import dataclass
from itertools import count

@dataclass(frozen=True)
class Booking:
    id: int
    room_id: str
    start: int
    end: int
    attendees: int

class Room:
    def __init__(self, room_id: str, capacity: int):
        self.id, self.capacity = room_id, capacity
        self._starts: list[int] = []
        self._bookings: list[Booking] = []      # sorted by start, non-overlapping

    def is_free(self, start: int, end: int) -> bool:
        i = bisect.bisect_left(self._starts, start)
        if i > 0 and self._bookings[i - 1].end > start:
            return False
        if i < len(self._bookings) and self._bookings[i].start < end:
            return False
        return True

    def add(self, booking: Booking) -> None:
        i = bisect.bisect_left(self._starts, booking.start)
        self._starts.insert(i, booking.start)
        self._bookings.insert(i, booking)

    def remove(self, booking: Booking) -> None:
        i = self._bookings.index(booking)
        del self._starts[i], self._bookings[i]

    def bookings(self) -> list[Booking]:
        return list(self._bookings)

class Scheduler:
    def __init__(self, rooms):
        self._rooms = sorted(rooms, key=lambda r: (r.capacity, r.id))
        self._by_id: dict[int, Booking] = {}
        self._ids = count(1)

    def book(self, start: int, end: int, attendees: int) -> Booking:
        if start >= end or attendees <= 0:
            raise ValueError("invalid request")
        for room in self._rooms:                       # smallest fitting room first
            if room.capacity >= attendees and room.is_free(start, end):
                booking = Booking(next(self._ids), room.id, start, end, attendees)
                room.add(booking)
                self._by_id[booking.id] = booking
                return booking
        raise LookupError("no room available")

    def cancel(self, booking_id: int) -> None:
        booking = self._by_id.pop(booking_id)
        next(r for r in self._rooms if r.id == booking.room_id).remove(booking)

s = Scheduler([Room("big", 10), Room("small", 4)])
b1 = s.book(9, 10, 3)
assert b1.room_id == "small"
assert s.book(9, 10, 3).room_id == "big"          # small is busy -> big
try:
    s.book(9, 11, 2); assert False
except LookupError:
    pass
assert s.book(10, 11, 4).room_id == "small"       # [9,10) and [10,11) don't overlap
s.cancel(b1.id)
assert s.book(9, 10, 2).room_id == "small"
```

**Follow-ups**
- *Recurring meetings?* Expand occurrences in a window and book all-or-nothing.
- *Find the earliest free slot of length L for a set of people?* Merge everyone's busy intervals (sort + merge), then scan gaps.
- *Many rooms?* Index rooms by capacity; an interval tree per room for overlap queries.

#### Why a sorted list and not a minute-by-minute grid?

A boolean array per room per minute (`busy[room][minute]`) makes the overlap check trivial and is a perfectly good answer for a small, fixed horizon (one day, 15-minute slots). It falls apart for arbitrary times, long horizons and many rooms: memory grows with time, not with bookings, and a one-hour check touches 60 cells. Keeping each room's bookings sorted and binary-searching the neighbours costs O(log n) per check and memory proportional to bookings. If the interviewer pushes further (thousands of bookings per room, frequent range queries), an **interval tree** answers "anything overlapping [s, e)?" in O(log n + k).

---

## 15. Problem: Hotel booking

**How often asked:** common · **Builds on:** [3. OOP in depth](#3-oop-in-depth), [4. Class relationships and UML](#4-class-relationships-and-uml), [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)

**Clarify:** rooms of types (single/double/suite) with nightly rates; search availability for a date range; book (system picks a room); check-in/check-out; cancel; weekend surcharge.

**Entities**
- `Room(number, type, rate)` · `Reservation(id, guest, room, check_in, check_out, status, amount)`.
- `ReservationStatus`: `RESERVED → CHECKED_IN → CHECKED_OUT`, `RESERVED → CANCELLED` (transition table).
- `PricingStrategy` — per night; weekend surcharge.
- `Hotel` — owns rooms and reservations.

**Key idea:** a stay is a half-open date range `[check_in, check_out)`; two stays conflict iff `a.in < b.out and b.in < a.out`. Only RESERVED/CHECKED_IN reservations block a room.

```python
from dataclasses import dataclass
from datetime import date, timedelta
from enum import Enum, auto
from itertools import count

class RoomType(Enum):
    SINGLE = auto()
    DOUBLE = auto()
    SUITE = auto()

class ResStatus(Enum):
    RESERVED = auto()
    CHECKED_IN = auto()
    CHECKED_OUT = auto()
    CANCELLED = auto()

ALLOWED = {
    ResStatus.RESERVED: {ResStatus.CHECKED_IN, ResStatus.CANCELLED},
    ResStatus.CHECKED_IN: {ResStatus.CHECKED_OUT},
    ResStatus.CHECKED_OUT: set(),
    ResStatus.CANCELLED: set(),
}
BLOCKING = {ResStatus.RESERVED, ResStatus.CHECKED_IN}

class HotelError(Exception): ...

@dataclass(frozen=True)
class Room:
    number: int
    type: RoomType
    rate: int

@dataclass
class Reservation:
    id: int
    guest: str
    room: int
    check_in: date
    check_out: date
    amount: int
    status: ResStatus = ResStatus.RESERVED

def weekend_surcharge(pct: int = 20):
    def price(room: Room, check_in: date, check_out: date) -> int:
        total, d = 0, check_in
        while d < check_out:
            weekend = d.weekday() in (4, 5)              # Friday and Saturday nights
            total += room.rate * (100 + pct) // 100 if weekend else room.rate
            d += timedelta(days=1)
        return total
    return price

class Hotel:
    def __init__(self, rooms: list[Room], pricing=weekend_surcharge()):
        self.rooms = {r.number: r for r in rooms}
        self.reservations: dict[int, Reservation] = {}
        self._pricing, self._ids = pricing, count(1)

    def _is_free(self, room_no: int, check_in: date, check_out: date) -> bool:
        return not any(r.room == room_no and r.status in BLOCKING
                       and r.check_in < check_out and check_in < r.check_out
                       for r in self.reservations.values())

    def search(self, room_type: RoomType, check_in: date, check_out: date) -> list[Room]:
        if check_in >= check_out:
            raise HotelError("check-out must be after check-in")
        return [r for r in sorted(self.rooms.values(), key=lambda r: r.number)
                if r.type is room_type and self._is_free(r.number, check_in, check_out)]

    def book(self, guest: str, room_type: RoomType, check_in: date, check_out: date) -> Reservation:
        free = self.search(room_type, check_in, check_out)
        if not free:
            raise HotelError("no room available")
        room = free[0]
        res = Reservation(next(self._ids), guest, room.number, check_in, check_out,
                          self._pricing(room, check_in, check_out))
        self.reservations[res.id] = res
        return res

    def _move(self, res_id: int, new: ResStatus) -> Reservation:
        res = self.reservations[res_id]
        if new not in ALLOWED[res.status]:
            raise HotelError(f"{res.status.name} -> {new.name} not allowed")
        res.status = new
        return res

    def check_in(self, res_id: int, today: date) -> None:
        res = self.reservations[res_id]
        if not res.check_in <= today < res.check_out:
            raise HotelError("not within the stay dates")
        self._move(res_id, ResStatus.CHECKED_IN)

    def check_out(self, res_id: int) -> None: self._move(res_id, ResStatus.CHECKED_OUT)
    def cancel(self, res_id: int) -> None: self._move(res_id, ResStatus.CANCELLED)

h = Hotel([Room(101, RoomType.DOUBLE, 1000), Room(102, RoomType.DOUBLE, 1000), Room(201, RoomType.SUITE, 5000)])
# Thu, Fri, Sat nights
r1 = h.book("asha", RoomType.DOUBLE, date(2024, 5, 2), date(2024, 5, 5))
assert r1.room == 101 and r1.amount == 1000 + 1200 + 1200
r2 = h.book("ravi", RoomType.DOUBLE, date(2024, 5, 4), date(2024, 5, 6))
assert r2.room == 102
try:
    h.book("x", RoomType.DOUBLE, date(2024, 5, 4), date(2024, 5, 5)); assert False
except HotelError:
    pass
# back-to-back ok
assert [r.number for r in h.search(RoomType.DOUBLE, date(2024, 5, 5), date(2024, 5, 6))] == [101]
h.cancel(r2.id)
assert [r.number for r in h.search(RoomType.DOUBLE, date(2024, 5, 4), date(2024, 5, 5))] == [102]
h.check_in(r1.id, date(2024, 5, 2)); h.check_out(r1.id)
try:
    h.cancel(r1.id); assert False
except HotelError:
    pass
```

**Follow-ups:** faster availability (per-room sorted interval list + bisect, as in the meeting scheduler; or an interval tree) · overbooking policy · room upgrades (strategy chooses a higher type if none free) · concurrency (lock around search+book, or DB exclusion constraint on date ranges).

#### Why the system picks the room

Guests book a *room type*, not room 204. Letting the hotel assign the actual room at booking time (or even at check-in) keeps flexibility: if 204 needs repairs, any free double will do. In code it means `book()` takes a type and a date range, and a small allocation step chooses a room — the place to plug in rules like "prefer rooms on the same floor for a group" later without touching anything else.

The thing that breaks first under load is the availability check: scanning every reservation for every room is O(rooms × reservations). Per-room sorted lists with binary search (as in the meeting scheduler) make it O(rooms × log n), which is plenty for a single hotel.

---

## 16. Problem: Car / bike rental

**How often asked:** occasional · **Builds on:** [3. OOP in depth](#3-oop-in-depth), [4. Class relationships and UML](#4-class-relationships-and-uml), [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)

**Clarify:** branches with vehicles of types (sedan, SUV, bike) and hourly prices; book a vehicle type at a branch for `[start, end)`; system picks the cheapest free vehicle; show availability; dynamic pricing (+10% when ≥ 80% of that type at the branch is booked for the slot).

```python
from dataclasses import dataclass, field
from itertools import count

class RentalError(Exception): ...

@dataclass(frozen=True)
class Vehicle:
    id: str
    type: str
    price_per_hour: int

@dataclass
class Branch:
    name: str
    vehicles: dict[str, Vehicle] = field(default_factory=dict)

@dataclass(frozen=True)
class RentalBooking:
    id: int
    branch: str
    vehicle_id: str
    start: int
    end: int
    amount: int

def surge_when_busy(threshold: float = 0.8, pct: int = 10):
    def price(base: int, hours: int, booked_ratio: float) -> int:
        amount = base * hours
        return amount * (100 + pct) // 100 if booked_ratio >= threshold else amount
    return price

class RentalService:
    def __init__(self, pricing=surge_when_busy()):
        self.branches: dict[str, Branch] = {}
        self.bookings: dict[int, RentalBooking] = {}
        self._pricing, self._ids = pricing, count(1)

    def add_branch(self, name: str) -> None:
        self.branches[name] = Branch(name)

    def add_vehicle(self, branch: str, v: Vehicle) -> None:
        self.branches[branch].vehicles[v.id] = v

    def _busy(self, vehicle_id: str, start: int, end: int) -> bool:
        return any(b.vehicle_id == vehicle_id and b.start < end and start < b.end for b in self.bookings.values())

    def available(self, branch: str, vtype: str, start: int, end: int) -> list[Vehicle]:
        if start >= end:
            raise RentalError("invalid slot")
        vs = [v for v in self.branches[branch].vehicles.values() if v.type == vtype]
        return sorted((v for v in vs if not self._busy(v.id, start, end)), key=lambda v: (v.price_per_hour, v.id))

    def book(self, branch: str, vtype: str, start: int, end: int) -> RentalBooking:
        total = [v for v in self.branches[branch].vehicles.values() if v.type == vtype]
        free = self.available(branch, vtype, start, end)
        if not free:
            raise RentalError("no vehicle available")
        booked_ratio = (len(total) - len(free)) / len(total)
        # cheapest-first selection strategy
        v = free[0]
        booking = RentalBooking(next(self._ids), branch, v.id, start, end,
                                self._pricing(v.price_per_hour, end - start, booked_ratio))
        self.bookings[booking.id] = booking
        return booking

    def cancel(self, booking_id: int) -> None:
        if self.bookings.pop(booking_id, None) is None:
            raise RentalError("unknown booking")

svc = RentalService()
svc.add_branch("koramangala")
for i, price in enumerate([100, 100, 100, 100, 120]):
    svc.add_vehicle("koramangala", Vehicle(f"s{i}", "sedan", price))
bookings = [svc.book("koramangala", "sedan", 10, 12) for _ in range(4)]
assert [b.amount for b in bookings] == [200, 200, 200, 200]
last = svc.book("koramangala", "sedan", 10, 12)     # 4/5 booked → surge
assert last.vehicle_id == "s4" and last.amount == 264
try:
    svc.book("koramangala", "sedan", 11, 13); assert False
except RentalError:
    pass
assert [v.id for v in svc.available("koramangala", "sedan", 12, 14)] == ["s0", "s1", "s2", "s3", "s4"]
svc.cancel(bookings[0].id)
assert svc.book("koramangala", "sedan", 11, 13).vehicle_id == "s0"
```

**Follow-ups:** pick the nearest branch if none free (strategy over branches) · late return fees · vehicle maintenance blocking (a "booking" of type MAINTENANCE) · indexes per vehicle (sorted intervals + bisect) for speed.

This is the hotel problem with different nouns: a vehicle is a room, a rental is a stay, and both reduce to "find a resource of this type with no overlapping interval". If you've done one, reuse the structure and spend the time on what's different — **branches** (availability is per branch, and a cross-branch fallback is a strategy over branches), **one-way rentals** (the vehicle ends up at another branch, so its location changes after the rental), and **maintenance** (model it as a blocking interval, not a special status, so the overlap check handles it for free).

---

## 17. Problem: Parking lot

**How often asked:** very common · **Builds on:** [5. SOLID with code](#5-solid-with-code), [8. Design patterns](#8-design-patterns)

**Clarify:** floors and spot sizes? vehicle types? pricing rule (per hour, per type)? multiple gates at once? payment? display board?
Assume: spots SMALL/MEDIUM/LARGE on several floors; bike→small, car→medium, truck→large; a vehicle may use a bigger spot if its size is full; hourly price per vehicle type (started hours, min 1); multiple gates (thread-safe).

**Entities**
- `Spot(id, floor, size)` — immutable metadata.
- `Ticket(id, plate, vehicle_type, spot_id, entered_at)` — immutable record.
- `ParkingLot` — **owns** free spots, active tickets, plate → ticket index.
- Pricing strategy — callable `(vehicle_type, seconds) → amount`.

**Invariants**
- A spot is either free (in a free heap) or referenced by exactly one active ticket.
- One active ticket per plate.

**Design decisions**
- Free spots kept in a **min-heap per size** keyed by `(floor, id)` → O(log n) allocation of the lowest-floor spot, no scanning.
- Try the vehicle's own size first, then larger sizes.
- Exit computes the fee **before** releasing anything (a pricing error leaves state intact).
- One lock around park/exit — two gates can't take the same spot.

```python
import heapq
from dataclasses import dataclass
from enum import IntEnum
from itertools import count
from math import ceil
from threading import Lock
from time import monotonic

class Size(IntEnum):
    SMALL = 1
    MEDIUM = 2
    LARGE = 3

VEHICLE_SIZE = {"bike": Size.SMALL, "car": Size.MEDIUM, "truck": Size.LARGE}

@dataclass(frozen=True)
class Spot:
    id: str
    floor: int
    size: Size

@dataclass(frozen=True)
class Ticket:
    id: int
    plate: str
    vehicle_type: str
    spot_id: str
    entered_at: float

class ParkingError(Exception): ...

def hourly_pricing(rate_per_hour: dict[str, int]):
    def price(vehicle_type: str, seconds: float) -> int:
        hours = max(1, ceil(seconds / 3600))
        return hours * rate_per_hour[vehicle_type]
    return price

class ParkingLot:
    def __init__(self, spots: list[Spot], pricing, clock=monotonic):
        self._spots = {s.id: s for s in spots}
        if len(self._spots) != len(spots):
            raise ValueError("duplicate spot id")
        self._free: dict[Size, list] = {size: [] for size in Size}
        for s in spots:
            heapq.heappush(self._free[s.size], (s.floor, s.id))
        self._tickets: dict[int, Ticket] = {}
        self._ticket_by_plate: dict[str, int] = {}
        self._ids = count(1)
        self._pricing, self._clock = pricing, clock
        self._lock = Lock()

    def park(self, plate: str, vehicle_type: str) -> Ticket:
        if vehicle_type not in VEHICLE_SIZE:
            raise ParkingError(f"unknown vehicle type {vehicle_type}")
        if not plate.strip():
            raise ParkingError("plate required")
        need = VEHICLE_SIZE[vehicle_type]
        with self._lock:
            if plate in self._ticket_by_plate:
                raise ParkingError("vehicle already parked")
            for size in Size:                          # smallest fitting size first
                if size >= need and self._free[size]:
                    _, spot_id = heapq.heappop(self._free[size])
                    break
            else:
                raise ParkingError("no spot available")
            ticket = Ticket(next(self._ids), plate, vehicle_type, spot_id, self._clock())
            self._tickets[ticket.id] = ticket
            self._ticket_by_plate[plate] = ticket.id
            return ticket

    def exit(self, ticket_id: int) -> int:
        with self._lock:
            ticket = self._tickets.get(ticket_id)
            if ticket is None:
                raise ParkingError("invalid or already used ticket")
            fee = self._pricing(ticket.vehicle_type, self._clock() - ticket.entered_at)
            # commit only after fee succeeded
            del self._tickets[ticket_id]
            del self._ticket_by_plate[ticket.plate]
            spot = self._spots[ticket.spot_id]
            heapq.heappush(self._free[spot.size], (spot.floor, spot.id))
            return fee

    def free_spots(self, vehicle_type: str) -> int:
        need = VEHICLE_SIZE[vehicle_type]
        with self._lock:
            return sum(len(self._free[s]) for s in Size if s >= need)

now = [0.0]
lot = ParkingLot(
    [Spot("S1", 0, Size.SMALL), Spot("M1", 0, Size.MEDIUM), Spot("M2", 1, Size.MEDIUM), Spot("L1", 0, Size.LARGE)],
    hourly_pricing({"bike": 20, "car": 40, "truck": 100}),
    clock=lambda: now[0],
)
bike = lot.park("KA01", "bike")
assert bike.spot_id == "S1"
assert lot.park("KA02", "bike").spot_id == "M1"          # small full → medium
assert lot.park("KA03", "car").spot_id == "M2"
assert lot.park("KA04", "truck").spot_id == "L1"
try:
    lot.park("KA05", "car"); assert False
except ParkingError:
    pass
try:
    lot.park("KA01", "bike"); assert False               # already inside
except ParkingError:
    pass
now[0] = 3601
assert lot.exit(bike.id) == 40                           # 2 started hours × 20
assert lot.free_spots("bike") == 1
try:
    lot.exit(bike.id); assert False                      # ticket reused
except ParkingError:
    pass
```

**Complexity:** park/exit O(log S); free count O(sizes).

**Follow-ups**
- *Display board per floor?* Observer: the lot publishes `spot_taken/spot_freed(floor, size)`; boards keep counters.
- *Reserved/EV spots?* Add a spot attribute + an allocation strategy that filters; `park()` asks the strategy instead of the heap directly.
- *Payment before exit?* `exit()` returns the fee and moves the ticket to `PAYMENT_PENDING`; a `pay(ticket_id)` call releases the spot. Keep fee calculation separate from payment.
- *Lost ticket?* Look up by plate; charge a max daily fee.
- *Multiple lots / many servers?* Spot state moves to a DB; allocation = `UPDATE spots SET occupied = true WHERE id = (SELECT id ... FOR UPDATE SKIP LOCKED LIMIT 1)`.

#### What usually happens in round two

Parking lot is so common that interviewers rarely stop at the basic version. Expect one of these extensions, and notice that a good first design absorbs each with a small change:
- *"Add EV spots that charge per kWh"* → a new spot attribute and a pricing strategy; `park()` doesn't change.
- *"Monthly pass holders park free"* → a pricing decorator that checks the pass before delegating to the normal tariff.
- *"Two entrances, cars arriving at the same moment"* → the lock around allocation already handles it; say why a lock per spot size would also work and when you'd bother.
- *"Show free spots per floor on a display"* → Observer on spot changes.

If one of these forces you to rewrite `park()`, the first design was too rigid — that's exactly what the extension is testing.

---

## 18. Problem: Splitwise (expense sharing)

**How often asked:** very common · **Builds on:** [5. SOLID with code](#5-solid-with-code), [8. Design patterns](#8-design-patterns)

**Clarify:** users; expenses paid by one user, split among participants: EQUAL, EXACT, PERCENT; show balances; settle up; simplify debts.

**Entities**
- `SplitStrategy` → `EqualSplit`, `ExactSplit`, `PercentSplit` — each validates input and returns `{user: share}` summing exactly to the amount.
- `Expense(id, paid_by, amount, shares, description)` — append-only log.
- `ExpenseManager` — owns users, expenses and **pairwise net debts** `owes[debtor][creditor]`.

**Invariants**
- Shares sum exactly to the expense amount (integer paise; remainder distributed).
- Between two users only one direction of debt is stored (netted).
- Sum of everyone's net balance is 0.

```python
import heapq
from abc import ABC, abstractmethod
from collections import defaultdict
from dataclasses import dataclass
from itertools import count

class SplitError(Exception): ...

def _spread(total: int, weights: list[int]) -> list[int]:
    """Split total proportionally to weights; leftover paise go to the first users."""
    wsum = sum(weights)
    parts = [total * w // wsum for w in weights]
    for i in range(total - sum(parts)):
        parts[i] += 1
    return parts

class SplitStrategy(ABC):
    @abstractmethod
    def shares(self, amount: int, users: list[str], values: dict | None) -> dict[str, int]: ...

class EqualSplit(SplitStrategy):
    def shares(self, amount, users, values=None):
        return dict(zip(users, _spread(amount, [1] * len(users))))

class ExactSplit(SplitStrategy):
    def shares(self, amount, users, values):
        if set(values) != set(users) or sum(values.values()) != amount:
            raise SplitError("exact amounts must cover all users and sum to the total")
        if any(v < 0 for v in values.values()):
            raise SplitError("negative share")
        return dict(values)

class PercentSplit(SplitStrategy):
    def shares(self, amount, users, values):
        if set(values) != set(users) or sum(values.values()) != 100:
            raise SplitError("percentages must cover all users and sum to 100")
        return dict(zip(users, _spread(amount, [values[u] for u in users])))

@dataclass(frozen=True)
class Expense:
    id: int
    paid_by: str
    amount: int
    shares: dict
    description: str

class ExpenseManager:
    def __init__(self):
        self._users: set[str] = set()
        self._expenses: list[Expense] = []
        # debtor -> creditor -> amount
        self._owes = defaultdict(lambda: defaultdict(int))
        self._ids = count(1)

    def add_user(self, user: str) -> None:
        self._users.add(user)

    def add_expense(self, paid_by: str, amount: int, users: list[str],
                    strategy: SplitStrategy, values: dict | None = None, description: str = "") -> Expense:
        if amount <= 0:
            raise SplitError("amount must be positive")
        unknown = ({paid_by} | set(users)) - self._users
        if unknown:
            raise SplitError(f"unknown users {sorted(unknown)}")
        if len(set(users)) != len(users) or not users:
            raise SplitError("participants must be unique and non-empty")
        shares = strategy.shares(amount, users, values)        # validates; may raise
        expense = Expense(next(self._ids), paid_by, amount, shares, description)
        for user, share in shares.items():
            if user != paid_by and share:
                self._add_debt(user, paid_by, share)
        self._expenses.append(expense)
        return expense

    def settle(self, debtor: str, creditor: str, amount: int) -> None:
        if amount <= 0 or amount > self._owes[debtor][creditor]:
            raise SplitError("invalid settlement amount")
        self._add_debt(creditor, debtor, amount)               # payment reverses debt

    def _add_debt(self, debtor: str, creditor: str, amount: int) -> None:
        reverse = self._owes[creditor][debtor]
        if reverse >= amount:
            self._owes[creditor][debtor] = reverse - amount
        else:
            self._owes[creditor][debtor] = 0
            self._owes[debtor][creditor] += amount - reverse
        for a, b in ((debtor, creditor), (creditor, debtor)):
            if self._owes[a][b] == 0:
                del self._owes[a][b]

    def balances(self) -> dict[tuple[str, str], int]:
        return {(d, c): amt for d, row in self._owes.items() for c, amt in row.items()}

    def net(self, user: str) -> int:
        """Positive: others owe this user. Negative: this user owes."""
        owed = sum(row.get(user, 0) for row in self._owes.values())
        owes = sum(self._owes[user].values()) if user in self._owes else 0
        return owed - owes

    def simplify(self) -> list[tuple[str, str, int]]:
        """Greedy: biggest debtor pays biggest creditor. At most n-1 payments."""
        nets = {u: self.net(u) for u in self._users}
        creditors = [(-v, u) for u, v in nets.items() if v > 0]
        debtors = [(v, u) for u, v in nets.items() if v < 0]
        heapq.heapify(creditors); heapq.heapify(debtors)
        plan = []
        while creditors and debtors:
            c_amt, c = heapq.heappop(creditors)
            d_amt, d = heapq.heappop(debtors)
            pay = min(-c_amt, -d_amt)
            plan.append((d, c, pay))
            if -c_amt > pay:
                heapq.heappush(creditors, (c_amt + pay, c))
            if -d_amt > pay:
                heapq.heappush(debtors, (d_amt + pay, d))
        return plan

m = ExpenseManager()
for u in "ABCD":
    m.add_user(u)
m.add_expense("A", 300, ["A", "B", "C"], EqualSplit(), description="dinner")
m.add_expense("B", 150, ["A", "B", "C"], EqualSplit(), description="cab")
assert m.balances() == {("B", "A"): 50, ("C", "A"): 100, ("C", "B"): 50}
assert (m.net("A"), m.net("B"), m.net("C")) == (150, 0, -150)
assert m.simplify() == [("C", "A", 150)]
m.add_expense("D", 1001, ["D", "A"], PercentSplit(), {"D": 50, "A": 50})
# D's share got the extra paisa (501)
assert m.balances()[("A", "D")] == 500
try:
    m.add_expense("A", 100, ["A", "B"], ExactSplit(), {"A": 30, "B": 30}); assert False
except SplitError:
    pass
m.settle("C", "A", 100)
assert ("C", "A") not in m.balances()
assert sum(m.net(u) for u in "ABCD") == 0
```

**Follow-ups**
- *New split type (shares/ratios)?* New strategy (`ShareSplit` using `_spread` with weights) — `add_expense` unchanged.
- *Groups?* `Group(id, members)`; expenses tagged with a group; balances computed per group or globally.
- *Edit/delete an expense?* Apply the reverse shares (negate), then the new one; keep the original in the log (audit).
- *Is greedy simplification optimal?* It gives ≤ n−1 transactions; the true minimum is NP-hard (subset-sum), greedy is the accepted answer.

#### Why keep net balances, and not just the expense log?

You could recompute everyone's balances from the expense log on every request, and for a small group that's fine. Keeping `owes[debtor][creditor]` updated as each expense arrives makes "how much do I owe?" O(1), and it's what lets "simplify debts" work on a small summary instead of the whole history. The log is still kept — it's the audit trail, and it's how you rebuild balances if a bug corrupts them.

The bug interviewers most often catch here is money in floats: ₹100 split three ways must be 3334 + 3333 + 3333 paise, with the leftover paise assigned deterministically, so the shares always add up to exactly the amount.

---

## 19. Problem: Elevator system

**How often asked:** very common · **Builds on:** [5. SOLID with code](#5-solid-with-code), [8. Design patterns](#8-design-patterns)

**Clarify:** N elevators, F floors; hall calls (up/down buttons on a floor) and car calls (buttons inside); goal: low wait time; capacity/emergency out of scope initially.

**Entities**
- `Elevator(id, floor, direction, stops)` — moves itself with **LOOK**: keep going in the current direction while there are stops ahead, then reverse.
- `Controller` — receives hall calls, **dispatches** to an elevator via a strategy, advances time with `step()`.
- `DispatchStrategy` — scores elevators for a hall call.

**Key decision:** separate **dispatch** (which elevator) from **movement** (in what order an elevator serves its stops). They change for different reasons. `step()` advances one floor per tick → deterministic, testable without threads.

```python
from enum import Enum

class Direction(Enum):
    UP = 1
    DOWN = -1
    IDLE = 0

class Elevator:
    def __init__(self, eid: int, floor: int = 0):
        self.id, self.floor, self.direction = eid, floor, Direction.IDLE
        self.stops: set[int] = set()
        self.served: list[int] = []                 # log of floors where doors opened

    def add_stop(self, floor: int) -> None:
        if floor == self.floor and self.direction is Direction.IDLE:
            self.served.append(floor)               # already here: open doors
        else:
            self.stops.add(floor)

    def step(self) -> None:
        if not self.stops:
            self.direction = Direction.IDLE
            return
        if self.direction is Direction.IDLE:        # head toward the nearest stop
            nearest = min(self.stops, key=lambda f: (abs(f - self.floor), f))
            self.direction = Direction.UP if nearest > self.floor else Direction.DOWN
        ahead = [f for f in self.stops if (f - self.floor) * self.direction.value > 0]
        if not ahead:                               # LOOK: reverse when nothing ahead
            self.direction = Direction.DOWN if self.direction is Direction.UP else Direction.UP
        self.floor += self.direction.value
        if self.floor in self.stops:
            self.stops.remove(self.floor)
            self.served.append(self.floor)
            if not self.stops:
                self.direction = Direction.IDLE

def nearest_suitable(elevators: list[Elevator], floor: int, direction: Direction, floors: int) -> Elevator:
    def cost(e: Elevator) -> tuple:
        distance = abs(e.floor - floor)
        if e.direction is Direction.IDLE:
            return (distance, e.id)
        moving_toward = (floor - e.floor) * e.direction.value >= 0
        if moving_toward and e.direction is direction:
            return (distance, e.id)                 # can pick up on the way
        # It must finish its current run first: in the worst case it goes to the
        # far end and comes back, so add a penalty of up to two full building heights.
        return (distance + 2 * floors, e.id)
    return min(elevators, key=cost)

class Controller:
    def __init__(self, n: int, floors: int, strategy=nearest_suitable):
        self.floors = floors
        self.elevators = [Elevator(i) for i in range(n)]
        self.strategy = strategy

    def _check(self, floor):
        if not 0 <= floor < self.floors:
            raise ValueError("invalid floor")

    def hall_call(self, floor: int, direction: Direction) -> int:
        self._check(floor)
        e = self.strategy(self.elevators, floor, direction, self.floors)
        e.add_stop(floor)
        return e.id

    def car_call(self, elevator_id: int, floor: int) -> None:
        self._check(floor)
        self.elevators[elevator_id].add_stop(floor)

    def step(self, ticks: int = 1) -> None:
        for _ in range(ticks):
            for e in self.elevators:
                e.step()

ctl = Controller(n=2, floors=20)
ctl.elevators[1].floor = 10
assert ctl.hall_call(8, Direction.DOWN) == 1          # elevator 1 is closer
assert ctl.hall_call(2, Direction.UP) == 0
ctl.step(2)
assert ctl.elevators[0].served == [2] and ctl.elevators[1].served == [8]
ctl.car_call(0, 7); ctl.car_call(0, 0)               # inside elevator 0 at floor 2
ctl.step(20)
# nearest first (0 is 2 away, 7 is 5 away)
assert ctl.elevators[0].served == [2, 0, 7]

e = Elevator(9, floor=5)
for f in (7, 2, 9):
    e.add_stop(f)
for _ in range(15):
    e.step()
# LOOK: finish going up, then come down
assert e.served == [7, 9, 2]
```

**Follow-ups**
- *Capacity/weight?* Elevator tracks load; dispatch skips full elevators.
- *Peak hours?* Swap strategies: zoning (elevators 0–1 serve floors 0–10), or send idle elevators to the lobby in the morning.
- *Emergency / maintenance?* State per elevator (`MAINTENANCE` excluded from dispatch; `EMERGENCY` clears stops and goes to ground) — State pattern if behavior diverges a lot.
- *Real system threads?* One thread per elevator calling `step()` on a timer + a lock around `stops`; or an event loop. Keep the logic testable as above.

#### How real buildings do it differently

Modern high-rise elevators often use **destination dispatch**: instead of pressing up/down, you enter your destination floor on a keypad in the lobby, and the system tells you which car to take. Because the controller knows destinations before passengers board, it can group people going to nearby floors into the same car, cutting stops per trip. If an interviewer asks how you'd improve throughput at peak hours, this is a great answer — and it maps neatly onto the design here: it's just a different `DispatchStrategy` that receives (origin, destination) instead of (floor, direction).

---

## 20. Problem: Rate limiter

**How often asked:** very common · **Builds on:** [5. SOLID with code](#5-solid-with-code), [8. Design patterns](#8-design-patterns)

**Clarify:** limit per user/API key/IP? which algorithm, or pluggable? single process or many servers? what to return when limited?

**Entities**
- `RateLimitStrategy` (interface) → `TokenBucket`, `SlidingWindowLog`, `FixedWindowCounter`
- `RateLimiter` — keeps one strategy instance per client key, created by a factory (per-plan limits)
- Clock injected

**Algorithms** (tradeoffs in the [HLD guide](hld-interview-guide.md#476-rate-limiting))
- Token bucket: refill `elapsed × rate` tokens (capped), take one per request. Allows bursts up to capacity.
- Sliding window log: deque of timestamps; drop those older than the window; allow if fewer than the limit.
- Fixed window: counter per window index `int(now // window)`; simplest; allows 2× bursts at edges.

```python
from abc import ABC, abstractmethod
from collections import deque
from threading import Lock
from time import monotonic

class RateLimitStrategy(ABC):
    @abstractmethod
    def allow(self, now: float) -> bool: ...

class TokenBucket(RateLimitStrategy):
    def __init__(self, capacity: int, refill_per_sec: float):
        if capacity <= 0 or refill_per_sec <= 0:
            raise ValueError("capacity and rate must be positive")
        self._cap, self._rate = capacity, refill_per_sec
        self._tokens, self._last = float(capacity), None

    def allow(self, now):
        if self._last is not None:
            self._tokens = min(self._cap, self._tokens + (now - self._last) * self._rate)
        self._last = now
        if self._tokens >= 1:
            self._tokens -= 1
            return True
        return False

class SlidingWindowLog(RateLimitStrategy):
    def __init__(self, limit: int, window_sec: float):
        self._limit, self._window = limit, window_sec
        self._hits = deque()

    def allow(self, now):
        while self._hits and self._hits[0] <= now - self._window:
            self._hits.popleft()
        if len(self._hits) < self._limit:
            self._hits.append(now)
            return True
        return False

class FixedWindowCounter(RateLimitStrategy):
    def __init__(self, limit: int, window_sec: float):
        self._limit, self._window = limit, window_sec
        self._window_id, self._count = None, 0

    def allow(self, now):
        window_id = int(now // self._window)
        if window_id != self._window_id:
            self._window_id, self._count = window_id, 0
        if self._count < self._limit:
            self._count += 1
            return True
        return False

class RateLimiter:
    """One strategy instance per client; the factory decides the rule (e.g. per plan)."""

    def __init__(self, strategy_factory, clock=monotonic):
        self._factory, self._clock = strategy_factory, clock
        self._limiters: dict[str, RateLimitStrategy] = {}
        self._lock = Lock()   # one lock: simple and correct; see follow-ups for scaling

    def allow(self, client_id: str) -> bool:
        with self._lock:
            limiter = self._limiters.get(client_id)
            if limiter is None:
                limiter = self._limiters[client_id] = self._factory(client_id)
            return limiter.allow(self._clock())

now = [0.0]
plans = {"free": lambda: TokenBucket(2, 1), "pro": lambda: SlidingWindowLog(3, 10)}
user_plan = {"alice": "free", "bob": "pro"}
rl = RateLimiter(lambda cid: plans[user_plan[cid]](), clock=lambda: now[0])

assert [rl.allow("alice") for _ in range(3)] == [True, True, False]   # burst of 2
now[0] = 1.0
assert rl.allow("alice") and not rl.allow("alice")                   # 1 token refilled
assert [rl.allow("bob") for _ in range(4)] == [True, True, True, False]
now[0] = 11.0
assert rl.allow("bob")                                               # old hits slid out

fw = FixedWindowCounter(2, 60)
assert [fw.allow(t) for t in (0, 1, 2, 60)] == [True, True, False, True]
```

**Follow-ups**
- *One lock for all clients is a bottleneck?* Lock per client (strategy holds its own lock) or lock striping (N locks, `hash(client) % N`). The dict creation needs its own small lock or `setdefault`.
- *Memory grows with clients?* Evict idle clients (LRU of limiters, or a TTL sweep).
- *Many servers?* Move state to Redis with a Lua script ([HLD chapter 48: Rate limiter (distributed)](hld-interview-guide.md#48-problem-rate-limiter-distributed)).
- *Add a new algorithm?* New `RateLimitStrategy` subclass; nothing else changes.

#### In libraries you'll actually use

Guava's `RateLimiter` (Java) implements a smoothed token bucket — it spaces permits evenly rather than allowing the whole burst at once, with an optional warm-up period for services that need time to get "hot". It's a useful reference when an interviewer asks how token bucket behaves at the edges. For limits shared across many servers, the in-process version here becomes the Redis-based design in [the HLD rate limiter chapter](hld-interview-guide.md#48-problem-rate-limiter-distributed).

---

## 21. Problem: Shopping cart + coupons

**How often asked:** common · **Builds on:** [5. SOLID with code](#5-solid-with-code), [8. Design patterns](#8-design-patterns)

**Entities:** `Product(sku, price, category)` · `Inventory(sku → qty)` · `Cart(user, lines)` · `Coupon` (Strategy) · `CheckoutService`.

**Coupon types (each a strategy with `is_applicable(cart)` + `discount(cart)`):** percent off with a max cap · flat off above a minimum cart value · buy X get Y free on a SKU · category-specific percent.

**Rules:** one coupon per order (or stackable rules — clarify); discount never exceeds the subtotal; checkout reserves stock **all-or-nothing**.

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass, field

@dataclass(frozen=True)
class Product:
    sku: str
    price: int
    category: str

@dataclass
class Cart:
    lines: dict[str, int] = field(default_factory=dict)       # sku -> qty

class Coupon(ABC):
    @abstractmethod
    def discount(self, cart: Cart, catalog: dict[str, Product]) -> int:
        """0 if not applicable."""

def subtotal(cart, catalog):
    return sum(catalog[s].price * q for s, q in cart.lines.items())

class PercentOff(Coupon):
    def __init__(self, pct: int, cap: int):
        self.pct, self.cap = pct, cap
    def discount(self, cart, catalog):
        return min(self.cap, subtotal(cart, catalog) * self.pct // 100)

class FlatOffAbove(Coupon):
    def __init__(self, off: int, minimum: int):
        self.off, self.minimum = off, minimum
    def discount(self, cart, catalog):
        return self.off if subtotal(cart, catalog) >= self.minimum else 0

class BuyXGetY(Coupon):
    def __init__(self, sku: str, x: int, y: int):
        self.sku, self.x, self.y = sku, x, y
    def discount(self, cart, catalog):
        qty = cart.lines.get(self.sku, 0)
        free = (qty // (self.x + self.y)) * self.y
        return free * catalog[self.sku].price if self.sku in catalog else 0

class CategoryPercent(Coupon):
    def __init__(self, category: str, pct: int):
        self.category, self.pct = category, pct
    def discount(self, cart, catalog):
        eligible = sum(catalog[s].price * q for s, q in cart.lines.items()
                       if catalog[s].category == self.category)
        return eligible * self.pct // 100

class OutOfStock(Exception): ...

class CheckoutService:
    def __init__(self, catalog: dict[str, Product], stock: dict[str, int], coupons: dict[str, Coupon]):
        self.catalog, self.stock, self.coupons = catalog, stock, coupons

    def checkout(self, cart: Cart, code: str | None = None) -> int:
        short = [s for s, q in cart.lines.items() if self.stock.get(s, 0) < q]
        if short:
            raise OutOfStock(short)                     # validate everything first
        total = subtotal(cart, self.catalog)
        if code is not None:
            coupon = self.coupons.get(code)
            if coupon is None:
                raise ValueError("invalid coupon")
            total -= min(total, coupon.discount(cart, self.catalog))
        for s, q in cart.lines.items():                 # then mutate
            self.stock[s] -= q
        return total

catalog = {"tee": Product("tee", 50000, "apparel"), "mug": Product("mug", 20000, "home")}
svc = CheckoutService(catalog, {"tee": 5, "mug": 1}, {
    "SAVE10": PercentOff(10, cap=5000),
    "FLAT100": FlatOffAbove(10000, minimum=100000),
    "TEE2+1": BuyXGetY("tee", 2, 1),
    "HOME20": CategoryPercent("home", 20),
})
cart = Cart({"tee": 3, "mug": 1})                       # subtotal 170000
assert svc.checkout(Cart(dict(cart.lines)), "SAVE10") == 165000   # 10% capped at 5000
# below minimum -> no discount
assert svc.checkout(Cart({"tee": 1}), "FLAT100") == 50000
try:
    svc.checkout(cart, "TEE2+1"); assert False                    # mug already sold out
except OutOfStock:
    pass
assert svc.checkout(Cart({"tee": 1}), "HOME20") == 50000          # no home items
```

**Follow-ups:** coupon usage limits per user (a redemption table + check) · expiry dates (a `valid_until` check in a base class or decorator) · "best coupon automatically" (evaluate all, pick max) · stock races (a lock around check+decrement, or `UPDATE ... WHERE qty >= n` in a DB).

#### Getting the money right

Order of operations is where cart designs go wrong. Compute the subtotal from current prices (not prices saved when the item was added), apply the coupon, clamp the discount to the subtotal, **then** reserve stock — and if any line can't be reserved, release what was reserved and change nothing. Recording the price and discount on the order at checkout matters later: refunds must use what the customer actually paid, not today's price.

If stacking is allowed, the order coupons apply in changes the result (10% off then ₹100 off ≠ ₹100 off then 10% off), so define it explicitly — usually percentage discounts first, flat discounts after, each capped.

---

## 22. Problem: Vending machine

**How often asked:** very common · **Builds on:** [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem), [8. Design patterns](#8-design-patterns)

**Clarify:** products in slots with price and stock; accepts coins of fixed denominations; returns change with the coins it has; cancel refunds; out-of-stock and cannot-make-change cases; admin restock.

**Design:** **State pattern** — `Idle` and `HasMoney` (the "dispensing" step is atomic inside `select`). Each state object decides what `insert`, `select`, `cancel` mean.

**Key decisions**
- On `select`: validate product, stock, enough money, **and that change can be made** → only then deduct stock, coins, and return. Failure leaves everything unchanged.
- Change-making with **limited** coins: greedy can fail (change 6 with coins {5×1, 2×3, 1×0}: greedy takes 5 then fails; 2+2+2 works). Use a small bounded-coin DP that minimizes coin count.
- Inserted coins join the machine's coin pool before computing change (they can be returned as change).

```python
from abc import ABC
from collections import Counter
from dataclasses import dataclass

class VendingError(Exception): ...

@dataclass
class Slot:
    product: str
    price: int
    stock: int

def make_change(amount: int, coins: Counter) -> Counter | None:
    """Fewest coins summing to amount using limited coin counts; None if impossible."""
    best: dict[int, Counter] = {0: Counter()}
    for coin, n in sorted(coins.items()):
        # each physical coin used at most once
        for _ in range(n):
            nxt = dict(best)
            for total, combo in best.items():
                t = total + coin
                if t <= amount:
                    cand = combo + Counter({coin: 1})
                    if t not in nxt or sum(cand.values()) < sum(nxt[t].values()):
                        nxt[t] = cand
            best = nxt
    return best.get(amount)

class State(ABC):
    def insert(self, vm, coin): raise VendingError("invalid action")
    def select(self, vm, code): raise VendingError("insert money first")
    def cancel(self, vm): raise VendingError("nothing to cancel")

class Idle(State):
    def insert(self, vm, coin):
        vm._accept(coin)
        vm.state = HasMoney()

class HasMoney(State):
    def insert(self, vm, coin):
        vm._accept(coin)

    def select(self, vm, code):
        slot = vm.slots.get(code)
        if slot is None:
            raise VendingError("unknown product")
        if slot.stock == 0:
            raise VendingError("out of stock")
        paid = sum(c * n for c, n in vm.inserted.items())
        if paid < slot.price:
            raise VendingError(f"insert {slot.price - paid} more")
        pool = vm.coins + vm.inserted
        change = make_change(paid - slot.price, pool)
        if change is None:
            raise VendingError("cannot make change; cancel or use exact amount")
        # commit only after every check passed
        slot.stock -= 1
        vm.coins = pool - change
        vm.inserted = Counter()
        vm.state = Idle()
        return slot.product, change

    def cancel(self, vm):
        refund, vm.inserted = vm.inserted, Counter()
        vm.state = Idle()
        return refund

class VendingMachine:
    ACCEPTED = {1, 2, 5, 10, 20}

    def __init__(self, slots: dict[str, Slot], coins: dict[int, int]):
        self.slots, self.coins = slots, Counter(coins)
        self.inserted, self.state = Counter(), Idle()

    def _accept(self, coin):
        if coin not in self.ACCEPTED:
            raise VendingError("coin rejected")
        self.inserted[coin] += 1

    def insert(self, coin: int): self.state.insert(self, coin)
    def select(self, code: str): return self.state.select(self, code)
    def cancel(self): return self.state.cancel(self)

    def restock(self, code: str, qty: int):
        if not isinstance(self.state, Idle):
            raise VendingError("transaction in progress")
        self.slots[code].stock += qty

vm = VendingMachine({"A1": Slot("chips", 14, 2), "B1": Slot("soda", 25, 0)}, {5: 1, 2: 3})
try:
    vm.select("A1"); assert False                    # no money yet
except VendingError:
    pass
vm.insert(10); vm.insert(10)
product, change = vm.select("A1")
assert product == "chips" and change == Counter({2: 3})   # greedy (5 + ?) would fail
assert vm.slots["A1"].stock == 1 and vm.coins == Counter({10: 2, 5: 1})
vm.insert(20); vm.insert(5)
try:
    vm.select("B1"); assert False                    # out of stock, money kept
except VendingError:
    pass
assert vm.cancel() == Counter({20: 1, 5: 1}) and isinstance(vm.state, Idle)
vm.insert(20)
try:
    # change 6 impossible now (only 10s and 5s)
    vm.select("A1"); assert False
except VendingError as e:
    assert "change" in str(e)
assert vm.slots["A1"].stock == 1                      # unchanged
```

**Follow-ups**
- *Card/UPI payment?* `PaymentMethod` strategy; the cash path keeps change logic, card path skips it.
- *Maintenance mode?* A `Maintenance` state that only accepts `restock`/`collect_cash`.
- *Multiple items in one transaction?* Keep a running balance; `select` repeatedly; change at the end.

#### Why the State pattern here, and not an `if state ==` table?

For the order lifecycle in other problems, an enum plus a transition table is enough: the *allowed transitions* differ by state, but the *behaviour* of each action doesn't. A vending machine is different — what `insert`, `select` and `cancel` **do** changes completely by state (in `Idle`, `select` is an error; in `HasMoney`, it runs the whole dispense logic). Putting that in one class means every method grows an `if state == …` ladder, and adding a `Maintenance` state means editing all of them. State objects keep each state's behaviour together and make new states additive. Saying *why* you chose the pattern is worth more than using it.

---

## 23. Problem: Text editor with undo and redo

**How often asked:** common · **Builds on:** [8.3 Behavioral](#83-behavioral)

"Add undo" is a follow-up in many problems (chess, drawing apps, spreadsheets), and the text editor is the cleanest place to practise it. There are two standard answers, and knowing when to use which is the point.

**Command:** every edit is an object that knows how to **do** and **undo** itself. Two stacks: undo and redo. A new edit clears the redo stack (you can't redo into a different future).

**Memento:** before each edit, save a snapshot of the state; undo restores the previous snapshot. Simpler, but each snapshot is a full copy.

```python
from abc import ABC, abstractmethod

class Editor:
    def __init__(self):
        self.text = ""

class Command(ABC):
    @abstractmethod
    def execute(self, ed: Editor) -> None: ...
    @abstractmethod
    def undo(self, ed: Editor) -> None: ...
    def merge(self, other: "Command") -> bool:
        return False                           # most commands don't combine

class Insert(Command):
    def __init__(self, pos: int, s: str):
        self.pos, self.s = pos, s

    def execute(self, ed):
        ed.text = ed.text[:self.pos] + self.s + ed.text[self.pos:]

    def undo(self, ed):
        ed.text = ed.text[:self.pos] + ed.text[self.pos + len(self.s):]

    def merge(self, other):
        """Typing 'h' then 'i' should undo as one step."""
        if isinstance(other, Insert) and other.pos == self.pos + len(self.s) and " " not in other.s:
            self.s += other.s
            return True
        return False

class Delete(Command):
    def __init__(self, pos: int, n: int):
        self.pos, self.n, self.removed = pos, n, ""

    def execute(self, ed):
        self.removed = ed.text[self.pos:self.pos + self.n]   # remember what to put back
        ed.text = ed.text[:self.pos] + ed.text[self.pos + self.n:]

    def undo(self, ed):
        ed.text = ed.text[:self.pos] + self.removed + ed.text[self.pos:]

class History:
    def __init__(self, ed: Editor):
        self.ed, self.undo_stack, self.redo_stack = ed, [], []

    def run(self, cmd: Command) -> None:
        cmd.execute(self.ed)
        self.redo_stack.clear()
        if not (self.undo_stack and self.undo_stack[-1].merge(cmd)):
            self.undo_stack.append(cmd)

    def undo(self) -> None:
        if self.undo_stack:
            cmd = self.undo_stack.pop()
            cmd.undo(self.ed)
            self.redo_stack.append(cmd)

    def redo(self) -> None:
        if self.redo_stack:
            cmd = self.redo_stack.pop()
            cmd.execute(self.ed)
            self.undo_stack.append(cmd)

ed = Editor()
h = History(ed)
for i, ch in enumerate("hello"):
    h.run(Insert(i, ch))                       # typed one key at a time
h.run(Insert(5, " world"))
# "hello" merged into one step
assert ed.text == "hello world" and len(h.undo_stack) == 2
h.run(Delete(0, 6))
assert ed.text == "world"
h.undo()
assert ed.text == "hello world"
h.undo()
assert ed.text == "hello"
h.redo()
assert ed.text == "hello world"
h.run(Insert(0, ">"))                          # a new edit…
h.redo()                                       # …means nothing left to redo
assert ed.text == ">hello world" and not h.redo_stack

class Snapshots:
    """Memento version: store copies of the state."""
    def __init__(self, ed: Editor):
        self.ed, self.past = ed, []

    def edit(self, change) -> None:
        # the memento: an opaque copy of the state
        self.past.append(self.ed.text)
        change(self.ed)

    def undo(self) -> None:
        if self.past:
            self.ed.text = self.past.pop()

ed2 = Editor()
snap = Snapshots(ed2)
snap.edit(lambda e: setattr(e, "text", "draft"))
snap.edit(lambda e: setattr(e, "text", e.text.upper()))
snap.undo()
assert ed2.text == "draft"
```

**Which one?**
- **Command** stores only the change, so it's cheap for big documents, and it doubles as an operation log (collaboration, autosave, macros). The cost is that every operation must have a correct inverse — easy for insert/delete, hard for "apply a filter to an image".
- **Memento** works for any change without writing inverses, but memory grows with document size × history length. Real apps often combine them: commands for each edit, plus an occasional snapshot so they don't replay thousands of commands.
- In both, the **redo stack is cleared by a new edit** — interviewers check this.

---

## 24. Problem: ATM

**How often asked:** common · **Builds on:** [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem), [8. Design patterns](#8-design-patterns)

**Design:** **State pattern** for the session (`Idle → CardInserted → Authenticated`) and **Chain of Responsibility** (or a simple greedy plan) for the cash dispenser.

**Key decisions**
- Plan the notes **before** debiting the account; then debit; then dispense (commit the plan). If the plan fails, nothing changes.
- With denominations where each is a multiple of the next smaller one (2000, 500, 100), greedy with limited note counts finds a solution whenever one exists. For arbitrary denominations use DP.
- 3 wrong PINs → retain the card.

```python
from abc import ABC

class ATMError(Exception): ...

class Bank:
    def __init__(self):
        self._accounts = {}           # card -> [pin, balance]

    def open(self, card, pin, balance):
        self._accounts[card] = [pin, balance]

    def verify(self, card, pin) -> bool:
        return card in self._accounts and self._accounts[card][0] == pin

    def debit(self, card, amount):
        acct = self._accounts[card]
        if acct[1] < amount:
            raise ATMError("insufficient balance")
        acct[1] -= amount

    def balance(self, card):
        return self._accounts[card][1]

class CashDispenser:
    def __init__(self, notes: dict[int, int]):      # denomination -> count
        self.notes = dict(notes)

    def plan(self, amount: int) -> dict[int, int]:
        plan, left = {}, amount
        for d in sorted(self.notes, reverse=True):
            use = min(left // d, self.notes[d])
            if use:
                plan[d], left = use, left - use * d
        if left:
            raise ATMError("cannot dispense this amount")
        return plan

    def commit(self, plan):
        for d, n in plan.items():
            self.notes[d] -= n

class State(ABC):
    def insert_card(self, atm, card): raise ATMError("invalid action")
    def enter_pin(self, atm, pin): raise ATMError("invalid action")
    def withdraw(self, atm, amount): raise ATMError("invalid action")
    def eject(self, atm): raise ATMError("invalid action")

class Idle(State):
    def insert_card(self, atm, card):
        atm.card, atm.attempts, atm.state = card, 0, CardInserted()

class CardInserted(State):
    def enter_pin(self, atm, pin):
        if atm.bank.verify(atm.card, pin):
            atm.state = Authenticated()
            return
        atm.attempts += 1
        if atm.attempts >= 3:
            atm.retained.append(atm.card)
            atm.card, atm.state = None, Idle()
            raise ATMError("card retained")
        raise ATMError("wrong pin")

    def eject(self, atm):
        atm.card, atm.state = None, Idle()

class Authenticated(State):
    def withdraw(self, atm, amount):
        if amount <= 0:
            raise ATMError("invalid amount")
        plan = atm.dispenser.plan(amount)        # 1. can we dispense?
        # 2. debit (may fail; nothing changed yet)
        atm.bank.debit(atm.card, amount)
        atm.dispenser.commit(plan)               # 3. dispense
        return plan

    def eject(self, atm):
        atm.card, atm.state = None, Idle()

class ATM:
    def __init__(self, bank, dispenser):
        self.bank, self.dispenser = bank, dispenser
        self.state, self.card, self.attempts, self.retained = Idle(), None, 0, []

    def insert_card(self, card): self.state.insert_card(self, card)
    def enter_pin(self, pin): self.state.enter_pin(self, pin)
    def withdraw(self, amount): return self.state.withdraw(self, amount)
    def eject(self): self.state.eject(self)

bank = Bank(); bank.open("c1", "1234", 10000)
atm = ATM(bank, CashDispenser({2000: 2, 500: 4, 100: 10}))
atm.insert_card("c1")
try:
    atm.withdraw(500); assert False        # not authenticated yet
except ATMError:
    pass
atm.enter_pin("1234")
assert atm.withdraw(4700) == {2000: 2, 500: 1, 100: 2}
try:
    atm.withdraw(130); assert False         # not a multiple of 100
except ATMError:
    pass
assert bank.balance("c1") == 5300
atm.eject()
atm.insert_card("c1")
for _ in range(2):
    try:
        atm.enter_pin("0000")
    except ATMError:
        pass
try:
    atm.enter_pin("0000"); assert False
except ATMError as e:
    assert str(e) == "card retained" and isinstance(atm.state, Idle)
```

**Follow-ups:** debit succeeded but the dispenser jammed → reverse the debit (compensation) and log for reconciliation · deposits · mini statement · concurrent use of the same account from two ATMs → the bank's debit must be atomic (DB conditional update).

#### The order of operations is the design

Debit-then-dispense and dispense-then-debit both fail badly: one can take money without giving cash, the other can give cash without taking money. The safe sequence is **plan the notes → debit → dispense**, with a compensating credit if the dispenser fails after the debit. Real ATMs also keep a journal of every step on local storage, so after a power cut the machine and the bank can reconcile exactly what happened — the same idea as a write-ahead log.

State pattern earns its place here because each session state really does behave differently: in `Idle`, entering a PIN is meaningless; in `CardInserted`, a withdrawal isn't allowed yet.

---

## 25. Problem: Food ordering (Zomato / Swiggy)

**How often asked:** very common · **Builds on:** [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem), [8. Design patterns](#8-design-patterns)

**Clarify:** users browse restaurants and menus, add to cart (one restaurant per cart), place order, restaurant accepts, rider assigned, status updates, cancellation rules. Out of scope: payments, search ranking, real maps.

**Entities**
- `MenuItem(id, name, price)` · `Restaurant(id, location, menu, available_items, is_open)`
- `Cart` per user — `restaurant_id` + `{item_id: qty}`
- `Order(id, user, restaurant, items, total, status, rider)` · `OrderStatus` enum with allowed transitions
- `Rider(id, location, busy)` · `RiderAssignmentStrategy` (nearest available)
- `FoodDeliveryService` — orchestrates; publishes status events to listeners (Observer)

**Invariants**
- A cart holds items from exactly one restaurant.
- Order status only moves along allowed transitions.
- A rider has at most one active order.

```python
from dataclasses import dataclass, field
from enum import Enum, auto
from itertools import count
from math import dist

class OrderStatus(Enum):
    PLACED = auto()
    ACCEPTED = auto()
    PREPARING = auto()
    PICKED_UP = auto()
    DELIVERED = auto()
    CANCELLED = auto()

S = OrderStatus
ALLOWED = {
    S.PLACED: {S.ACCEPTED, S.CANCELLED},
    S.ACCEPTED: {S.PREPARING, S.CANCELLED},
    S.PREPARING: {S.PICKED_UP},
    S.PICKED_UP: {S.DELIVERED},
    S.DELIVERED: set(),
    S.CANCELLED: set(),
}

class OrderError(Exception): ...

@dataclass(frozen=True)
class MenuItem:
    id: str
    name: str
    price: int

@dataclass
class Restaurant:
    id: str
    location: tuple[float, float]
    menu: dict[str, MenuItem]
    available: set[str] = field(default_factory=set)
    is_open: bool = True

@dataclass
class Rider:
    id: str
    location: tuple[float, float]
    order_id: int | None = None

@dataclass
class Cart:
    restaurant_id: str
    items: dict[str, int] = field(default_factory=dict)

@dataclass
class Order:
    id: int
    user_id: str
    restaurant_id: str
    items: dict[str, int]
    total: int
    status: OrderStatus = S.PLACED
    rider_id: str | None = None

def nearest_available(riders, location):
    free = [r for r in riders if r.order_id is None]
    return min(free, key=lambda r: dist(r.location, location), default=None)

class FoodDeliveryService:
    def __init__(self, assign_rider=nearest_available):
        self._restaurants: dict[str, Restaurant] = {}
        self._riders: dict[str, Rider] = {}
        self._orders: dict[int, Order] = {}
        self._carts: dict[str, Cart] = {}
        self._assign = assign_rider
        self._ids = count(1)
        self._listeners = []

    def add_restaurant(self, r: Restaurant): self._restaurants[r.id] = r
    def add_rider(self, r: Rider): self._riders[r.id] = r
    def subscribe(self, listener): self._listeners.append(listener)

    def add_to_cart(self, user_id: str, restaurant_id: str, item_id: str, qty: int = 1):
        rest = self._restaurants.get(restaurant_id)
        if rest is None or not rest.is_open:
            raise OrderError("restaurant unavailable")
        if item_id not in rest.menu or item_id not in rest.available:
            raise OrderError("item unavailable")
        if qty <= 0:
            raise OrderError("qty must be positive")
        cart = self._carts.get(user_id)
        if cart and cart.restaurant_id != restaurant_id:
            raise OrderError("cart has items from another restaurant")
        cart = self._carts.setdefault(user_id, Cart(restaurant_id))
        cart.items[item_id] = cart.items.get(item_id, 0) + qty

    def place_order(self, user_id: str) -> Order:
        cart = self._carts.get(user_id)
        if not cart or not cart.items:
            raise OrderError("cart is empty")
        rest = self._restaurants[cart.restaurant_id]
        if not rest.is_open or any(i not in rest.available for i in cart.items):
            # cart kept for the user to fix
            raise OrderError("some items are no longer available")
        total = sum(rest.menu[i].price * q for i, q in cart.items.items())
        order = Order(next(self._ids), user_id, rest.id, dict(cart.items), total)
        self._orders[order.id] = order
        del self._carts[user_id]
        self._publish(order, None)
        return order

    def update_status(self, order_id: int, new: OrderStatus) -> None:
        order = self._orders[order_id]
        if new not in ALLOWED[order.status]:
            raise OrderError(f"{order.status.name} -> {new.name} not allowed")
        if new is S.ACCEPTED:                          # assign before changing state
            rider = self._assign(self._riders.values(),
                                 self._restaurants[order.restaurant_id].location)
            if rider is None:
                raise OrderError("no rider available")
            rider.order_id, order.rider_id = order.id, rider.id
        if new in (S.DELIVERED, S.CANCELLED) and order.rider_id:
            self._riders[order.rider_id].order_id = None
        old, order.status = order.status, new
        self._publish(order, old)

    def _publish(self, order, old):
        for listener in self._listeners:
            listener(order, old)

svc = FoodDeliveryService()
svc.add_restaurant(Restaurant("r1", (0, 0), {"m1": MenuItem("m1", "Dosa", 12000),
                                             "m2": MenuItem("m2", "Idli", 6000)}, {"m1", "m2"}))
svc.add_restaurant(Restaurant("r2", (5, 5), {"p": MenuItem("p", "Pizza", 30000)}, {"p"}))
svc.add_rider(Rider("near", (1, 1))); svc.add_rider(Rider("far", (9, 9)))
log = []
svc.subscribe(lambda o, old: log.append(o.status.name))

svc.add_to_cart("u1", "r1", "m1", 2); svc.add_to_cart("u1", "r1", "m2")
try:
    svc.add_to_cart("u1", "r2", "p"); assert False
except OrderError:
    pass
order = svc.place_order("u1")
assert order.total == 30000
svc.update_status(order.id, S.ACCEPTED)
assert order.rider_id == "near"
try:
    # must go through PREPARING, PICKED_UP
    svc.update_status(order.id, S.DELIVERED); assert False
except OrderError:
    pass
for s in (S.PREPARING, S.PICKED_UP, S.DELIVERED):
    svc.update_status(order.id, s)
assert log == ["PLACED", "ACCEPTED", "PREPARING", "PICKED_UP", "DELIVERED"]
```

**Follow-ups**
- *Cancellation fee after PREPARING?* A `CancellationPolicy` strategy by status; `PREPARING` not in the cancel-allowed set today.
- *Batch two orders to one rider?* Change the rider model to a capacity + list of orders; the assignment strategy considers route overlap.
- *Coupons?* See [cart + coupons](#21-problem-shopping-cart--coupons) — discount strategies applied in `place_order`.
- *Concurrency?* Two orders accepted at once could pick the same rider → assignment under a lock, or a conditional update on the rider row.

#### Where this design bends first

- **Rider assignment** is the piece most likely to change — nearest rider today, batching or ETA-based scoring tomorrow — which is why it's a strategy and not code inside `place_order`.
- **The status machine** is the piece most likely to be wrong. List the transitions explicitly (`PLACED → ACCEPTED → PREPARING → PICKED_UP → DELIVERED`, cancellation only from some states) and reject everything else; interviewers like to ask "can a delivered order be cancelled?" to see whether the code knows.
- **Menu availability** changes during the order: an item can run out between "add to cart" and "place order", so `place_order` re-validates every line instead of trusting the cart.

---

## 26. Problem: Ride sharing (Uber-style LLD)

**How often asked:** common · **Builds on:** [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem), [8. Design patterns](#8-design-patterns)

**Clarify:** riders request rides with pickup/drop and vehicle type; system picks the nearest available driver of that type within a radius; driver may decline (try the next); trip lifecycle; fare = base + per-km × surge; ratings.

**Entities**
- `Driver(id, vehicle_type, location, status, ratings)` · `Trip(id, rider, driver, pickup, drop, status, fare, declined_by)`.
- `TripStatus`: `ASSIGNED → STARTED → COMPLETED`, `ASSIGNED → CANCELLED`.
- Strategies: `MatchingStrategy` (nearest available), `PricingStrategy` (base + distance × surge), `SurgeStrategy` (demand/supply).

```python
from dataclasses import dataclass, field
from enum import Enum, auto
from itertools import count
from math import dist

class DriverStatus(Enum):
    AVAILABLE = auto()
    ON_TRIP = auto()
    OFFLINE = auto()

class TripStatus(Enum):
    ASSIGNED = auto()
    STARTED = auto()
    COMPLETED = auto()
    CANCELLED = auto()

ALLOWED = {
    TripStatus.ASSIGNED: {TripStatus.STARTED, TripStatus.CANCELLED},
    TripStatus.STARTED: {TripStatus.COMPLETED},
    TripStatus.COMPLETED: set(),
    TripStatus.CANCELLED: set(),
}

class RideError(Exception): ...

@dataclass
class Driver:
    id: str
    vehicle_type: str
    location: tuple[float, float]
    status: DriverStatus = DriverStatus.AVAILABLE
    ratings: list[int] = field(default_factory=list)

    @property
    def rating(self) -> float:
        return sum(self.ratings) / len(self.ratings) if self.ratings else 5.0

@dataclass
class Trip:
    id: int
    rider: str
    driver_id: str
    pickup: tuple[float, float]
    drop: tuple[float, float]
    vehicle_type: str
    status: TripStatus = TripStatus.ASSIGNED
    fare: int | None = None
    declined_by: set[str] = field(default_factory=set)

def nearest_available(drivers, pickup, vehicle_type, exclude, radius):
    options = [d for d in drivers if d.status is DriverStatus.AVAILABLE and d.vehicle_type == vehicle_type
               and d.id not in exclude and dist(d.location, pickup) <= radius]
    return min(options, key=lambda d: (dist(d.location, pickup), -d.rating, d.id), default=None)

def distance_pricing(base: dict[str, int], per_km: dict[str, int]):
    def fare(vehicle_type: str, km: float, surge: float) -> int:
        return round((base[vehicle_type] + per_km[vehicle_type] * km) * surge)
    return fare

class RideService:
    def __init__(self, pricing, match=nearest_available, radius_km: float = 5.0, surge=lambda svc: 1.0):
        self.drivers: dict[str, Driver] = {}
        self.trips: dict[int, Trip] = {}
        self._pricing, self._match, self._radius, self._surge = pricing, match, radius_km, surge
        self._ids = count(1)

    def add_driver(self, d: Driver) -> None: self.drivers[d.id] = d

    def update_location(self, driver_id: str, loc) -> None: self.drivers[driver_id].location = loc

    def request_ride(self, rider: str, pickup, drop, vehicle_type: str) -> Trip:
        driver = self._match(self.drivers.values(), pickup, vehicle_type, set(), self._radius)
        if driver is None:
            raise RideError("no driver nearby")
        trip = Trip(next(self._ids), rider, driver.id, pickup, drop, vehicle_type)
        driver.status = DriverStatus.ON_TRIP                # reserve the driver
        self.trips[trip.id] = trip
        return trip

    def decline(self, trip_id: int) -> Trip:
        trip = self.trips[trip_id]
        if trip.status is not TripStatus.ASSIGNED:
            raise RideError("can only decline an assigned trip")
        old = self.drivers[trip.driver_id]
        old.status = DriverStatus.AVAILABLE
        trip.declined_by.add(old.id)
        nxt = self._match(self.drivers.values(), trip.pickup, trip.vehicle_type, trip.declined_by, self._radius)
        if nxt is None:
            trip.status = TripStatus.CANCELLED
            raise RideError("no other driver available")
        nxt.status, trip.driver_id = DriverStatus.ON_TRIP, nxt.id
        return trip

    def _move(self, trip: Trip, new: TripStatus) -> None:
        if new not in ALLOWED[trip.status]:
            raise RideError(f"{trip.status.name} -> {new.name} not allowed")
        trip.status = new

    def start(self, trip_id: int) -> None: self._move(self.trips[trip_id], TripStatus.STARTED)

    def complete(self, trip_id: int) -> int:
        trip = self.trips[trip_id]
        fare = self._pricing(trip.vehicle_type, dist(trip.pickup, trip.drop), self._surge(self))
        self._move(trip, TripStatus.COMPLETED)
        trip.fare = fare
        driver = self.drivers[trip.driver_id]
        driver.status, driver.location = DriverStatus.AVAILABLE, trip.drop
        return fare

    def cancel(self, trip_id: int) -> None:
        trip = self.trips[trip_id]
        self._move(trip, TripStatus.CANCELLED)
        self.drivers[trip.driver_id].status = DriverStatus.AVAILABLE

    def rate_driver(self, trip_id: int, stars: int) -> None:
        trip = self.trips[trip_id]
        if trip.status is not TripStatus.COMPLETED or not 1 <= stars <= 5:
            raise RideError("invalid rating")
        self.drivers[trip.driver_id].ratings.append(stars)

def demand_surge(svc: RideService) -> float:
    busy = sum(d.status is DriverStatus.ON_TRIP for d in svc.drivers.values())
    return 1.5 if busy / max(1, len(svc.drivers)) >= 0.8 else 1.0

svc = RideService(distance_pricing({"mini": 50, "sedan": 80}, {"mini": 10, "sedan": 15}), surge=demand_surge)
svc.add_driver(Driver("d1", "mini", (0, 1)))
svc.add_driver(Driver("d2", "mini", (0, 3)))
svc.add_driver(Driver("d3", "sedan", (0, 0.5)))
trip = svc.request_ride("r1", (0, 0), (0, 4), "mini")
# nearest mini, not the nearer sedan
assert trip.driver_id == "d1"
svc.decline(trip.id)
assert trip.driver_id == "d2" and svc.drivers["d1"].status is DriverStatus.AVAILABLE
svc.start(trip.id)
# 50 + 10 × 4 km, no surge (1 of 3 busy at completion)
assert svc.complete(trip.id) == 90
svc.rate_driver(trip.id, 4)
assert svc.drivers["d2"].rating == 4.0 and svc.drivers["d2"].location == (0, 4)
try:
    svc.request_ride("r2", (100, 100), (0, 0), "mini"); assert False
except RideError:
    pass
```

**Follow-ups:** driver offers with timeouts (offer state + expiry) · pooled rides · real geo index instead of scanning · concurrency: two requests grabbing the same driver → atomic status flip (lock or conditional update).

#### Matching is the part to defend

Everything else is a lifecycle and some arithmetic; matching is where the design choices live. "Nearest available driver" scanned over a list is fine for the interview, but say what changes at scale: a geo index (grid or geohash) to find nearby drivers, an **offer with a timeout** instead of instant assignment (drivers can decline), and an atomic "available → assigned" flip so two simultaneous requests can't take the same driver. Each of these slots into the `MatchingStrategy` and driver-status code without changing the trip model.

For the distributed version, see [44. Problem: Ride hailing (Uber / Lyft)](hld-interview-guide.md#44-problem-ride-hailing-uber--lyft).

---

## 27. Problem: Customer issue resolution system

**How often asked:** occasional · **Builds on:** [8.3 Behavioral](#83-behavioral), [9. Machine-coding: layout, errors, tests](#9-machine-coding-layout-errors-tests) · Reported in machine-coding rounds at PhonePe and Atlassian (candidate reports).

**Clarify:** customers raise issues against a transaction (type: payment, mutual fund, gold, insurance); agents have expertise in some types; assign an issue to a free agent with matching expertise, otherwise waitlist it on such an agent; agents resolve issues and automatically pick the next from their waitlist; search issues by email/type; view agents' work history.

**Entities:** `Issue(id, txn_id, type, subject, email, status, agent_id, resolution)` · `Agent(id, name, expertise, current, waitlist, history)` · `AssignmentStrategy` (free-first, then shortest waitlist).

**Invariants:** an agent works on at most one issue; every OPEN issue is either assigned or waiting in exactly one agent's waitlist (or unassignable).

```python
from collections import deque
from dataclasses import dataclass, field
from enum import Enum, auto
from itertools import count

class IssueType(Enum):
    PAYMENT = auto()
    MUTUAL_FUND = auto()
    GOLD = auto()
    INSURANCE = auto()

class IssueStatus(Enum):
    OPEN = auto()
    WAITING = auto()
    IN_PROGRESS = auto()
    RESOLVED = auto()

@dataclass
class Issue:
    id: str
    txn_id: str
    type: IssueType
    subject: str
    email: str
    status: IssueStatus = IssueStatus.OPEN
    agent_id: str | None = None
    resolution: str | None = None

@dataclass
class Agent:
    id: str
    name: str
    expertise: set[IssueType]
    current: str | None = None
    waitlist: deque = field(default_factory=deque)
    history: list[str] = field(default_factory=list)

def free_then_shortest_queue(agents, issue_type):
    capable = [a for a in agents if issue_type in a.expertise]
    if not capable:
        return None
    free = [a for a in capable if a.current is None]
    pool = free or capable
    return min(pool, key=lambda a: (a.current is not None, len(a.waitlist), a.id))

class IssueService:
    def __init__(self, strategy=free_then_shortest_queue):
        self.issues: dict[str, Issue] = {}
        self.agents: dict[str, Agent] = {}
        self._strategy = strategy
        self._issue_ids, self._agent_ids = count(1), count(1)

    def create_issue(self, txn_id, type_, subject, email) -> str:
        issue = Issue(f"I{next(self._issue_ids)}", txn_id, type_, subject, email)
        self.issues[issue.id] = issue
        return issue.id

    def add_agent(self, name, expertise) -> str:
        agent = Agent(f"A{next(self._agent_ids)}", name, set(expertise))
        self.agents[agent.id] = agent
        return agent.id

    def assign_issue(self, issue_id: str) -> str:
        issue = self.issues[issue_id]
        if issue.status is not IssueStatus.OPEN:
            raise ValueError("issue already assigned or closed")
        agent = self._strategy(self.agents.values(), issue.type)
        if agent is None:
            raise LookupError("no agent with this expertise")
        issue.agent_id = agent.id
        if agent.current is None:
            agent.current, issue.status = issue.id, IssueStatus.IN_PROGRESS
        else:
            agent.waitlist.append(issue.id)
            issue.status = IssueStatus.WAITING
        return agent.id

    def resolve_issue(self, issue_id: str, resolution: str) -> None:
        issue = self.issues[issue_id]
        if issue.status is not IssueStatus.IN_PROGRESS:
            raise ValueError("only in-progress issues can be resolved")
        agent = self.agents[issue.agent_id]
        issue.status, issue.resolution = IssueStatus.RESOLVED, resolution
        agent.history.append(issue.id)
        agent.current = None
        if agent.waitlist:                                  # auto-pick next
            nxt = self.issues[agent.waitlist.popleft()]
            agent.current, nxt.status = nxt.id, IssueStatus.IN_PROGRESS

    def get_issues(self, email: str | None = None, type_: IssueType | None = None) -> list[str]:
        return [i.id for i in self.issues.values()
                if (email is None or i.email == email) and (type_ is None or i.type is type_)]

    def work_history(self) -> dict[str, list[str]]:
        return {a.id: list(a.history) for a in self.agents.values()}

svc = IssueService()
i1 = svc.create_issue("T1", IssueType.PAYMENT, "failed", "a@x.com")
i2 = svc.create_issue("T2", IssueType.PAYMENT, "refund", "b@x.com")
i3 = svc.create_issue("T3", IssueType.GOLD, "price", "a@x.com")
a1 = svc.add_agent("Ann", [IssueType.PAYMENT, IssueType.GOLD])
a2 = svc.add_agent("Bob", [IssueType.GOLD])
assert svc.assign_issue(i1) == a1
assert svc.assign_issue(i3) == a2                       # Ann busy; Bob free and capable
assert svc.assign_issue(i2) == a1 and svc.issues[i2].status is IssueStatus.WAITING
svc.resolve_issue(i1, "retried")
assert svc.agents[a1].current == i2                     # auto-picked from waitlist
assert svc.get_issues(email="a@x.com") == [i1, i3]
assert svc.work_history() == {a1: [i1], a2: []}
```

**Follow-ups:** SLA-based priority (waitlist as a heap by deadline) · agent shifts/capacity > 1 · reassign on agent leave · notify customers on status change (Observer).

#### What evaluators look for in this one

This is a real machine-coding prompt, usually with a fixed set of commands to implement in 90 minutes. Evaluators typically check:
- **All commands work end to end**, with a runnable driver — partial but working beats complete but broken.
- **The assignment strategy is swappable** (first-free, least-loaded, round-robin) without touching the service.
- **Waitlist behaviour is right:** resolving an issue automatically pulls the agent's next waiting issue.
- **Clean separation:** models, repositories, services, strategies — not one class with every method.
- **Edge cases:** an issue type no agent can handle, resolving an issue twice, filtering by multiple criteria.

---

## 28. Problem: Task tracker (Jira-style)

**How often asked:** occasional · **Builds on:** [7.1 State machines with a transition table](#71-state-machines-with-a-transition-table), [8.3 Behavioral](#83-behavioral)

A task tracker looks like CRUD, but three things make it a design problem: **workflows differ per project**, every change must be **auditable**, and people **watch** issues and expect notifications.

**Clarify:** projects with keys (PAY, OPS); issues with type, title, status, assignee, priority; status changes follow the project's workflow; comments; history of every change; watchers notified; search with filters. Out of scope: sprints and boards (mention as extensions).

**Entities**
- `Workflow` — allowed transitions between statuses (data, not code, so each project can have its own).
- `Project(key, workflow)` — issues are numbered per project: PAY-1, PAY-2.
- `Issue(key, title, type, status, assignee, priority, watchers)`.
- `Change(issue, field, old, new, by, at)` — append-only history.
- `Tracker` — the service; notifies watchers on every change (Observer). Search takes composable filters (Specification).

```python
from dataclasses import dataclass, field
from itertools import count

class TrackerError(Exception): ...

@dataclass(frozen=True)
class Workflow:
    initial: str
    transitions: dict[str, frozenset[str]]

    def check(self, old: str, new: str) -> None:
        if new not in self.transitions.get(old, frozenset()):
            raise TrackerError(f"can't move {old} → {new}")

SOFTWARE = Workflow("TODO", {"TODO": frozenset({"IN_PROGRESS"}),
                             "IN_PROGRESS": frozenset({"IN_REVIEW", "TODO"}),
                             "IN_REVIEW": frozenset({"DONE", "IN_PROGRESS"}),
                             "DONE": frozenset({"TODO"})})                      # reopen
SIMPLE = Workflow("OPEN", {"OPEN": frozenset({"CLOSED"}), "CLOSED": frozenset({"OPEN"})})

@dataclass
class Issue:
    key: str
    title: str
    type: str
    status: str
    reporter: str
    priority: str = "MEDIUM"
    assignee: str | None = None
    watchers: set[str] = field(default_factory=set)

@dataclass(frozen=True)
class Change:
    key: str
    field: str
    old: object
    new: object
    by: str
    at: int

class Tracker:
    def __init__(self, clock=lambda: 0):
        self._clock = clock
        self._projects: dict[str, tuple[Workflow, count]] = {}
        self.issues: dict[str, Issue] = {}
        self.history: list[Change] = []
        self.notifications: list[tuple[str, str]] = []      # (user, message)

    def create_project(self, key: str, workflow: Workflow) -> None:
        if key in self._projects:
            raise TrackerError("project exists")
        self._projects[key] = (workflow, count(1))

    def create_issue(self, project: str, title: str, type: str, reporter: str, **kw) -> Issue:
        workflow, numbers = self._projects[project]
        issue = Issue(f"{project}-{next(numbers)}", title, type, workflow.initial, reporter, **kw)
        issue.watchers.add(reporter)
        self.issues[issue.key] = issue
        return issue

    def _change(self, issue: Issue, name: str, new, by: str) -> None:
        old = getattr(issue, name)
        setattr(issue, name, new)
        self.history.append(Change(issue.key, name, old, new, by, self._clock()))
        for user in issue.watchers - {by}:                  # don't notify yourself
            self.notifications.append((user, f"{issue.key} {name}: {old} → {new} by {by}"))

    def assign(self, key: str, user: str, by: str) -> None:
        issue = self.issues[key]
        issue.watchers.add(user)
        self._change(issue, "assignee", user, by)

    def transition(self, key: str, new_status: str, by: str) -> None:
        issue = self.issues[key]
        self._projects[key.split("-")[0]][0].check(issue.status, new_status)
        self._change(issue, "status", new_status, by)

    def search(self, *filters) -> list[str]:
        return sorted(k for k, i in self.issues.items() if all(f(i) for f in filters))

status_is = lambda s: lambda i: i.status == s
assigned_to = lambda u: lambda i: i.assignee == u

t = Tracker()
t.create_project("PAY", SOFTWARE)
t.create_project("OPS", SIMPLE)
bug = t.create_issue("PAY", "Refund fails for UPI", "BUG", reporter="asha", priority="HIGH")
t.create_issue("PAY", "Add wallet top-up limit", "TASK", reporter="asha")
ops = t.create_issue("OPS", "Rotate DB password", "TASK", reporter="ravi")
assert (bug.key, ops.key, ops.status) == ("PAY-1", "OPS-1", "OPEN")

t.assign("PAY-1", "vikram", by="asha")
try:
    t.transition("PAY-1", "DONE", by="vikram"); raise AssertionError
except TrackerError:
    pass                                                    # TODO → DONE skips review
t.transition("PAY-1", "IN_PROGRESS", by="vikram")
# a different workflow per project
t.transition("OPS-1", "CLOSED", by="ravi")
assert t.search(status_is("IN_PROGRESS"), assigned_to("vikram")) == ["PAY-1"]
assert ("asha", "PAY-1 status: TODO → IN_PROGRESS by vikram") in t.notifications
assert [c.field for c in t.history if c.key == "PAY-1"] == ["assignee", "status"]
```

**Why it's built this way**
- **Workflows are data.** A transition table per project means a new workflow is configuration, not a new class — the same idea as the order state machines elsewhere in this book, made editable.
- **History is append-only**, written in the same place every change goes through (`_change`), so no code path can update an issue without leaving a record. The current issue is effectively a cache of its history.
- **Notifications go through one place** too; in a real system `_change` would publish an event and a separate notifier would send emails — so a slow mail server never slows down updating an issue.

**Extensions:** custom fields per project (a `dict` of typed field definitions on the project) · sprints (an issue belongs to at most one active sprint; a sprint has start/end) · transition rules ("only the assignee can move to IN_REVIEW", "resolution required to close") as validators attached to transitions · search at scale → an inverted index or a search engine ([34. Search](hld-interview-guide.md#34-search)).

---

## 29. Problem: Online auction

**How often asked:** occasional · **Builds on:** [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem), [8. Design patterns](#8-design-patterns)

**Clarify:** sellers create auctions (start price, minimum increment, end time, optional reserve price); users place bids; seller can't bid; no bids after the end; **anti-sniping**: a bid in the last minute extends the end; closing picks the winner if the reserve is met.

**Invariant:** the highest bid is strictly increasing by at least the increment; bids are accepted only while OPEN and before `ends_at`.

```python
from dataclasses import dataclass, field
from itertools import count
from threading import Lock

class AuctionError(Exception): ...

@dataclass
class Bid:
    user: str
    amount: int
    at: float

@dataclass
class Auction:
    id: int
    item: str
    seller: str
    start_price: int
    increment: int
    ends_at: float
    reserve: int = 0
    bids: list[Bid] = field(default_factory=list)
    closed: bool = False
    winner: str | None = None
    lock: Lock = field(default_factory=Lock, repr=False)

    @property
    def highest(self) -> Bid | None:
        return self.bids[-1] if self.bids else None

class AuctionHouse:
    SNIPE_WINDOW = 60

    def __init__(self, clock):
        self.auctions: dict[int, Auction] = {}
        self._clock, self._ids = clock, count(1)

    def create(self, item, seller, start_price, increment, duration, reserve=0) -> int:
        if start_price <= 0 or increment <= 0 or duration <= 0:
            raise AuctionError("invalid auction parameters")
        a = Auction(next(self._ids), item, seller, start_price, increment, self._clock() + duration, reserve)
        self.auctions[a.id] = a
        return a.id

    def bid(self, auction_id: int, user: str, amount: int) -> None:
        a = self.auctions[auction_id]
        # check-then-act on the highest bid
        with a.lock:
            now = self._clock()
            if a.closed or now >= a.ends_at:
                raise AuctionError("auction ended")
            if user == a.seller:
                raise AuctionError("seller can't bid")
            minimum = a.start_price if a.highest is None else a.highest.amount + a.increment
            if amount < minimum:
                raise AuctionError(f"bid at least {minimum}")
            a.bids.append(Bid(user, amount, now))
            if a.ends_at - now < self.SNIPE_WINDOW:
                a.ends_at = now + self.SNIPE_WINDOW        # anti-sniping extension

    def close(self, auction_id: int) -> str | None:
        a = self.auctions[auction_id]
        with a.lock:
            if self._clock() < a.ends_at:
                raise AuctionError("not over yet")
            if not a.closed:
                a.closed = True
                if a.highest and a.highest.amount >= a.reserve:
                    a.winner = a.highest.user
            return a.winner

now = [0.0]
house = AuctionHouse(clock=lambda: now[0])
aid = house.create("vintage watch", "seller", start_price=1000, increment=100, duration=600, reserve=1500)
house.bid(aid, "u1", 1000)
try:
    house.bid(aid, "u2", 1050); assert False          # below 1000 + 100
except AuctionError:
    pass
house.bid(aid, "u2", 1200)
now[0] = 570                                          # last 30 seconds
house.bid(aid, "u1", 1600)
assert house.auctions[aid].ends_at == 630             # extended
now[0] = 600
try:
    house.close(aid); assert False
except AuctionError:
    pass
now[0] = 630
assert house.close(aid) == "u1"
try:
    house.bid(aid, "u3", 5000); assert False
except AuctionError:
    pass
```

**Follow-ups:** proxy/auto-bidding (each user sets a max; the system bids the minimum needed — Strategy) · notifications when outbid (Observer) · payments and escrow on close · scheduled closing (task scheduler) · scale (bids per auction serialized per auction; shard by auction_id).

#### Hard close vs soft close

eBay auctions end at a fixed time (a "hard close"), which is why **sniping** exists: bidders — often software — place a bid in the last few seconds so others can't respond. Many other auction platforms use a **soft close** instead, extending the end time whenever a bid lands near it, which is what this chapter implements. Neither is wrong: hard closes are predictable for buyers; soft closes tend to extract a price closer to what bidders are really willing to pay. eBay's answer to sniping was **proxy bidding** — you tell the system your maximum and it bids the minimum needed on your behalf — which reduces the advantage of last-second bids.

---

## 30. Problem: Cricket scoreboard

**How often asked:** occasional (popular in Indian machine-coding rounds) · **Builds on:** [7.1 State machines with a transition table](#71-state-machines-with-a-transition-table), [8.3 Behavioral](#83-behavioral)

The trap is storing lots of counters (score, wickets, overs, each batter's runs) and updating all of them on every ball — then a scorer's mistake ("that was a wide, not a dot ball") is painful to undo. The cleaner design: **store the balls, and compute everything else from them.** Undo becomes "remove the last ball".

**Rules that matter for the model**
- An over has 6 **legal** deliveries. Wides and no-balls add 1 extra run each and don't count towards the over.
- Batters swap ends after an odd number of runs, and again at the end of each over.
- A wicket brings the next batter in; the innings ends when the overs run out or there's only one batter left.

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Ball:
    runs: int = 0                    # runs off the bat
    extra: str | None = None         # "wide", "no_ball", "bye", "leg_bye"
    extra_runs: int = 0              # byes/leg byes, or runs taken off a wide
    # striker out (run-outs of the other batter: an extension)
    wicket: bool = False

class InningsOver(Exception): ...

class Innings:
    def __init__(self, batting_order: list[str], overs: int):
        self.order, self.max_balls = batting_order, overs * 6
        self.balls: list[Ball] = []

    def add(self, ball: Ball) -> None:
        if self.summary()["complete"]:
            raise InningsOver()
        self.balls.append(ball)

    def undo(self) -> None:
        self.balls.pop()                         # everything else is recomputed

    def summary(self) -> dict:
        striker, non_striker, next_in = self.order[0], self.order[1], 2
        runs = wickets = legal = 0
        batters = {name: [0, 0] for name in self.order}          # runs, balls faced
        for b in self.balls:
            is_legal = b.extra not in ("wide", "no_ball")
            runs += b.runs + b.extra_runs + (0 if is_legal else 1)
            batters[striker][0] += b.runs
            if b.extra != "wide":
                # a no-ball is still faced
                batters[striker][1] += 1
            legal += is_legal
            if b.wicket:
                wickets += 1
                striker = self.order[next_in] if next_in < len(self.order) else None
                next_in += 1
            elif (b.runs + b.extra_runs) % 2:
                striker, non_striker = non_striker, striker       # ran an odd number
            if is_legal and legal % 6 == 0:
                striker, non_striker = non_striker, striker       # end of the over
        complete = legal >= self.max_balls or wickets >= len(self.order) - 1
        return {"score": f"{runs}/{wickets}", "overs": f"{legal // 6}.{legal % 6}",
                "striker": striker, "batters": batters, "complete": complete}

inn = Innings(["Rohit", "Gill", "Kohli"], overs=2)
for ball in [Ball(1), Ball(4), Ball(extra="wide"), Ball(0), Ball(wicket=True), Ball(2), Ball(1)]:
    inn.add(ball)
s = inn.summary()
assert (s["score"], s["overs"], s["striker"]) == ("9/1", "1.0", "Kohli")
assert s["batters"] == {"Rohit": [1, 1], "Gill": [4, 3], "Kohli": [3, 2]}
# the scorer fixes a mistake
inn.undo()
assert (inn.summary()["score"], inn.summary()["overs"]) == ("8/1", "0.5")
```

**Why compute instead of store?** The ball list is the truth; every number on the scoreboard is a view of it. That gives undo for free, makes every statistic consistent with every other, and lets you add new views (run rate, partnership, a bowler's figures) without touching how balls are recorded — event sourcing in miniature. Recomputing 120 balls per update is nothing; for a live service with millions of viewers you'd compute once per ball and push the summary out ([26. Problem: Notification system](hld-interview-guide.md#26-problem-notification-system)).

**Extensions:** bowlers (who bowled each over; a bowler can't bowl consecutive overs) · second innings with a target · run-outs of the non-striker (the `Ball` records who is out) · commentary as another view of the same events.

---

## 31. Problem: Logging framework

**How often asked:** very common · **Builds on:** [8. Design patterns](#8-design-patterns)

**Clarify:** levels (DEBUG < INFO < WARN < ERROR); multiple destinations (console, file, remote); formats (text, JSON); per-module loggers (`app.db`) inheriting settings; must not slow down requests.

**Entities**
- `LogRecord(logger, level, message, ts)`.
- `Formatter` (Strategy): `TextFormatter`, `JsonFormatter`.
- `Handler` (sink) with its own level + formatter; `MemoryHandler` here (stands in for console/file); `AsyncHandler` wraps another handler with a queue + background thread.
- `Logger(name, level, handlers, parent, propagate)` — hierarchy by dotted names; a record goes to this logger's handlers and **propagates** to ancestors (Chain of Responsibility).
- `LogManager.get_logger(name)` — one logger per name (registry/singleton per name).

```python
import json
import queue
import threading
from abc import ABC, abstractmethod
from dataclasses import dataclass
from enum import IntEnum
from time import time

class Level(IntEnum):
    DEBUG = 10
    INFO = 20
    WARN = 30
    ERROR = 40

@dataclass(frozen=True)
class LogRecord:
    logger: str
    level: Level
    message: str
    ts: float

class Formatter(ABC):
    @abstractmethod
    def format(self, r: LogRecord) -> str: ...

class TextFormatter(Formatter):
    def format(self, r): return f"{r.level.name} {r.logger}: {r.message}"

class JsonFormatter(Formatter):
    def format(self, r): return json.dumps({"level": r.level.name, "logger": r.logger, "msg": r.message})

class Handler(ABC):
    def __init__(self, level: Level = Level.DEBUG, formatter: Formatter | None = None):
        self.level, self.formatter = level, formatter or TextFormatter()
        # lines from threads don't interleave
        self._lock = threading.Lock()

    def handle(self, r: LogRecord) -> None:
        if r.level >= self.level:
            line = self.formatter.format(r)
            with self._lock:
                self.emit(line)

    @abstractmethod
    def emit(self, line: str) -> None: ...

class MemoryHandler(Handler):
    def __init__(self, *a, **kw):
        super().__init__(*a, **kw)
        self.lines: list[str] = []
    def emit(self, line): self.lines.append(line)

class AsyncHandler(Handler):
    """Producer-consumer: callers enqueue; one background thread writes to the inner handler."""
    _STOP = object()

    def __init__(self, inner: Handler, capacity: int = 10_000):
        super().__init__(inner.level, inner.formatter)
        self._inner, self._q, self.dropped = inner, queue.Queue(capacity), 0
        # daemon so a missing close() can't hang exit; close() still drains the queue
        self._thread = threading.Thread(target=self._run, daemon=True)
        self._thread.start()

    def handle(self, r):
        try:
            self._q.put_nowait(r)                   # never block the request path
        except queue.Full:
            self.dropped += 1

    def emit(self, line): raise NotImplementedError

    def _run(self):
        while (r := self._q.get()) is not self._STOP:
            self._inner.handle(r)

    def close(self):
        self._q.put(self._STOP)
        self._thread.join()

class Logger:
    def __init__(self, name: str, parent: "Logger | None" = None, level: Level | None = None):
        self.name, self.parent, self.level = name, parent, level
        self.handlers: list[Handler] = []
        self.propagate = True

    def effective_level(self) -> Level:
        logger = self
        while logger.level is None:
            logger = logger.parent
        return logger.level

    def log(self, level: Level, message: str) -> None:
        if level < self.effective_level():
            return
        record = LogRecord(self.name, level, message, time())
        logger = self
        while logger:
            for h in logger.handlers:
                h.handle(record)
            if not logger.propagate:
                break
            logger = logger.parent

    def debug(self, m): self.log(Level.DEBUG, m)
    def info(self, m): self.log(Level.INFO, m)
    def warn(self, m): self.log(Level.WARN, m)
    def error(self, m): self.log(Level.ERROR, m)

class LogManager:
    def __init__(self):
        self.root = Logger("root", level=Level.INFO)
        self._loggers = {"root": self.root}
        self._lock = threading.Lock()

    def get_logger(self, name: str) -> Logger:
        with self._lock:
            if name in self._loggers:
                return self._loggers[name]
            parent, parts = self.root, name.split(".")
            for i in range(1, len(parts) + 1):
                key = ".".join(parts[:i])
                if key not in self._loggers:
                    self._loggers[key] = Logger(key, parent)
                parent = self._loggers[key]
            return parent

mgr = LogManager()
everything = MemoryHandler(Level.DEBUG)
errors_json = MemoryHandler(Level.ERROR, JsonFormatter())
mgr.root.handlers += [everything, errors_json]
db = mgr.get_logger("app.db")
db.level = Level.DEBUG
http = mgr.get_logger("app.http")                    # inherits INFO from root
assert mgr.get_logger("app.db") is db and db.parent is mgr.get_logger("app")
db.debug("query took 3ms")
http.debug("dropped")                                 # below effective level
http.error("500 on /orders")
assert everything.lines == ["DEBUG app.db: query took 3ms", "ERROR app.http: 500 on /orders"]
assert json.loads(errors_json.lines[0])["logger"] == "app.http"

sink = MemoryHandler()
async_h = AsyncHandler(sink)
worker = mgr.get_logger("worker"); worker.handlers.append(async_h); worker.propagate = False
for i in range(100):
    worker.info(f"job {i}")
async_h.close()
assert len(sink.lines) == 100 and async_h.dropped == 0
```

**Follow-ups**
- *Log file rotation?* A `RotatingFileHandler` decorator: when size > N, rename and reopen.
- *Queue full policy?* Drop DEBUG/INFO, block or write synchronously for ERROR.
- *Structured context (request_id)?* Thread-local/contextvars context merged into every record.
- *Configuration from file?* A builder/factory reads config and creates handlers/formatters by name.

#### This design is Python's own logging module

The hierarchy (`app` → `app.db`), propagation to parent loggers, per-handler levels and formatters in this chapter mirror Python's standard `logging` module (and Java's Log4j/Logback, which inspired it). If you're asked to design a logger, it's fair to say so — then show you understand *why* each piece exists.

#### The cautionary tale: Log4Shell

In December 2021, a vulnerability in Apache Log4j 2 (CVE-2021-44228, "Log4Shell") let attackers run code on servers just by getting a crafted string logged — for example in a username or HTTP header. Log4j's message lookup feature interpreted `${jndi:ldap://…}` inside log messages and fetched remote content. It affected a huge number of Java applications. The design lesson fits any logging framework: **log messages are data**; the formatter must never interpret or execute their contents.

---

## 32. Problem: Notification / pub-sub system

**How often asked:** very common · **Builds on:** [8. Design patterns](#8-design-patterns)

**Clarify:** topics; publishers; subscribers; push (callbacks) or pull (consumers read at their own pace)? replay? Ordering? This version is Kafka-like **pull with offsets**, the more common machine-coding ask ("design a message queue").

**Entities**
- `Message(offset, payload, timestamp)`
- `Topic(name, messages)` — append-only log
- `Broker` — creates topics, publishes, tracks **per-subscriber offsets per topic**
- Subscriber is just an ID here; push mode would add a callback per subscriber (Observer)

**Invariants:** messages in a topic are ordered by offset; each subscriber reads each message once unless it resets its offset (replay).

```python
from dataclasses import dataclass
from threading import Lock

@dataclass(frozen=True)
class Message:
    offset: int
    payload: object

class Broker:
    def __init__(self, retention: int | None = None):
        self._topics: dict[str, list[Message]] = {}
        # offset of the first retained message
        self._base: dict[str, int] = {}
        self._offsets: dict[tuple[str, str], int] = {}
        self._retention = retention
        self._lock = Lock()

    def create_topic(self, topic: str) -> None:
        with self._lock:
            if topic in self._topics:
                raise ValueError("topic exists")
            self._topics[topic], self._base[topic] = [], 0

    def subscribe(self, subscriber: str, topic: str, from_beginning: bool = False) -> None:
        with self._lock:
            log = self._log(topic)
            start = self._base[topic] if from_beginning else self._base[topic] + len(log)
            self._offsets[(subscriber, topic)] = start

    def publish(self, topic: str, payload) -> int:
        with self._lock:
            log = self._log(topic)
            offset = self._base[topic] + len(log)
            log.append(Message(offset, payload))
            if self._retention is not None and len(log) > self._retention:
                drop = len(log) - self._retention
                del log[:drop]
                self._base[topic] += drop
            return offset

    def poll(self, subscriber: str, topic: str, max_messages: int = 10) -> list[Message]:
        with self._lock:
            key = (subscriber, topic)
            if key not in self._offsets:
                raise KeyError("not subscribed")
            log, base = self._log(topic), self._base[topic]
            start = max(self._offsets[key], base)     # skip messages lost to retention
            batch = log[start - base: start - base + max_messages]
            self._offsets[key] = start + len(batch)   # auto-commit after read
            return batch

    def seek(self, subscriber: str, topic: str, offset: int) -> None:
        with self._lock:
            if (subscriber, topic) not in self._offsets:
                raise KeyError("not subscribed")
            self._offsets[(subscriber, topic)] = offset

    def _log(self, topic):
        if topic not in self._topics:
            raise KeyError(f"unknown topic {topic}")
        return self._topics[topic]

b = Broker(retention=3)
b.create_topic("orders")
b.subscribe("billing", "orders")
b.publish("orders", "o1"); b.publish("orders", "o2")
b.subscribe("late", "orders")                       # only sees new messages
b.subscribe("audit", "orders", from_beginning=True)
b.publish("orders", "o3")
assert [m.payload for m in b.poll("billing", "orders")] == ["o1", "o2", "o3"]
assert [m.payload for m in b.poll("late", "orders")] == ["o3"]
assert b.poll("billing", "orders") == []
b.seek("billing", "orders", 1)                      # replay
assert [m.payload for m in b.poll("billing", "orders", 1)] == ["o2"]
b.publish("orders", "o4")                           # retention drops o1
assert [m.payload for m in b.poll("audit", "orders")] == ["o2", "o3", "o4"]
```

**Follow-ups**
- *Push delivery?* Subscribers register callbacks; a dispatcher thread per subscriber calls them (a slow subscriber must not block others → per-subscriber queue).
- *At-least-once?* Don't auto-commit: `poll` returns messages, the consumer calls `commit(offset)` after processing. A crash before commit → redelivery.
- *Consumer groups?* Partition the topic; each partition assigned to one consumer in the group.
- *Blocking poll (wait for new messages)?* Condition variable: `poll` waits on `cond` until the log grows; `publish` calls `notify_all`. See [concurrency part](#42-concurrency-what-gets-asked).
- *Notification-service flavor (email/SMS/push)?* Channels as strategies (`EmailSender`, `SmsSender`) behind a `NotificationSender` interface; user preferences decide channels; retry with backoff; templates.

#### Pull or push?

This chapter builds a **pull** design: subscribers ask for messages from their offset. Push (the broker calls a subscriber's callback) feels simpler but makes the broker responsible for slow and failing subscribers — it needs a queue and a thread per subscriber, retries, and a policy for when a queue fills up. Pull pushes that responsibility to the consumer, which reads at its own pace and can replay by moving its offset. That's why Kafka chose pull; [60. Problem: In-memory pub-sub with concurrent subscribers](#60-problem-in-memory-pub-sub-with-concurrent-subscribers) shows the push version with per-subscriber queues.

---

## 33. Problem: In-memory file system

**How often asked:** common · **Builds on:** [8.2 Structural](#82-structural), [41.1 Files, inodes and links](#411-files-inodes-and-links) · Reported at Atlassian and Uber (candidate reports).

**Design:** **Composite** — `Directory` and `File` share a `Node` base; a directory holds children by name. Paths are parsed into parts and walked from the root.

**Operations:** `mkdir(path)` (create parents like `mkdir -p`), `write(path, content, append=False)`, `read(path)`, `ls(path)` (sorted names; for a file, just its name), `rm(path)`, `size(path)` (recursive for directories).

```python
class Node:
    def __init__(self, name: str):
        self.name = name

class File(Node):
    def __init__(self, name: str):
        super().__init__(name)
        self.content = ""

    def size(self) -> int:
        return len(self.content)

class Directory(Node):
    def __init__(self, name: str):
        super().__init__(name)
        self.children: dict[str, Node] = {}

    def size(self) -> int:
        return sum(child.size() for child in self.children.values())

class FileSystem:
    def __init__(self):
        self.root = Directory("")

    @staticmethod
    def _parts(path: str) -> list[str]:
        if not path.startswith("/"):
            raise ValueError("path must be absolute")
        return [p for p in path.split("/") if p]

    def _walk(self, parts, create_dirs=False) -> Directory:
        node = self.root
        for part in parts:
            child = node.children.get(part)
            if child is None:
                if not create_dirs:
                    raise FileNotFoundError("/" + "/".join(parts))
                child = node.children[part] = Directory(part)
            if not isinstance(child, Directory):
                raise NotADirectoryError(part)
            node = child
        return node

    def mkdir(self, path: str) -> None:
        self._walk(self._parts(path), create_dirs=True)

    def write(self, path: str, content: str, append: bool = False) -> None:
        *dirs, name = self._parts(path)
        parent = self._walk(dirs, create_dirs=True)
        node = parent.children.get(name)
        if isinstance(node, Directory):
            raise IsADirectoryError(path)
        if node is None:
            node = parent.children[name] = File(name)
        node.content = node.content + content if append else content

    def read(self, path: str) -> str:
        node = self._get(path)
        if not isinstance(node, File):
            raise IsADirectoryError(path)
        return node.content

    def ls(self, path: str) -> list[str]:
        node = self._get(path)
        return [node.name] if isinstance(node, File) else sorted(node.children)

    def rm(self, path: str) -> None:
        *dirs, name = self._parts(path)
        parent = self._walk(dirs)
        if name not in parent.children:
            raise FileNotFoundError(path)
        del parent.children[name]

    def size(self, path: str) -> int:
        return self._get(path).size()

    def _get(self, path: str) -> Node:
        parts = self._parts(path)
        if not parts:
            return self.root
        *dirs, name = parts
        parent = self._walk(dirs)
        if name not in parent.children:
            raise FileNotFoundError(path)
        return parent.children[name]

fs = FileSystem()
fs.mkdir("/a/b")
fs.write("/a/b/notes.txt", "hello")
fs.write("/a/b/notes.txt", " world", append=True)
fs.write("/a/c.txt", "xyz")
assert fs.read("/a/b/notes.txt") == "hello world"
assert fs.ls("/a") == ["b", "c.txt"] and fs.ls("/a/c.txt") == ["c.txt"]
assert fs.size("/a") == 14
fs.rm("/a/b")
assert fs.ls("/a") == ["c.txt"]
try:
    fs.read("/a/b/notes.txt"); assert False
except FileNotFoundError:
    pass
```

**Follow-ups:** `mv` (detach from one parent, attach to another; reject moving a dir into itself) · permissions (owner + mode on `Node`, checked in `_walk`) · search by name/extension (DFS with a filter strategy) · thread safety (one lock for the tree, or a read-write lock since reads dominate).

#### Why Composite fits

Most operations need to treat "a file" and "a directory full of things" the same way: `size()` of a file is its length; of a directory, the sum of its children's sizes. With a shared `Node` base, that recursion is two short methods instead of `if isinstance(...)` checks in every operation. The parts that need care are all in **path handling** — `"/a//b/"`, `.` and `..`, and refusing to `mv` a directory into its own subtree — so write one `_walk(path)` helper and test it hard.

---

## 34. Problem: Stack Overflow / Q&A

**How often asked:** occasional · **Builds on:** [8. Design patterns](#8-design-patterns)

**Clarify:** users post questions (with tags), answers, comments; upvote/downvote questions and answers (one vote per user per post, changeable); the asker accepts one answer; reputation changes; search by tag/keyword.

**Reputation rules (example):** question upvote +5, answer upvote +10, any downvote −2, accepted answer +15 to the answerer. Changing a vote reverses the old effect first.

```python
from collections import defaultdict
from dataclasses import dataclass, field
from itertools import count

class QAError(Exception): ...

@dataclass
class Post:
    id: int
    author: str
    body: str
    score: int = 0
    comments: list[tuple[str, str]] = field(default_factory=list)

@dataclass
class Question(Post):
    title: str = ""
    tags: set[str] = field(default_factory=set)
    answer_ids: list[int] = field(default_factory=list)
    accepted: int | None = None

@dataclass
class Answer(Post):
    question_id: int = 0

REP = {("Question", 1): 5, ("Answer", 1): 10, ("Question", -1): -2, ("Answer", -1): -2}
ACCEPT_REP = 15

class QA:
    def __init__(self):
        self.reputation: dict[str, int] = defaultdict(int)
        self.posts: dict[int, Post] = {}
        self._votes: dict[tuple[str, int], int] = {}
        self._by_tag: dict[str, set[int]] = defaultdict(set)
        self._ids = count(1)

    def ask(self, user, title, body, tags) -> int:
        q = Question(next(self._ids), user, body, title=title, tags={t.lower() for t in tags})
        self.posts[q.id] = q
        for t in q.tags:
            self._by_tag[t].add(q.id)
        return q.id

    def answer(self, user, question_id, body) -> int:
        q = self.posts.get(question_id)
        if not isinstance(q, Question):
            raise QAError("unknown question")
        a = Answer(next(self._ids), user, body, question_id=question_id)
        self.posts[a.id] = a
        q.answer_ids.append(a.id)
        return a.id

    def comment(self, user, post_id, text) -> None:
        self.posts[post_id].comments.append((user, text))

    def vote(self, user, post_id, value: int) -> None:
        if value not in (1, -1, 0):
            raise QAError("vote must be +1, -1 or 0 (remove)")
        post = self.posts[post_id]
        if post.author == user:
            raise QAError("can't vote on your own post")
        kind = type(post).__name__
        old = self._votes.get((user, post_id), 0)
        if old == value:
            return
        if old:                                         # reverse previous vote
            post.score -= old
            self.reputation[post.author] -= REP[(kind, old)]
        if value:
            post.score += value
            self.reputation[post.author] += REP[(kind, value)]
            self._votes[(user, post_id)] = value
        else:
            self._votes.pop((user, post_id), None)

    def accept(self, user, answer_id) -> None:
        a = self.posts[answer_id]
        q = self.posts[a.question_id]
        if q.author != user:
            raise QAError("only the asker can accept")
        if q.accepted is not None:
            self.reputation[self.posts[q.accepted].author] -= ACCEPT_REP
        q.accepted = answer_id
        self.reputation[a.author] += ACCEPT_REP

    def search(self, tag: str | None = None, keyword: str = "") -> list[int]:
        ids = self._by_tag.get(tag.lower(), set()) if tag else {p for p in self.posts if isinstance(self.posts[p], Question)}
        kw = keyword.lower()
        return sorted(i for i in ids if kw in self.posts[i].title.lower() or kw in self.posts[i].body.lower())

qa = QA()
q = qa.ask("asha", "How does GIL work?", "Threads in CPython...", ["Python", "concurrency"])
a1 = qa.answer("ravi", q, "It's a mutex around bytecode execution")
a2 = qa.answer("meera", q, "Use multiprocessing")
qa.vote("ravi", q, 1); qa.vote("meera", a1, 1); qa.vote("asha", a2, -1)
assert qa.reputation == {"asha": 5, "ravi": 10, "meera": -2}
qa.vote("asha", a2, 1)                                   # change vote: -(-2) + 10
assert qa.reputation["meera"] == 10 and qa.posts[a2].score == 1
qa.accept("asha", a1)
assert qa.reputation["ravi"] == 25
try:
    qa.vote("ravi", a1, 1); assert False
except QAError:
    pass
assert qa.search(tag="python") == [q] and qa.search(keyword="gil") == [q] and qa.search(tag="java") == []
```

**Follow-ups:** badges (Observer on reputation events) · close/duplicate questions (state) · bounty (escrowed reputation) · search ranking (score + recency) · moderation queues.

#### Votes are the tricky part

Store votes as **one row per (user, post)** with a value of +1 or −1, not as counters you increment. Then "change my vote" is: look up the old value, reverse its reputation effect, apply the new one — and a user can never vote twice. The post's score is the sum of its votes (kept as a cached counter for speed, updated in the same operation). Reputation works the same way: an append-only list of reputation events makes it easy to recompute, audit, and award badges by watching the event stream.

---

## 35. Problem: LRU cache and LFU cache (with TTL)

**How often asked:** very common · **Builds on:** [3. OOP in depth](#3-oop-in-depth), [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)

**Clarify:** capacity; `get` returns value or miss; `put` inserts/updates and evicts when full; O(1) required; thread-safe?

**LRU design:** hashmap `key → node` + **doubly linked list** ordered by recency (head = most recent). Sentinel head/tail nodes remove edge cases. `get`: move node to front. `put`: update or insert at front; if over capacity remove `tail.prev`.

Interviewers usually want the linked list written by hand (not `OrderedDict`):

```python
class _Node:
    __slots__ = ("key", "val", "prev", "next")
    def __init__(self, key=None, val=None):
        self.key, self.val, self.prev, self.next = key, val, None, None

class LRUCache:
    def __init__(self, capacity: int):
        if capacity < 0:
            raise ValueError("capacity must be >= 0")
        self._cap = capacity
        self._map: dict = {}
        self._head, self._tail = _Node(), _Node()          # sentinels
        self._head.next, self._tail.prev = self._tail, self._head

    def _unlink(self, node: _Node) -> None:
        node.prev.next, node.next.prev = node.next, node.prev

    def _push_front(self, node: _Node) -> None:
        node.prev, node.next = self._head, self._head.next
        self._head.next.prev = node
        self._head.next = node

    def get(self, key):
        node = self._map.get(key)
        if node is None:
            return None
        self._unlink(node)
        self._push_front(node)
        return node.val

    def put(self, key, val) -> None:
        if self._cap == 0:
            return
        node = self._map.get(key)
        if node:
            node.val = val
            self._unlink(node)
            self._push_front(node)
            return
        if len(self._map) == self._cap:
            lru = self._tail.prev
            self._unlink(lru)
            del self._map[lru.key]
        node = _Node(key, val)
        self._map[key] = node
        self._push_front(node)

    def keys_most_recent_first(self) -> list:
        out, node = [], self._head.next
        while node is not self._tail:
            out.append(node.key)
            node = node.next
        return out

c = LRUCache(2)
c.put(1, "a"); c.put(2, "b")
assert c.get(1) == "a"                 # 1 becomes most recent
c.put(3, "c")                          # evicts 2
assert c.get(2) is None and c.keys_most_recent_first() == [3, 1]
c.put(1, "A")                          # update moves to front
assert c.get(1) == "A" and c.keys_most_recent_first() == [1, 3]
```

**LFU design (O(1)):**
- `vals: key → (value, freq, expires_at)`
- `freqs: freq → OrderedDict of keys` (LRU order within the same frequency for tie-breaks)
- `min_freq` — the lowest frequency present; eviction pops the oldest key from `freqs[min_freq]`.
- On access: move the key from `freqs[f]` to `freqs[f+1]`; if `freqs[f]` becomes empty and `f == min_freq`, then `min_freq = f + 1`. A new key has freq 1 → `min_freq = 1`.
- **TTL:** store `expires_at`; check lazily on `get`. Active expiry (a min-heap of expiry times or random sampling like Redis) is an extension.

```python
from collections import OrderedDict, defaultdict
from time import monotonic

class LFUCache:
    def __init__(self, capacity: int, clock=monotonic):
        if capacity < 0:
            raise ValueError("capacity must be >= 0")
        self._cap, self._clock = capacity, clock
        # key -> (value, freq, expires_at | None)
        self._vals = {}
        self._freqs = defaultdict(OrderedDict)   # freq -> keys, least recent first
        self._min_freq = 0

    def get(self, key):
        item = self._vals.get(key)
        if item is None:
            return None
        if item[2] is not None and item[2] <= self._clock():
            self._remove(key)                    # lazy expiry
            return None
        self._bump(key)
        return self._vals[key][0]

    def put(self, key, value, ttl: float | None = None) -> None:
        if self._cap == 0:
            return
        expires = None if ttl is None else self._clock() + ttl
        if key in self._vals:
            _, freq, _ = self._vals[key]
            self._vals[key] = (value, freq, expires)
            self._bump(key)
            return
        if len(self._vals) >= self._cap:
            self._evict()
        self._vals[key] = (value, 1, expires)
        self._freqs[1][key] = None
        self._min_freq = 1

    def _bump(self, key):
        value, freq, expires = self._vals[key]
        del self._freqs[freq][key]
        if not self._freqs[freq]:
            del self._freqs[freq]
            if self._min_freq == freq:
                self._min_freq = freq + 1
        self._vals[key] = (value, freq + 1, expires)
        self._freqs[freq + 1][key] = None

    def _remove(self, key):
        _, freq, _ = self._vals.pop(key)
        del self._freqs[freq][key]
        if not self._freqs[freq]:
            del self._freqs[freq]

    def _evict(self):
        if self._min_freq not in self._freqs:    # stale after an expiry removal
            self._min_freq = min(self._freqs)
        key, _ = self._freqs[self._min_freq].popitem(last=False)
        if not self._freqs[self._min_freq]:
            del self._freqs[self._min_freq]
        del self._vals[key]

now = [0.0]
cache = LFUCache(2, clock=lambda: now[0])
cache.put("a", 1); cache.put("b", 2)
assert cache.get("a") == 1           # a: freq 2, b: freq 1
cache.put("c", 3)                    # evicts b (lowest freq)
assert cache.get("b") is None and cache.get("c") == 3
# a: 2, c: 2 -> tie, evict least recent at freq 2 = a
cache.put("d", 4, ttl=5)
assert cache.get("a") is None
now[0] = 6
assert cache.get("d") is None        # expired
assert cache.get("c") == 3
```

**Follow-ups**
- *Thread-safe?* One lock around every public method — even `get` mutates recency, so a read-write lock doesn't help. Higher throughput: a **segmented cache** (N independent caches by `hash(key) % N`, each with its own lock). See the [concurrency part](#55-problem-thread-safe-lru-cache).
- *LRU vs LFU?* LRU adapts quickly to changing access patterns; LFU protects consistently popular items but old popular items can stick around → age/decay frequencies.
- *Write-back to a DB?* Mark entries dirty; on eviction flush dirty values (decorate the cache with a `on_evict` callback).
- *Distributed?* Consistent hashing across nodes (HLD).

#### What real caches do instead of exact LRU

- **Redis** doesn't keep a global linked list — that would cost memory per key across millions of keys. Its LRU is **approximate**: on eviction it samples a few keys and evicts the best candidate among them (and keeps a small pool of good candidates across samples). Since Redis 4.0 there's also an LFU mode that stores a tiny **logarithmic counter** per key (a probabilistic counter that grows slowly, so 8 bits can represent very large access counts) and **decays** it over time, so yesterday's popular keys eventually become evictable.
- **The Linux page cache** classically uses two lists — "active" and "inactive" — so a one-time scan of a big file doesn't flush the pages that are genuinely hot. Since Linux 6.1, the multi-generational LRU (MGLRU) can replace them with several "generations" of pages, aged in the background, which tracks hotness more precisely.
- **Caffeine** (Java) uses W-TinyLFU: a small "window" LRU for new items plus a frequency sketch to decide whether a newcomer deserves to evict an established entry.

The common thread: exact LRU is easy to code and expensive to run at scale, so production caches approximate it — and add frequency information to resist scans.

---

## 36. Problem: Key-value store with transactions

**How often asked:** common · **Builds on:** [3. OOP in depth](#3-oop-in-depth), [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)

**Classic machine-coding ask:** `set`, `get`, `delete`, `begin`, `commit`, `rollback`, with **nested** transactions.

**Key idea:** an **undo log** per transaction level. On the first write to a key inside a level, record its old value (or "missing"). Rollback restores those values. Commit merges the level's undo log into its parent (keeping the parent's older value if it already has one), so a later rollback of the parent still restores correctly.

```python
_MISSING = object()

class TransactionalKV:
    def __init__(self):
        self._data: dict = {}
        self._undo: list[dict] = []          # one undo log per open transaction

    def get(self, key):
        return self._data.get(key)

    def set(self, key, value) -> None:
        self._remember(key)
        self._data[key] = value

    def delete(self, key) -> None:
        if key in self._data:
            self._remember(key)
            del self._data[key]

    def begin(self) -> None:
        self._undo.append({})

    def rollback(self) -> None:
        if not self._undo:
            raise RuntimeError("no transaction")
        for key, old in self._undo.pop().items():
            if old is _MISSING:
                self._data.pop(key, None)
            else:
                self._data[key] = old

    def commit(self) -> None:
        if not self._undo:
            raise RuntimeError("no transaction")
        top = self._undo.pop()
        if self._undo:                       # nested: parent must still be able to undo
            parent = self._undo[-1]
            for key, old in top.items():
                parent.setdefault(key, old)

    def _remember(self, key):
        if self._undo and key not in self._undo[-1]:
            self._undo[-1][key] = self._data.get(key, _MISSING)

kv = TransactionalKV()
kv.set("a", 1)
kv.begin()
kv.set("a", 2); kv.set("b", 3)
kv.begin()
kv.delete("a")
kv.rollback()                       # undo inner only
assert kv.get("a") == 2 and kv.get("b") == 3
kv.begin()
kv.set("a", 5)
kv.commit()                         # merged into outer
kv.rollback()                       # undo outer, including merged change
assert kv.get("a") == 1 and kv.get("b") is None
```

**Follow-ups**
- *`count(value)` in O(1)?* Maintain `value → count` alongside `_data`; update it in `set`/`delete` and rollback.
- *Concurrent clients with isolation?* Each client gets its own transaction buffer (writes kept private until commit) + optimistic concurrency check at commit (key versions). That's MVCC-lite.
- *Undo log vs copy-on-begin?* Copying the whole dict per `begin` is O(n); the undo log is O(keys touched).

#### Not every "transaction" rolls back

Redis has `MULTI`/`EXEC` "transactions", and interviewers sometimes ask how they differ from this design. Redis queues the commands and executes them together without interleaving other clients — but if one command fails at runtime (say, incrementing a string), the others still apply; there's **no rollback**. Isolation without atomicity-on-error. Databases with undo logs (like this chapter's) or MVCC provide real rollback. Knowing this difference is a good sign you understand what "transaction" actually promises.

---

## 37. Problem: Task scheduler / job scheduler

**How often asked:** common · **Builds on:** [3. OOP in depth](#3-oop-in-depth), [7. Modeling techniques that come up in every problem](#7-modeling-techniques-that-come-up-in-every-problem)

**Clarify:** schedule a task to run after a delay or at a fixed interval; cancel; retry failed tasks a limited number of times; order by run time; ties by insertion order.

**Design**
- Min-heap of `(run_at, seq, task_id)`; `seq` breaks ties deterministically.
- `Task(id, fn, interval, retries_left, retry_delay)`.
- **Cancel is lazy:** remove from the task map; skip heap entries whose task is gone.
- This version is driven by a **simulated clock** (`run_until(t)`) → fully deterministic. The real-thread version (condition variable + worker thread) is in the [concurrency part](#58-problem-delayed-task-scheduler).

```python
import heapq
from dataclasses import dataclass
from itertools import count
from typing import Callable

@dataclass
class Task:
    id: int
    fn: Callable[[], None]
    interval: float | None
    retries_left: int
    retry_delay: float

class Scheduler:
    def __init__(self):
        self.now = 0.0
        self._heap: list[tuple[float, int, int]] = []
        self._tasks: dict[int, Task] = {}
        self._ids, self._seq = count(1), count()
        self.failures: list[tuple[int, str]] = []

    def schedule(self, fn, delay: float = 0, interval: float | None = None,
                 retries: int = 0, retry_delay: float = 1.0) -> int:
        if delay < 0 or (interval is not None and interval <= 0):
            raise ValueError("invalid timing")
        task = Task(next(self._ids), fn, interval, retries, retry_delay)
        self._tasks[task.id] = task
        self._push(self.now + delay, task.id)
        return task.id

    def cancel(self, task_id: int) -> bool:
        return self._tasks.pop(task_id, None) is not None

    def _push(self, run_at: float, task_id: int) -> None:
        heapq.heappush(self._heap, (run_at, next(self._seq), task_id))

    def run_until(self, t: float) -> None:
        while self._heap and self._heap[0][0] <= t:
            run_at, _, task_id = heapq.heappop(self._heap)
            task = self._tasks.get(task_id)
            if task is None:
                continue                                    # cancelled
            self.now = run_at
            try:
                task.fn()
            except Exception as exc:
                if task.retries_left > 0:
                    task.retries_left -= 1
                    self._push(run_at + task.retry_delay, task_id)
                    continue
                self.failures.append((task_id, str(exc)))
            if task.interval is not None and task_id in self._tasks:
                self._push(run_at + task.interval, task_id)
            elif task.interval is None:
                self._tasks.pop(task_id, None)
        self.now = t

log = []
s = Scheduler()
s.schedule(lambda: log.append(("b", s.now)), delay=5)
s.schedule(lambda: log.append(("a", s.now)), delay=2)
c = s.schedule(lambda: log.append(("c", s.now)), delay=3)
tick = s.schedule(lambda: log.append(("tick", s.now)), delay=0, interval=4)
s.cancel(c)
attempts = []
def flaky():
    attempts.append(s.now)
    if len(attempts) < 3:
        raise RuntimeError("boom")
s.schedule(flaky, delay=1, retries=2, retry_delay=0.5)
s.schedule(lambda: 1 / 0, delay=6)
s.run_until(9)
assert log == [("tick", 0), ("a", 2), ("tick", 4), ("b", 5), ("tick", 8)]
assert attempts == [1, 1.5, 2.0]                    # 2 retries, then success
assert len(s.failures) == 1 and "division" in s.failures[0][1]
s.cancel(tick)
s.run_until(20)
assert log[-1] == ("tick", 8)
```

**Follow-ups:** cron expressions (compute next run with a parser) · priorities (add priority to the heap key) · persistence and multiple nodes (DB table + `SELECT ... FOR UPDATE SKIP LOCKED`, see HLD job scheduler) · long tasks shouldn't block others (hand off to a worker pool).

#### Heaps, timer wheels and hierarchical timer wheels

A min-heap is the textbook structure: O(log n) to add or pop, and it's what Java's `ScheduledThreadPoolExecutor` uses (its `DelayedWorkQueue` is a heap). When you have **millions** of timers that are mostly cancelled before they fire — request timeouts are the classic case — a heap's log n per operation adds up.

A **timing wheel** is a circular array of buckets, one per tick. Adding a timer drops it into the bucket `ticks_from_now % size` in O(1); each tick processes one bucket. **Hierarchical** wheels (seconds wheel, minutes wheel, …) handle long delays without a giant array. The Linux kernel uses a timer wheel for kernel timers, and Kafka uses a hierarchical timing wheel for its "purgatory" of delayed requests (e.g., produce requests waiting for replicas to acknowledge). If an interviewer asks "what if there are 10 million timers?", this is the answer.

---

## 38. Follow-up questions in every LLD round

| Question | Short answer |
|---|---|
| "How would you add a new X type?" | Point to the interface/strategy: one new class, no edits to existing logic (OCP). |
| "What if two users do this at the same time?" | Name the check-then-act race; lock the critical section (or per-entity locks); in a DB, conditional update / unique constraint. |
| "How would you persist this?" | Repositories behind an interface; swap in-memory for a DB-backed one; transactions where invariants span rows. |
| "How do you test it?" | Inject clock and strategies; unit test each strategy; test invalid transitions and that failed operations leave state unchanged. |
| "Why not inheritance here?" | The variation is a policy, not a type with different identity; composition avoids subclass explosion (`WeekendTruckLot`…). |
| "Why this pattern?" | Name the problem it solves in *this* design and the change it makes cheap. |
| "What's the complexity?" | Give it per operation; mention the data structure that makes the hot path fast (heap for free spots, hashmap + list for LRU, bisect for intervals). |
| "How would it scale to many servers?" | In-process locks stop working; move state to a DB/Redis, use conditional updates, and idempotency for retries. |

---

## 39. Operating systems: processes, threads and scheduling

Concurrency questions sit on top of operating-system concepts. Campus and SDE1 interviews ask OS directly; SDE2 interviews use it to judge whether your concurrency and performance answers have a solid base. This chapter and the next two cover what's worth knowing — deep where interviews go, brief elsewhere.

### 39.1 Programs, processes and threads

- A **program** is a file on disk. A **process** is a running instance of it: its own virtual address space (code, data, heap, stack), open files, and at least one thread.
- A **thread** is an execution path inside a process: its own stack and registers, sharing the process's memory and files with sibling threads.
- The OS tracks each process in a **PCB** (process control block): process ID, state, registers, memory mappings, open files, scheduling info.

```text
Process A (own memory)                 Process B (own memory)
┌───────────────────────────┐          ┌──────────────────┐
│ code │ heap │ open files  │          │ code │ heap │ …  │
│ thread 1: stack + regs    │          │ thread 1         │
│ thread 2: stack + regs    │          └──────────────────┘
└───────────────────────────┘
threads in A share A's heap → need locks;   A and B share nothing by default → need IPC
```

| | Process | Thread |
|---|---|---|
| Memory | Isolated | Shared with siblings |
| Create / switch cost | Higher (new address space) | Lower |
| A crash | Kills only that process | Kills the whole process |
| Communication | IPC (pipes, sockets, shared memory) | Shared variables (+ locks) |

**Process states:**

```text
new ──► ready ──(scheduled)──► running ──(exits)──► terminated
          ▲        (preempted)  │
          └─────────────────────┤
          ▲                     │ waits for I/O, lock, sleep
          └──── waiting ◄───────┘   (I/O done → back to ready)
```

**Creating processes (Unix):** `fork()` makes a near-copy of the current process (memory shared **copy-on-write** — pages are only copied when one side writes); `exec()` replaces the copy's program with a new one. Shells run commands with fork + exec.

**User mode vs kernel mode:** your code runs in user mode and can't touch hardware directly. Reading a file, opening a socket or allocating memory needs a **system call**, which switches into the kernel. System calls cost far more than function calls → batching I/O (bigger reads/writes) matters for performance.

**Context switch:** saving one thread's registers and loading another's (plus switching the address space for processes). Microseconds each, plus cache and TLB misses afterwards — which is why thousands of busy threads waste CPU, and why event loops scale better for I/O-heavy servers.

**Zombie and orphan processes** (*asked in campus interviews*): a **zombie** has exited but its parent hasn't read its exit status (`wait()`), so its PCB entry lingers. An **orphan**'s parent died first; it's adopted by `init`/`systemd`, which reaps it.

### 39.2 CPU scheduling

*Asked in campus/OS rounds; for SDE2 know the trade-offs, not formulas.*

The scheduler decides which **ready** thread runs next. Goals conflict: throughput, low waiting time, fast response for interactive tasks, fairness.

| Algorithm | Rule | Strength | Weakness |
|---|---|---|---|
| FCFS | First come, first served | Simple | Short jobs stuck behind long ones (**convoy effect**) |
| SJF / SRTF | Shortest (remaining) job first | Minimal average waiting time | Needs to know job length; long jobs can **starve** |
| Round robin | Each job gets a time slice (quantum), then goes to the back | Fair, good response time | Slice too small → too many context switches; too big → becomes FCFS |
| Priority | Highest priority first | Expresses importance | Starvation → fix with **aging** (priority rises while waiting) |
| MLFQ | Several queues; jobs that use their whole slice drop to lower priority | Interactive (short-burst) jobs stay responsive without knowing lengths | Tuning |

Real kernels: Linux used **CFS** (Completely Fair Scheduler: picks the task with the least virtual runtime, kept in a red-black tree) from 2007 until Linux 6.6 (2023) switched to **EEVDF**, which keeps CFS's fairness but handles latency-sensitive tasks better.

```python
def fcfs(jobs):
    t, waits = 0, {}
    for name, burst in jobs:
        waits[name] = t
        t += burst
    return waits

def sjf(jobs):                                      # all jobs arrive at time 0
    return fcfs(sorted(jobs, key=lambda j: j[1]))

def round_robin(jobs, quantum):
    remaining = dict(jobs)
    queue, t, finish = [name for name, _ in jobs], 0, {}
    while queue:
        name = queue.pop(0)
        run = min(quantum, remaining[name])
        t += run
        remaining[name] -= run
        if remaining[name]:
            queue.append(name)
        else:
            finish[name] = t
    # waiting = finish - burst
    return {name: finish[name] - burst for name, burst in jobs}

jobs = [("report", 24), ("click", 3), ("keypress", 3)]
avg = lambda w: sum(w.values()) / len(w)
assert avg(fcfs(jobs)) == 17                           # 0, 24, 27 — convoy effect
assert avg(sjf(jobs)) == 3                             # short jobs first: 6, 0, 3
rr = round_robin(jobs, quantum=4)
# interactive jobs answered quickly
assert rr["click"] == 4 and rr["keypress"] == 7
assert avg(rr) < avg(fcfs(jobs))
```

### 39.3 Interrupts, system calls and priority inversion

*Asked occasionally in OS rounds; priority inversion is a favourite "tell me a story" question.*

**Interrupts.** Hardware signals the CPU that something happened — a network packet arrived, a disk read finished, a timer fired. The CPU pauses what it's doing, runs the kernel's handler, then resumes (or switches to another thread). The **timer interrupt** is what makes preemptive scheduling possible: every few milliseconds the kernel gets control back and can decide whether the running thread has had its turn.

**System calls.** User programs can't touch hardware or other processes' memory directly. To read a file, the program executes a special instruction that **traps into the kernel**:

```text
user mode:   read(fd, buf, 4096)
               │  save registers, switch to kernel mode
kernel mode:   check fd and permissions → copy from page cache (or start disk I/O
               and block this thread) → copy into buf
               │  switch back to user mode, restore registers
user mode:   read() returns the byte count
```

A system call costs far more than a function call (mode switch, cache effects; often hundreds of nanoseconds or more), which is why fast I/O code batches work: one `read` of 64 KB instead of 16 reads of 4 KB, buffered writers, `sendfile` for zero-copy, and newer interfaces like Linux's `io_uring` that submit many operations per call.

**Priority inversion.** A high-priority thread waits for a lock held by a low-priority thread. A medium-priority thread, which needs no lock, preempts the low one — so the high-priority thread is effectively blocked by the medium one, possibly for a long time.

```text
Low:    takes lock ──── preempted by Medium ………………………… releases lock
Medium:                 runs and runs ─────────────►
High:          wants lock ── waits ─────────────────────────────── gets it (too late)
```

The famous case is **Mars Pathfinder (1997)**: on Mars, the lander kept resetting itself. A high-priority bus-management task waited on a mutex held by a low-priority meteorological task, medium-priority communication tasks kept running, and a watchdog saw the high-priority task miss its deadline and reset the system. Engineers reproduced it on Earth and fixed it by remotely turning on **priority inheritance** for that mutex in the VxWorks operating system: while a low-priority thread holds a lock a high-priority thread wants, it temporarily runs at the high priority, so medium tasks can't get in the way.

### 39.4 Deadlock handling in operating systems

*Campus favourite. The four conditions and prevention by lock ordering are covered in [43. Concurrency concepts](#43-concurrency-concepts); here's the classical OS view.*

Four strategies:
1. **Prevention:** make one of the four conditions impossible (e.g., always acquire resources in a fixed order → no circular wait).
2. **Avoidance:** before granting a request, check the system stays in a **safe state** — **Banker's algorithm**.
3. **Detection and recovery:** let deadlocks happen, detect cycles in the wait-for graph, then kill/roll back a victim (what databases do).
4. **Ignore it** ("ostrich algorithm"): most general-purpose OSes do this for application locks — it's the programmer's job.

**Banker's algorithm:** each process declares its maximum need. A state is **safe** if there's some order in which every process can get its remaining need from what's available, finish, and release everything.

```python
def is_safe(available, allocation, maximum):
    need = [[m - a for m, a in zip(mx, al)] for mx, al in zip(maximum, allocation)]
    work, finished, order = list(available), [False] * len(allocation), []
    while len(order) < len(allocation):
        progressed = False
        for i, done in enumerate(finished):
            if not done and all(n <= w for n, w in zip(need[i], work)):
                # process i finishes, releases
                work = [w + a for w, a in zip(work, allocation[i])]
                finished[i], progressed = True, True
                order.append(i)
        if not progressed:
            return False, order
    return True, order

# classic textbook example: 5 processes, 3 resource types
allocation = [[0, 1, 0], [2, 0, 0], [3, 0, 2], [2, 1, 1], [0, 0, 2]]
maximum    = [[7, 5, 3], [3, 2, 2], [9, 0, 2], [2, 2, 2], [4, 3, 3]]
safe, order = is_safe([3, 3, 2], allocation, maximum)
assert safe and order == [1, 3, 4, 0, 2]
assert not is_safe([0, 0, 0], allocation, maximum)[0]
```
In practice, Banker's algorithm is rarely used — processes can't declare maximum needs in advance. Real systems prevent (lock ordering), detect (databases), or time out.

---

## 40. Operating systems: memory management

### 40.1 Virtual memory and paging

Every process believes it has a large, private, contiguous memory starting at address 0. That's **virtual memory**. The OS and CPU translate virtual addresses to physical RAM in fixed-size chunks called **pages** (usually 4 KB).

```text
Process A virtual pages          Page table (per process)        Physical RAM frames
  page 0  ─────────────────────► frame 7                          [frame 0][1][2]…[7: A's page 0]
  page 1  ─────────────────────► frame 2
  page 2  ─────────────────────► "not in RAM" (on disk / not yet loaded) → page fault
```

Why this design:
- **Isolation:** A can't read B's memory — their page tables point to different frames.
- **Overcommit:** programs can be bigger than RAM; untouched pages never need a frame (**demand paging**).
- **Sharing:** read-only pages (shared libraries, `fork`'s copy-on-write pages) map to the same frame in many processes.

**TLB (translation lookaside buffer):** a small CPU cache of recent page translations. Every memory access needs a translation; the TLB makes most of them free. A TLB miss walks the page table (slower). Context switches between processes can flush it — another hidden cost of switching.

**Page fault:** the process touches a page that isn't in RAM. The CPU traps into the kernel, which loads the page (from disk or by zero-filling), updates the page table, and resumes the instruction.
- *Minor fault:* page is in RAM already (e.g., shared) — cheap.
- *Major fault:* must read from disk — milliseconds, a disaster in a hot path.

**Thrashing:** the working set of all running processes doesn't fit in RAM, so the system spends its time swapping pages in and out instead of doing work. Symptoms: high disk I/O, CPU idle, everything slow. Fix: less memory per process, fewer processes, more RAM. Production servers often disable swap entirely and prefer the **OOM killer** stopping a process over slow-motion thrashing.

### 40.2 Page replacement

When RAM is full and a new page is needed, which page leaves? Same problem as cache eviction ([35. Problem: LRU cache and LFU cache (with TTL)](#35-problem-lru-cache-and-lfu-cache-with-ttl)).

| Policy | Evicts | Note |
|---|---|---|
| FIFO | The oldest loaded page | Simple; suffers **Belady's anomaly** (more frames can cause *more* faults) |
| Optimal (Belady's MIN) | The page used farthest in the future | Impossible in practice (needs the future); the benchmark |
| LRU | The least recently used | Great in practice; exact LRU is too expensive in hardware |
| Clock (second chance) | Circle the frames; skip pages with "referenced" bit set (clearing it) | Cheap LRU approximation real kernels use |

```python
from collections import OrderedDict, deque

def faults_fifo(refs, frames):
    mem, order, faults = set(), deque(), 0
    for p in refs:
        if p not in mem:
            faults += 1
            if len(mem) == frames:
                mem.remove(order.popleft())
            mem.add(p); order.append(p)
    return faults

def faults_lru(refs, frames):
    mem, faults = OrderedDict(), 0
    for p in refs:
        if p in mem:
            mem.move_to_end(p)
        else:
            faults += 1
            if len(mem) == frames:
                mem.popitem(last=False)
            mem[p] = True
    return faults

def faults_optimal(refs, frames):
    mem, faults = set(), 0
    for i, p in enumerate(refs):
        if p in mem:
            continue
        faults += 1
        if len(mem) == frames:
            future = refs[i + 1:]
            victim = max(mem, key=lambda q: future.index(q) if q in future else len(future) + 1)
            mem.remove(victim)
        mem.add(p)
    return faults

refs = [1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5]
# Belady's anomaly: more frames, more faults
assert (faults_fifo(refs, 3), faults_fifo(refs, 4)) == (9, 10)
# LRU never has the anomaly
assert faults_lru(refs, 4) <= faults_lru(refs, 3)
# optimal is the lower bound
assert faults_optimal(refs, 3) <= faults_lru(refs, 3)
```

### 40.3 Stack, heap, allocation and garbage collection

- **Stack:** per thread; function frames (local variables, return addresses) pushed and popped automatically. Fast, limited size → deep recursion causes **stack overflow**.
- **Heap:** objects with dynamic lifetimes. Allocated with `malloc`/`new` (or implicitly in Python/Java); must be freed manually (C/C++) or by a garbage collector.
- **Memory leak:** memory that's no longer needed but still referenced (or never freed). In GC languages, the classic cause is an ever-growing cache or list, or listeners never unsubscribed.
- **Fragmentation:** *internal* — allocated block bigger than requested (wasted inside); *external* — enough total free memory, but not in one contiguous piece.

**Garbage collection approaches:**
- **Reference counting** (CPython's main mechanism): free an object as soon as its count hits zero. Immediate, but can't free **cycles** on its own — CPython adds a cycle-detecting collector.
- **Tracing GC** (Java, Go, .NET): periodically find everything reachable from roots, free the rest. Generational GCs exploit that most objects die young. Can cause **pause times**, which matter for latency-sensitive services.

```python
import gc
import weakref

class Node:
    def __init__(self): self.other = None

a, b = Node(), Node()
a.other, b.other = b, a                     # reference cycle
probe = weakref.ref(a)
# refcounts never reach zero because of the cycle…
del a, b
gc.collect()                                # …the cycle collector frees them
assert probe() is None
```

### 40.4 Stories from production: memory

- **Copy-on-write surprises with Redis:** Redis saves snapshots (RDB) by calling `fork()`; the child writes the snapshot while the parent keeps serving. Thanks to copy-on-write the fork is cheap — but every page the parent modifies during the snapshot gets copied. On a write-heavy instance, memory use can approach double, and if the box runs out, the OOM killer strikes. That's why Redis docs recommend leaving memory headroom and why some deployments snapshot from replicas.
- **GC pauses driving a language change:** Discord's 2020 post "Why Discord is switching from Go to Rust" describes a service (tracking which messages each user has read) whose latency spiked every two minutes: Go's runtime forced a garbage collection at least that often, and each one had to scan a very large in-memory LRU cache. Rewriting it in Rust (no GC, memory freed deterministically) removed the spikes. The general lesson: large, long-lived in-memory caches and tracing GCs can fight each other.

---

## 41. Operating systems: file systems, I/O and IPC

### 41.1 Files, inodes and links

*Asked occasionally (hard vs soft links is a campus favourite).*

- On Unix file systems, a file's metadata (size, owner, permissions, timestamps, pointers to data blocks) lives in an **inode**. A **directory** is just a table mapping names → inode numbers.
- **Hard link:** another name pointing to the **same inode**. Deleting one name doesn't delete the data until the last link is gone. Can't span file systems or point to directories.
- **Soft (symbolic) link:** a small file containing a **path**. Breaks if the target moves; can point anywhere, including directories.
- **Journaling** (ext4, XFS, NTFS): metadata changes are written to a journal first, so a crash mid-update doesn't corrupt the file system — the same write-ahead idea databases use ([10.2 How a write is made durable: WAL](hld-interview-guide.md#102-how-a-write-is-made-durable-wal)).

### 41.2 The page cache and durability

Reads and writes normally go through the kernel's **page cache** in RAM:
- `write()` returning successfully means the data is in the page cache — **not** that it's on disk. A power cut can lose it.
- `fsync()` forces it to stable storage. Databases call it at commit (that's the durability in ACID); it's slow (milliseconds on SSDs, more on network disks), which is why databases batch commits (group commit).
- Reading a file twice is fast the second time — it's served from the page cache. Kafka deliberately relies on this: it writes to the file system and lets the OS cache recent data, so consumers reading near the tail are served from memory.

```python
import os
import tempfile

path = os.path.join(tempfile.mkdtemp(), "ledger.log")
with open(path, "ab") as f:
    f.write(b"txn 42 committed\n")
    f.flush()                    # Python buffer → kernel page cache
    os.fsync(f.fileno())         # page cache → disk: now it survives a power cut
assert open(path, "rb").read() == b"txn 42 committed\n"
```

### 41.3 I/O models: blocking, non-blocking, multiplexing, async

*Asked often in backend interviews — it's the foundation of how servers handle many connections.*

| Model | What happens | Used by |
|---|---|---|
| **Blocking** | `recv()` puts the thread to sleep until data arrives | Thread-per-connection servers |
| **Non-blocking** | `recv()` returns immediately with "nothing yet" (`EAGAIN`) — you'd have to poll in a loop | Building block for the next one |
| **I/O multiplexing** | One call waits on **many** sockets: "tell me which ones are ready" (`select`, `poll`, Linux **`epoll`**, BSD/macOS **`kqueue`**) | Nginx, Redis, Node.js (libuv), Python asyncio, Netty |
| **Async I/O** | Submit the operation; the kernel completes it and notifies you (Linux **`io_uring`**, Windows IOCP) | High-performance storage and networking |

**Why `epoll` beat `select`:** `select` hands the kernel the whole list of sockets on every call (O(n) per call, and a 1,024-descriptor limit). `epoll` registers interest once and returns only the ready sockets — cost scales with **activity**, not with the number of connections. That change is what made one machine able to hold hundreds of thousands of mostly-idle connections (chat, push notifications).

An event loop with Python's `selectors` (which uses `epoll`/`kqueue` underneath):

```python
import selectors
import socket

sel = selectors.DefaultSelector()            # epoll on Linux, kqueue on macOS
pairs = [socket.socketpair() for _ in range(3)]
for server_side, _client in pairs:
    server_side.setblocking(False)
    sel.register(server_side, selectors.EVENT_READ)

pairs[0][1].sendall(b"ping-0")               # only two "clients" send anything
pairs[2][1].sendall(b"ping-2")

handled = []
for key, _mask in sel.select(timeout=1):     # ONE wait call covers every connection
    data = key.fileobj.recv(1024)
    key.fileobj.sendall(data.upper())         # echo back
    handled.append(data)

# idle connection 1 cost nothing
assert sorted(handled) == [b"ping-0", b"ping-2"]
assert pairs[0][1].recv(1024) == b"PING-0"
for a, b in pairs:
    sel.unregister(a); a.close(); b.close()
```

**Zero-copy (`sendfile`):** sending a file over a socket normally copies disk → kernel → user buffer → kernel socket buffer. `sendfile()` lets the kernel move it directly. Kafka uses this to serve consumers efficiently, and web servers use it for static files.

### 41.4 Inter-process communication (IPC)

*Know the options and when each fits; details rarely asked.*

| Mechanism | What | Typical use |
|---|---|---|
| **Pipe** | One-way byte stream between related processes (`ls \| grep`) | Shell pipelines, parent ↔ child |
| **Named pipe (FIFO)** | A pipe with a file-system name; unrelated processes can use it | Simple local producer/consumer |
| **Unix domain socket** | Socket API on one machine, no network stack overhead | Postgres/Docker/local daemons, sidecars |
| **TCP socket** | Communication across machines | Everything distributed |
| **Shared memory** | Same physical pages mapped into several processes — fastest, needs your own synchronization | High-performance data exchange, Python `multiprocessing.shared_memory` |
| **Message queue (OS-level)** | Kernel-managed queue of messages | Legacy/embedded; in distributed systems you use a broker instead |
| **Signal** | A tiny notification (`SIGTERM`, `SIGKILL`, `SIGINT`) | Shutdown, reload config |

**Signals and graceful shutdown** (asked in backend interviews): Kubernetes and most process managers send `SIGTERM`, wait a grace period (30 s by default in Kubernetes), then send `SIGKILL` (which can't be caught). Your server should handle `SIGTERM`: stop accepting new work, finish in-flight requests, close connections, exit.

```python
import os

# one-way channel (parent → child in real use)
r, w = os.pipe()
os.write(w, b"job:42\n")
os.close(w)
assert os.read(r, 100) == b"job:42\n"
os.close(r)
```

### 41.5 Memory-mapped files (mmap)

*Asked occasionally, usually as "how does a database or Kafka read files fast?" or "what's mmap?".*

`mmap` maps a file into the process's address space. After that, reading `data[1000]` is a plain memory access: if the page isn't in memory yet, a **page fault** makes the kernel load it from the page cache or disk. Writes go to the page in memory and the kernel writes them back later (or when you call `msync`).

```python
import mmap
import os
import tempfile

path = os.path.join(tempfile.mkdtemp(), "data.bin")
with open(path, "wb") as f:
    f.write(b"hello world")

with open(path, "r+b") as f, mmap.mmap(f.fileno(), 0) as data:
    assert data[:5] == b"hello"                 # a read is a memory access
    data[:5] = b"HELLO"                         # a write changes the page in memory
    # msync: write the dirty page back to the file
    data.flush()

with open(path, "rb") as f:
    assert f.read() == b"HELLO world"
```

**Why use it**
- No copy from the kernel into your own buffer — you read the page cache directly — and no `read()` system call per access.
- Random access into a big file looks like indexing an array; the OS loads only the pages you touch.
- Two processes mapping the same file with `MAP_SHARED` see each other's writes: a fast form of shared memory.
- Real uses: Kafka's offset index files, LMDB, SQLite (optionally), and many search-index formats.

**Why databases are wary of it**
- You don't control **when** dirty pages are written back or **which** pages get evicted — the OS decides, which makes crash-safe transactions hard to reason about.
- I/O errors show up as a signal (SIGBUS) on a memory access instead of an error code from `read()`.
- The 2022 paper "Are You Sure You Want to Use MMAP in Your Database Management System?" (Crotty, Leis, Pavlo) argues these problems are why most serious databases manage their own buffer pool instead. MongoDB's original storage engine was mmap-based and was replaced by WiredTiger, which has its own cache.

### 41.6 How servers evolved: Apache, Nginx and the event loop

In the early 2000s the dominant web server, Apache, typically handled each connection with its own process or thread. That works until thousands of slow clients (mobile networks, keep-alive connections) tie up thousands of processes, each with its own memory — the **C10K problem**. Nginx (first released in 2004) took the event-driven approach: a few worker processes, each running an `epoll`/`kqueue` loop over thousands of non-blocking connections. Memory per connection dropped dramatically, which is why Nginx became the default reverse proxy in front of application servers. Node.js (2009) brought the same model to application code, and Python's asyncio, Java's Netty and Go's runtime scheduler all build on it.

---

## 42. Concurrency: what gets asked

Threads, locks and async inside one process: what LLD and machine-coding rounds ask when they say *"now make it thread-safe"*. Coordination **across** processes and servers (DB locks, distributed locks, idempotency, server concurrency models) is in the HLD guide: [6.9](hld-interview-guide.md#69-how-servers-handle-concurrent-requests) and [17.13](hld-interview-guide.md#1713-coordinating-concurrent-work-across-servers).

- **Standalone concurrency round:** rare (some fintech/infra teams, Uber, Microsoft teams, Indian product companies).
- **Much more common:** inside an LLD/machine-coding round → *"Now make it thread-safe"* or *"What if two users book the same seat?"*
- **Inside HLD:** the same race, but across servers → DB conditional updates, distributed locks, idempotency.
- **Language questions:** GIL, `threading` vs `multiprocessing` vs `asyncio` (Python); `synchronized`/`volatile`/`ConcurrentHashMap` (Java).

**SDE2 bar:** spot the race, name the invariant, pick the right primitive, keep critical sections small, avoid deadlock, and know where in-process locks stop working (multiple processes/servers).

---

## 43. Concurrency concepts

### 43.1 Processes, threads, coroutines, and the GIL

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

### 43.2 Kernel threads, green threads and virtual threads

*Comes up as "what's a goroutine?" or "what are Java virtual threads?" — usually in Java/Go-heavy companies.*

Who schedules a "thread" decides how cheap it is:

| Model | Scheduled by | Cost per thread | Examples |
|---|---|---|---|
| **1:1 kernel threads** | The OS | ~1 MB stack reserved, a kernel object, µs-level switches | pthreads, Java platform threads, Python `threading` |
| **N:1 green threads** | The language runtime, on one OS thread | Tiny | Early Java (1.1), Python `asyncio` tasks (cooperative) |
| **M:N** | Runtime multiplexes many user threads onto a few OS threads | A few KB | Go goroutines, Java virtual threads, Erlang processes |

- **Goroutines** start with a small stack that grows as needed; the Go runtime parks a goroutine when it blocks on a channel or network I/O and runs another on the same OS thread. A server can run hundreds of thousands of them.
- **Java virtual threads** (Project Loom, standard since Java 21 in 2023) work the same way: a virtual thread runs on a "carrier" platform thread, and blocking I/O unmounts it so the carrier can run another. You write plain blocking code, and get the scalability of async code. One historical gotcha: blocking inside a `synchronized` block used to **pin** the virtual thread to its carrier; Java 24 removed most of that pinning.
- **Python asyncio** gives similar scalability for I/O, but switching happens only at `await`, and everything runs on one OS thread — a CPU-heavy function stalls all tasks ([43.8 asyncio concurrency](#438-asyncio-concurrency)).

In short: M:N threads make "one thread per request" cheap again, but they don't make CPU work faster, and locks still behave like locks.

### 43.3 Race conditions and critical sections

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

### 43.4 Synchronization primitives

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

### 43.5 Deadlock, livelock, starvation

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

### 43.6 Pessimistic vs optimistic concurrency

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

### 43.7 Thread pools and futures

- Creating a thread per task is expensive and unbounded → use a **pool**: N worker threads pull tasks from a queue.
- `concurrent.futures.ThreadPoolExecutor` (I/O) / `ProcessPoolExecutor` (CPU).
- `submit()` returns a **Future**: `result(timeout)`, `exception()`, `cancel()`, `add_done_callback()`.
- `as_completed(futures)` yields futures as they finish; `wait(..., FIRST_COMPLETED)` for event loops over results.
- Size: I/O-bound → more threads than cores (bounded by downstream limits); CPU-bound → ≈ number of cores (processes).
- **Bound the queue** too, or a burst of tasks will use unbounded memory (backpressure).

### 43.8 asyncio concurrency

Async web frameworks (FastAPI, aiohttp, Node.js) all work this way, so expect questions on it.
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

### 43.9 Python ↔ Java cheat sheet

| Concept | Python | Java |
|---|---|---|
| Mutex | `Lock`, `with lock:` | `synchronized`, `ReentrantLock` |
| Reentrant | `RLock` | `synchronized` / `ReentrantLock` (both reentrant) |
| Wait/notify | `Condition.wait/notify` | `Object.wait/notify`, `Condition.await/signal` |
| Semaphore | `Semaphore` | `Semaphore` |
| Read-write lock | none built in (see [chapter 63](#63-problem-read-write-lock-from-scratch)) | `ReentrantReadWriteLock`, `StampedLock` |
| Latch / barrier | `Event`, `Barrier` | `CountDownLatch`, `CyclicBarrier` |
| Blocking queue | `queue.Queue` | `ArrayBlockingQueue`, `LinkedBlockingQueue` |
| Concurrent map | `dict` + lock | `ConcurrentHashMap` (fine-grained, lock-free reads) |
| Atomic counter | lock or `itertools.count()` | `AtomicInteger`, `LongAdder` |
| Visibility | GIL (practically) | `volatile`, happens-before |
| Pool | `ThreadPoolExecutor` | `ExecutorService` |
| Future | `concurrent.futures.Future` | `Future`, `CompletableFuture` |

### 43.10 Java concurrency utilities interviewers ask about

*Common in Java-language rounds; the ideas transfer to any language.*

**`ConcurrentHashMap`** — a thread-safe map without one global lock.
- Java 8+: reads take no lock. Inserting into an **empty** bucket uses a single CAS; updating a non-empty bucket locks **only that bucket**. Buckets with many collisions turn into small balanced trees.
- `size()` uses striped counters, so it's an estimate while writes are in flight.
- Use `computeIfAbsent(key, k -> new Value())` for "create if missing" — it's atomic. `if (!map.containsKey(k)) map.put(k, v)` is a race.
- (Java 7 used "segments": a fixed number of locks, each guarding part of the table — the lock-striping idea.)

**`CompletableFuture`** — composing async results.
- `supplyAsync(task)` → `thenApply` (transform), `thenCompose` (chain another async call), `thenCombine` (join two), `allOf` (wait for many), `exceptionally` / `handle` (errors).
- Gotcha: without an explicit executor it runs on `ForkJoinPool.commonPool()`, which is sized for CPU work. Blocking I/O there starves everything else in the JVM using that pool → pass your own executor for I/O.

**Coordination helpers**

| Tool | What it does | Typical use |
|---|---|---|
| `CountDownLatch(n)` | Threads wait until `countDown()` has been called n times; **one-shot** | "Start serving when all 3 dependencies are ready" |
| `CyclicBarrier(n)` | n threads wait for each other, then all continue; **reusable**, optional action per round | Simulation steps, parallel phases |
| `Semaphore(n)` | At most n holders at once | Limit concurrent calls to a downstream service |
| `Phaser` | A barrier where parties can join and leave | Dynamic phased work |

**Counters:** `volatile` gives visibility but `count++` is still a race; `AtomicInteger` uses CAS (fine at low contention); `LongAdder` spreads increments over cells and sums on read (much faster when many threads increment the same counter — the sharded-counter idea from [41. Problem: Distributed counter (likes, views)](hld-interview-guide.md#41-problem-distributed-counter-likes-views)).

**`ReentrantLock` vs `synchronized`:** both are reentrant mutexes. `ReentrantLock` adds `tryLock(timeout)` (avoid deadlock by giving up), optional fairness, and several `Condition`s per lock (like the two conditions in a bounded queue).

The same tools in Python — a latch built from a condition, and `threading.Barrier` as the cyclic barrier:

```python
from threading import Barrier, Condition, Thread

class CountDownLatch:
    def __init__(self, count: int):
        self._count, self._cond = count, Condition()

    def count_down(self) -> None:
        with self._cond:
            self._count -= 1
            if self._count <= 0:
                self._cond.notify_all()

    def wait(self, timeout: float | None = None) -> bool:
        with self._cond:
            return self._cond.wait_for(lambda: self._count <= 0, timeout)

ready, latch = [], CountDownLatch(3)
for name in ("db", "cache", "queue"):
    Thread(target=lambda n=name: (ready.append(n), latch.count_down())).start()
assert latch.wait(2) and sorted(ready) == ["cache", "db", "queue"]

rounds = []
barrier = Barrier(3, action=lambda: rounds.append("step done"))   # like CyclicBarrier
workers = [Thread(target=lambda: [barrier.wait() for _ in range(2)]) for _ in range(3)]
for w in workers: w.start()
for w in workers: w.join(2)
# reused for two rounds
assert rounds == ["step done", "step done"]
```

### 43.11 Thread lifecycle, daemon threads, thread-local state

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
    # exceptions come back via the Future
    assert isinstance(fut.exception(), ZeroDivisionError)
```

### 43.12 Memory model, atomic operations, CAS, lock-free

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

### 43.13 Multiprocessing in Python

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

### 43.14 asyncio in depth

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

    # all tasks finish (or all cancelled) here
    async with asyncio.TaskGroup() as tg:
        tg.create_task(producer())
        for _ in range(3):
            tg.create_task(consumer())
    return sorted(out)

async def main():
    start = time.perf_counter()
    results = await asyncio.gather(*(fetch(i, 0.05) for i in range(10)))
    # concurrent, not 0.5 s
    assert results == list(range(10)) and time.perf_counter() - start < 0.3

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

    # off the event loop
    blocking_result = await asyncio.to_thread(lambda: sum(range(1000)))
    assert blocking_result == 499500
    assert await producer_consumer() == list(range(20))

asyncio.run(main())
```

### 43.15 Common concurrency bugs (checklist)

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

### 43.16 Testing and debugging concurrent code

- **Maximize overlap:** start threads behind a `Barrier` so they hit the critical section together.
- **Assert invariants,** not exact interleavings: totals conserved, no double booking, counts match.
- **Repeat** many iterations; run the test in a loop in CI.
- **Detect deadlocks:** `join(timeout)` then assert `not t.is_alive()`; dump all stacks with `faulthandler.dump_traceback_later(10)` or `py-spy dump --pid`.
- **Deterministic logic:** inject clocks and keep the core logic single-threaded and testable; add threads at the edges.
- **Tools:** Java `jstack` / thread dumps, ThreadSanitizer for C/C++/Go (`-race` in Go), lock contention profilers.

---

## 44. Problem: LC 1114 — Print in Order

**How often asked:** occasional · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives)

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

---

## 45. Problem: LC 1115 — Print FooBar Alternately

**How often asked:** occasional · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives)

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

---

## 46. Problem: LC 1116 — Print Zero Even Odd

**How often asked:** occasional · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives)

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

---

## 47. Problem: LC 1117 — Building H2O

**How often asked:** occasional · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives)

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

---

## 48. Problem: LC 1188 — Design Bounded Blocking Queue

**How often asked:** occasional · **Builds on:** [54. Problem: Bounded blocking queue / producer-consumer](#54-problem-bounded-blocking-queue--producer-consumer)

`enqueue` blocks when full, `dequeue` blocks when empty, `size()` returns the count.

This is exactly the queue built and tested in [54. Problem: Bounded blocking queue / producer-consumer](#54-problem-bounded-blocking-queue--producer-consumer): one lock and two conditions (`not_full`, `not_empty`), waiting in a loop on the condition. To submit it on LeetCode, rename `put` → `enqueue` and `get` → `dequeue`, add `size()` (return `len(self._items)` under the lock), and drop the timeout and `close()` parts, which LeetCode doesn't ask for.

The one mistake to avoid: waiting with `if` instead of `while` (or `wait_for`). After a wake-up, another thread may already have taken the item, so the condition must be checked again.

---

## 49. Problem: LC 1195 — Fizz Buzz Multithreaded

**How often asked:** occasional · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives)

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

---

## 50. Problem: LC 1226 — The Dining Philosophers

**How often asked:** occasional · **Builds on:** [43.5 Deadlock, livelock, starvation](#435-deadlock-livelock-starvation)

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

Edsger Dijkstra posed this problem in 1965 (as an exam exercise about computers competing for tape drives); Tony Hoare later gave it the philosophers-and-forks framing. It survives because it shows, in five lines of setup, how circular waiting turns into deadlock — and why "everyone grabs left first" is the bug.

---

## 51. Problem: LC 1242 — Web Crawler Multithreaded

**How often asked:** common · **Builds on:** [43.7 Thread pools and futures](#437-thread-pools-and-futures), [43.3 Race conditions and critical sections](#433-race-conditions-and-critical-sections)

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

**Follow-ups:** politeness per host (semaphore per host) · distributed crawl ([HLD chapter 40: Web crawler](hld-interview-guide.md#40-problem-web-crawler)) · why not let workers update `seen`? You can, with a lock, but single-owner state is simpler and race-free.

---

## 52. Problem: Print 1 to N with K threads in turn

**How often asked:** common (a favourite warm-up; also asked as "odd/even with two threads") · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives)

K threads must print 1, 2, 3 … N between them, strictly in turn: thread 0 prints 1, thread 1 prints 2, …, thread K−1 prints K, thread 0 prints K+1, and so on.

**Idea:** one shared counter, one condition variable. Each thread waits until "the next number is mine" (`(next − 1) % K == my_id`) or everything is done, prints, increments, and wakes the others.

```python
from threading import Condition, Thread

def print_in_turn(n: int, k: int) -> list[tuple[int, int]]:
    out, cond, state = [], Condition(), {"next": 1}

    def worker(me: int) -> None:
        while True:
            with cond:
                cond.wait_for(lambda: state["next"] > n or (state["next"] - 1) % k == me)
                if state["next"] > n:
                    return
                out.append((me, state["next"]))
                state["next"] += 1
                cond.notify_all()

    threads = [Thread(target=worker, args=(i,)) for i in range(k)]
    for t in threads: t.start()
    for t in threads: t.join(5)
    return out

result = print_in_turn(10, 3)
assert [num for _, num in result] == list(range(1, 11))
assert all(me == (num - 1) % 3 for me, num in result)
# threads with nothing to print still exit
assert print_in_turn(1, 4) == [(0, 1)]
```

- **Why `notify_all`?** With one shared condition, `notify()` might wake the wrong thread, which goes back to sleep — and the right one never wakes (a lost wake-up). `notify_all` wakes everyone; only the right one proceeds.
- **Doing better for large K:** give each thread its own semaphore. Thread *i* waits on `sem[i]`, prints, then releases `sem[(i + 1) % K]`. Exactly one thread wakes each time — no thundering herd.
- **The exit condition matters:** every thread must be able to see "done", or `join()` hangs. That's the most common bug in this problem.

#### Variant: a traffic-light controller

"Two roads cross; never show green on both" is the same skill — making an invariant impossible to break — but usually solved with a state machine, not threads. One controller owns the cycle; lights are derived from the current phase, so both-green can't be represented.

```python
PHASES = [("NS", "GREEN", 30), ("NS", "YELLOW", 5), ("EW", "GREEN", 30), ("EW", "YELLOW", 5)]

def lights_at(t: int) -> dict[str, str]:
    t %= sum(d for _, _, d in PHASES)
    for road, colour, duration in PHASES:
        if t < duration:
            return {r: (colour if r == road else "RED") for r in ("NS", "EW")}
        t -= duration

assert lights_at(0) == {"NS": "GREEN", "EW": "RED"}
assert lights_at(32) == {"NS": "YELLOW", "EW": "RED"}
assert lights_at(40) == {"NS": "RED", "EW": "GREEN"}
assert all(list(lights_at(t).values()).count("GREEN") <= 1 for t in range(200))
```

A real controller adds a short all-red phase between roads, sensors that extend green when cars are waiting, and a pedestrian button — each a change to the phase list or how long a phase lasts, not to the invariant.

---

## 53. Problem: Thread-safe singleton

**How often asked:** common · **Builds on:** [8.1 Creational](#81-creational), [43.12 Memory model, atomic operations, CAS, lock-free](#4312-memory-model-atomic-operations-cas-lock-free)

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
    # release all threads at once to provoke the race
    start.wait()
    seen.append(ConfigRegistry())
ts = [Thread(target=worker) for _ in range(20)]
for t in ts: t.start()
for t in ts: t.join(2)
assert ConfigRegistry.created == 1 and all(s is seen[0] for s in seen)
```

**Follow-up:** "Why are singletons discouraged?" Hidden global state, hard to test. Prefer creating one instance at startup and injecting it.

#### Why double-checked locking was famously broken in Java

Before Java 5, double-checked locking was a well-known bug: the compiler or CPU could reorder the writes inside `new Config()`, so another thread could see a **non-null reference to a not-yet-constructed object** and use it. The fix came with the revised Java Memory Model (JSR-133, Java 5, 2004): declaring the field `volatile` forbids that reordering. That's why the Java version of this pattern always looks like `private static volatile Config instance;`. In Java, an even simpler safe option is the "holder class" idiom (the JVM initializes a nested class lazily and thread-safely), or an `enum` singleton.

---

## 54. Problem: Bounded blocking queue / producer-consumer

**How often asked:** common · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives), [17.12 Backpressure](hld-interview-guide.md#1712-backpressure)

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
# all produced -> let consumers drain and exit
q.close()
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

This is essentially Java's `ArrayBlockingQueue`: one lock and two conditions, `notEmpty` and `notFull`, exactly as here. Python's `queue.Queue` is built the same way (a mutex plus `not_empty`/`not_full`/`all_tasks_done` conditions). Writing it yourself in an interview shows you understand what those library classes do under the hood.

---

## 55. Problem: Thread-safe LRU cache

**How often asked:** common · **Builds on:** [35. Problem: LRU cache and LFU cache (with TTL)](#35-problem-lru-cache-and-lfu-cache-with-ttl), [43.6 Pessimistic vs optimistic concurrency](#436-pessimistic-vs-optimistic-concurrency)

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

---

## 56. Problem: Thread-safe rate limiter

**How often asked:** common · **Builds on:** [20. Problem: Rate limiter](#20-problem-rate-limiter), [43.3 Race conditions and critical sections](#433-race-conditions-and-critical-sections)

[20. Problem: Rate limiter](#20-problem-rate-limiter) already wraps every `allow()` in **one lock**. That's correct — the token bucket's read-modify-write of `tokens` can't interleave — but it makes every client wait for every other client. The interview follow-up is "make it scale across many threads". The change is small:

```diff
 class RateLimiter:
     def __init__(self, strategy_factory, clock=monotonic):
-        self._lock = Lock()                       # one lock for every client
+        self._create_lock = Lock()                # only guards creating a client's limiter

     def allow(self, client_id):
-        with self._lock:
-            limiter = self._limiters.get(client_id) or create(...)
-            return limiter.allow(self._clock())
+        limiter = self._limiters.get(client_id)
+        if limiter is None:
+            with self._create_lock:
+                limiter = self._limiters.get(client_id) or create(Locked(...))
+        return limiter.allow(self._clock())        # Locked(): each client has its own lock
```

- Each client's strategy is wrapped in a small **decorator** that owns a lock, so two clients never wait for each other and no strategy class changes.
- Creating a limiter is check-then-act, so it happens under its own lock with a second check inside (two threads seeing "missing" must not create two buckets). In Java this is `ConcurrentHashMap.computeIfAbsent`.

```python
from threading import Barrier, Lock, Thread
from time import monotonic

class TokenBucket:                                 # same as in the rate limiter chapter
    def __init__(self, capacity: int, refill_per_sec: float):
        self._cap, self._rate = capacity, refill_per_sec
        self._tokens, self._last = float(capacity), None

    def allow(self, now):
        if self._last is not None:
            self._tokens = min(self._cap, self._tokens + (now - self._last) * self._rate)
        self._last = now
        if self._tokens >= 1:
            self._tokens -= 1
            return True
        return False

class Locked:
    """Wraps any rate-limit strategy with its own lock."""
    def __init__(self, inner):
        self._inner, self._lock = inner, Lock()

    def allow(self, now):
        with self._lock:
            return self._inner.allow(now)

class ConcurrentRateLimiter:
    def __init__(self, strategy_factory, clock=monotonic):
        self._factory, self._clock = strategy_factory, clock
        self._limiters: dict[str, Locked] = {}
        self._create_lock = Lock()

    def allow(self, client_id: str) -> bool:
        limiter = self._limiters.get(client_id)
        if limiter is None:
            with self._create_lock:
                limiter = self._limiters.get(client_id)
                if limiter is None:
                    limiter = self._limiters[client_id] = Locked(self._factory(client_id))
        return limiter.allow(self._clock())

frozen = [0.0]
# no refill
rl = ConcurrentRateLimiter(lambda cid: TokenBucket(50, 1), clock=lambda: frozen[0])
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
assert len(rl._limiters) == 1                 # the 10 racing threads created one bucket
```

**Follow-ups:** many servers → Redis + Lua ([48. Problem: Rate limiter (distributed)](hld-interview-guide.md#48-problem-rate-limiter-distributed)) · idle buckets → evict by last access · millions of clients → lock striping (N locks, `hash(client) % N`) instead of one lock object per client.

---

## 57. Problem: Thread pool from scratch

**How often asked:** common · **Builds on:** [43.7 Thread pools and futures](#437-thread-pools-and-futures), [54. Problem: Bounded blocking queue / producer-consumer](#54-problem-bounded-blocking-queue--producer-consumer)

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
        self._workers = [Thread(target=self._work, name=f"worker-{i}") for i in range(workers)]
        for w in self._workers:
            w.start()

    def submit(self, fn, *args, **kwargs) -> Future:
        # no submit can slip in after shutdown's sentinels
        with self._lock:
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

#### The `ThreadPoolExecutor` gotcha interviewers love

Java's `ThreadPoolExecutor` takes a core size, a max size and a queue. Most people assume it grows from core to max as load rises. It doesn't: it creates threads up to **core**, then puts new tasks in the **queue**, and only creates threads beyond core when the queue is **full**. With an unbounded queue (the default in `Executors.newFixedThreadPool`) the pool never grows past core and the queue can grow until memory runs out. That's why the follow-up in this chapter about bounding the queue and choosing a rejection policy matters so much in practice.

---

## 58. Problem: Delayed task scheduler

**How often asked:** common · **Builds on:** [37. Problem: Task scheduler / job scheduler](#37-problem-task-scheduler--job-scheduler), [43.4 Synchronization primitives](#434-synchronization-primitives)

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
        # daemon: a forgotten shutdown() must not keep the process alive; pending
        # timers are dropped on exit, which is acceptable for this scheduler
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
            # one bad task must not kill the scheduler
            except Exception as exc:
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

**Follow-ups:** persistence/restarts → store tasks in a DB, poll due ones ([HLD chapter 23: Distributed job scheduler (cron at scale)](hld-interview-guide.md#23-problem-distributed-job-scheduler-cron-at-scale)) · fixed-rate vs fixed-delay (next run from scheduled time vs from completion) · many schedulers → claim tasks atomically (`SELECT ... FOR UPDATE SKIP LOCKED`).

---

## 59. Problem: Connection pool

**How often asked:** occasional · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives), [10.9 Connection pooling](hld-interview-guide.md#109-connection-pooling)

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
            # factory failed: give the permit back
            self._permits.release()
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
            # conservative: don't reuse after an error
            self.release(conn, broken=True)
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

**Follow-ups:** idle timeout/max lifetime (close old connections) · min idle (pre-warm) · pool size math: `workers × pool_size ≤ DB max_connections` — every app process has its own pool (see [6.9](hld-interview-guide.md#69-how-servers-handle-concurrent-requests)) · async pools (`asyncpg.create_pool`).

#### How big should the pool be?

The intuition "more connections = more throughput" is wrong past a small number. A database has a fixed number of CPU cores and disks; more concurrent queries than it can execute just queue inside the database and add context-switching and lock contention. The HikariCP project's well-known "About Pool Sizing" article makes this point and cites a long-standing PostgreSQL rule of thumb: connections ≈ (CPU cores × 2) + effective disk spindles — a starting point to measure from, not a law. In practice, a pool of a few dozen connections often outperforms hundreds, and many app instances each with a large pool is a common way to overload a database ([10.9 Connection pooling](hld-interview-guide.md#109-connection-pooling)).

---

## 60. Problem: In-memory pub-sub with concurrent subscribers

**How often asked:** common · **Builds on:** [32. Problem: Notification / pub-sub system](#32-problem-notification--pub-sub-system), [54. Problem: Bounded blocking queue / producer-consumer](#54-problem-bounded-blocking-queue--producer-consumer)

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
        self.thread = Thread(target=self._run)        # stopped by PubSub.close()
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
            # snapshot; don't hold the lock while delivering
            subs = list(self._topics.get(topic, ()))
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
# publisher never waited on the slow subscriber
assert time.monotonic() - started < 0.2
bus.close()
assert fast_seen == list(range(50))
assert slow.dropped > 0 and len(slow_seen) + slow.dropped == 50
```

**Follow-ups:** guaranteed delivery → don't drop; persist to a log and let subscribers track offsets (see [LLD 32](#32-problem-notification--pub-sub-system)) · ordering per key with parallelism → several worker threads, route by `hash(key) % workers` · retries with backoff → re-enqueue with attempt count, DLQ after N.

---

## 61. Problem: Hit counter (concurrent)

**How often asked:** common · **Builds on:** [43.3 Race conditions and critical sections](#433-race-conditions-and-critical-sections)

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

---

## 62. Problem: Bank transfer without deadlock

**How often asked:** common · **Builds on:** [43.5 Deadlock, livelock, starvation](#435-deadlock-livelock-starvation)

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
- *Audit trail?* Ledger entries for each transfer ([HLD chapter 20: Payment system / digital wallet](hld-interview-guide.md#20-problem-payment-system--digital-wallet)).

---

## 63. Problem: Read-write lock from scratch

**How often asked:** occasional · **Builds on:** [43.4 Synchronization primitives](#434-synchronization-primitives)

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

Java ships `ReentrantReadWriteLock` (with an optional fair mode) and, since Java 8, `StampedLock`, which adds **optimistic reads**: read without locking, then check a stamp to see whether a writer intervened, and retry only if one did. For read-heavy data with rare writes that's faster than taking even a read lock — the same optimistic-concurrency idea databases use with version columns.

---

## 64. Problem: Movie ticket booking (BookMyShow)

**How often asked:** very common · **Builds on:** [43.3 Race conditions and critical sections](#433-race-conditions-and-critical-sections), [7.4 Validate → compute → commit](#74-validate--compute--commit), [13. Problem: Ticket booking (BookMyShow / Ticketmaster)](hld-interview-guide.md#13-problem-ticket-booking-bookmyshow--ticketmaster)

**Clarify:** cities → theaters → screens → shows; seat types with prices; select seats → **hold** (10 min) → pay → confirm; hold expiry; cancellation with refund; concurrent users on the same seats.

**Entities**
- `Seat(id, seat_type)` (screen layout) · `Show(id, movie, starts_at, seats)` — seat status is **per show**, not per screen.
- `SeatState(status, hold_id, expires_at, booking_id)` · `Hold(id, user, show, seats, amount, expires_at)` · `Booking(id, hold_id, user, show, seats, amount, status)`.
- `PricingStrategy` (by seat type; could add weekend/dynamic).
- `PaymentGateway` (interface; fake in tests).
- `BookingService` — owns seat states, holds, bookings; **lock per show**.

**Invariants**
- A seat in a show is held by at most one live hold or booked by at most one booking.
- A hold is all-or-nothing across its seats.
- Confirming the same hold twice returns the same booking (idempotent) and charges once.
- **The payment call happens outside the lock.** A gateway call can take seconds; holding the show's lock that long would stall every other user booking that show. So `confirm` marks the hold as *paying* under the lock (expiry can no longer free its seats), releases the lock, charges, then takes the lock again to commit — or, if the charge failed, to put the hold back.

```python
from dataclasses import dataclass
from enum import Enum, auto
from itertools import count
import threading
from threading import Lock
from time import monotonic

class SeatStatus(Enum):
    AVAILABLE = auto()
    HELD = auto()
    BOOKED = auto()

class BookingError(Exception): ...
class SeatUnavailable(BookingError): ...
class HoldExpired(BookingError): ...
class PaymentFailed(BookingError): ...

@dataclass(frozen=True)
class Seat:
    id: str
    seat_type: str

@dataclass(frozen=True)
class Show:
    id: str
    movie: str
    starts_at: str
    seats: tuple[Seat, ...]

@dataclass
class SeatState:
    status: SeatStatus = SeatStatus.AVAILABLE
    hold_id: int | None = None
    expires_at: float = 0.0
    booking_id: int | None = None

@dataclass(frozen=True)
class Hold:
    id: int
    user: str
    show_id: str
    seat_ids: tuple[str, ...]
    amount: int
    expires_at: float

@dataclass
class Booking:
    id: int
    hold_id: int
    user: str
    show_id: str
    seat_ids: tuple[str, ...]
    amount: int
    status: str = "CONFIRMED"

class FakeGateway:
    def __init__(self): self.charged, self.refunded, self.fail = {}, [], False
    def charge(self, amount: int, idempotency_key: str) -> bool:
        if self.fail:
            return False
        self.charged.setdefault(idempotency_key, amount)      # idempotent on the key
        return True
    def refund(self, amount: int, key: str) -> None:
        self.refunded.append((key, amount))

class BookingService:
    HOLD_SECONDS = 600

    def __init__(self, prices: dict[str, int], gateway, clock=monotonic):
        self._prices, self._gateway, self._clock = prices, gateway, clock
        self._shows: dict[str, Show] = {}
        self._seats: dict[str, dict[str, SeatState]] = {}
        self._locks: dict[str, Lock] = {}
        self._holds: dict[int, Hold] = {}
        self._bookings: dict[int, Booking] = {}
        self._booking_by_hold: dict[int, int] = {}
        # holds whose payment is in flight
        self._paying: set[int] = set()
        self._ids = count(1)

    def add_show(self, show: Show) -> None:
        self._shows[show.id] = show
        self._seats[show.id] = {s.id: SeatState() for s in show.seats}
        self._locks[show.id] = Lock()

    def _free(self, st: SeatState, now: float) -> bool:
        if st.status is SeatStatus.AVAILABLE:
            return True
        return (st.status is SeatStatus.HELD and st.expires_at <= now
                # an in-flight payment keeps its seats
                and st.hold_id not in self._paying)

    def available(self, show_id: str) -> list[str]:
        with self._locks[show_id]:
            now = self._clock()
            return sorted(sid for sid, st in self._seats[show_id].items() if self._free(st, now))

    def hold(self, user: str, show_id: str, seat_ids: list[str]) -> Hold:
        if show_id not in self._shows:
            raise BookingError("unknown show")
        if not seat_ids or len(set(seat_ids)) != len(seat_ids):
            raise BookingError("seat list empty or has duplicates")
        seats = self._seats[show_id]
        if any(s not in seats for s in seat_ids):
            raise BookingError("unknown seat")
        types = {s.id: s.seat_type for s in self._shows[show_id].seats}
        with self._locks[show_id]:
            now = self._clock()
            taken = [s for s in seat_ids if not self._free(seats[s], now)]
            if taken:
                raise SeatUnavailable(taken)                  # nothing changed
            hold = Hold(next(self._ids), user, show_id, tuple(seat_ids),
                        sum(self._prices[types[s]] for s in seat_ids), now + self.HOLD_SECONDS)
            for s in seat_ids:
                seats[s] = SeatState(SeatStatus.HELD, hold.id, hold.expires_at)
            self._holds[hold.id] = hold
            return hold

    def confirm(self, hold_id: int) -> Booking:
        hold = self._holds.get(hold_id)
        if hold is None:
            raise BookingError("unknown hold")
        lock, seats = self._locks[hold.show_id], self._seats[hold.show_id]
        with lock:                                            # 1. validate and claim
            if hold_id in self._booking_by_hold:              # idempotent retry
                return self._bookings[self._booking_by_hold[hold_id]]
            if hold_id in self._paying:
                raise BookingError("payment already in progress")
            now = self._clock()
            if any(seats[s].hold_id != hold_id or seats[s].status is not SeatStatus.HELD
                   or seats[s].expires_at <= now for s in hold.seat_ids):
                raise HoldExpired(hold_id)
            self._paying.add(hold_id)
        # 2. slow network call with no lock held; the key makes a retried charge safe
        ok = self._gateway.charge(hold.amount, idempotency_key=f"hold-{hold_id}")
        with lock:                                            # 3. commit or undo
            self._paying.discard(hold_id)
            if not ok:
                # seats stay HELD; user may retry
                raise PaymentFailed(hold_id)
            booking = Booking(next(self._ids), hold_id, hold.user, hold.show_id, hold.seat_ids, hold.amount)
            for s in hold.seat_ids:
                seats[s] = SeatState(SeatStatus.BOOKED, booking_id=booking.id)
            self._bookings[booking.id] = booking
            self._booking_by_hold[hold_id] = booking.id
            return booking

    def cancel(self, booking_id: int) -> None:
        booking = self._bookings[booking_id]
        with self._locks[booking.show_id]:
            if booking.status != "CONFIRMED":
                raise BookingError("already cancelled")
            for s in booking.seat_ids:
                self._seats[booking.show_id][s] = SeatState()
            booking.status = "CANCELLED"
            self._gateway.refund(booking.amount, f"booking-{booking_id}")

now = [0.0]
gw = FakeGateway()
svc = BookingService({"GOLD": 30000, "SILVER": 20000}, gw, clock=lambda: now[0])
svc.add_show(Show("s1", "Dune", "2025-05-01T18:00",
                  (Seat("A1", "GOLD"), Seat("A2", "GOLD"), Seat("B1", "SILVER"))))
h1 = svc.hold("u1", "s1", ["A1", "A2"])
assert h1.amount == 60000 and svc.available("s1") == ["B1"]
try:
    svc.hold("u2", "s1", ["A2", "B1"]); assert False
except SeatUnavailable as e:
    assert e.args[0] == ["A2"]
assert svc.available("s1") == ["B1"]                      # failed hold changed nothing
now[0] = 601                                              # u1's hold expires
h2 = svc.hold("u2", "s1", ["A2", "B1"])
try:
    svc.confirm(h1.id); assert False
except HoldExpired:
    pass
b = svc.confirm(h2.id)
assert svc.confirm(h2.id) is b and gw.charged == {f"hold-{h2.id}": 50000}   # idempotent
assert svc.available("s1") == ["A1"]
svc.cancel(b.id)
assert svc.available("s1") == ["A1", "A2", "B1"] and gw.refunded == [(f"booking-{b.id}", 50000)]

# 50 users race for overlapping seats: exactly one hold can win, because every
# request contains A2
svc3 = BookingService({"GOLD": 30000}, FakeGateway())
svc3.add_show(Show("s3", "Dune", "2025-05-01T21:00", tuple(Seat(f"A{i}", "GOLD") for i in (1, 2, 3))))
start, winners = threading.Barrier(50), []

def attempt(i):
    start.wait()
    try:
        winners.append(svc3.hold(f"u{i}", "s3", ["A2", "A3"] if i % 2 else ["A1", "A2"]))
    except SeatUnavailable:
        pass

threads = [threading.Thread(target=attempt, args=(i,)) for i in range(50)]
for t in threads: t.start()
for t in threads: t.join(5)
assert len(winners) == 1

# A slow payment doesn't block the show, and its seats can't be stolen meanwhile
class SlowGateway(FakeGateway):
    def __init__(self):
        super().__init__()
        self.entered, self.release = threading.Event(), threading.Event()
    def charge(self, amount, idempotency_key):
        self.entered.set()
        self.release.wait(2)
        return super().charge(amount, idempotency_key)

clock = [0.0]
slow_gw = SlowGateway()
svc4 = BookingService({"GOLD": 30000}, slow_gw, clock=lambda: clock[0])
svc4.add_show(Show("s4", "Dune", "2025-05-02T18:00", (Seat("A1", "GOLD"), Seat("B1", "GOLD"))))
paying = svc4.hold("u1", "s4", ["A1"])
result = []
payer = threading.Thread(target=lambda: result.append(svc4.confirm(paying.id)))
payer.start()
assert slow_gw.entered.wait(2)                 # u1 is now inside the gateway call
assert svc4.hold("u2", "s4", ["B1"]).amount == 30000   # the show's lock is free
clock[0] = 10_000                              # u1's hold has technically expired…
try:
    svc4.hold("u3", "s4", ["A1"]); assert False
# …but an in-flight payment keeps its seat
except SeatUnavailable:
    pass
slow_gw.release.set()
payer.join(2)
assert result and result[0].seat_ids == ("A1",)
```

**Follow-ups**
- *Two users click the same seat at the same instant?* The check-and-hold runs under the show's lock; in a DB it's one conditional `UPDATE ... WHERE status = 'AVAILABLE' OR hold expired` and checking the row count.
- *Payment completes after the hold expired?* `confirm` refuses → refund the charge (compensation).
- *Real-time seat map?* Observer → WebSocket push of seat status changes.
- *Different prices for weekends/premium shows?* Pricing strategy takes `(show, seat)`.
- *Why not lock seats when the user opens the seat map?* Browsing users would block real buyers; lock only on explicit selection with a short TTL.

#### Why a lock per show and not per seat?

Per-seat locks give the most concurrency, but a booking usually takes **several** seats, so you'd need to acquire several locks — always in a fixed order (sorted seat IDs) to avoid deadlock — and release them all on every path. A lock per show is coarser but simple, and contention only happens between people booking the **same show**, which is exactly the contention that matters. Start coarse; go finer only if measurement shows the per-show lock is the bottleneck. (In a real multi-server deployment, neither in-process lock is enough — see [the HLD ticket booking chapter](hld-interview-guide.md#13-problem-ticket-booking-bookmyshow--ticketmaster).)

---

## 65. Problem: Wallet / payment service

**How often asked:** occasional · **Builds on:** [62. Problem: Bank transfer without deadlock](#62-problem-bank-transfer-without-deadlock), [43.5 Deadlock, livelock, starvation](#435-deadlock-livelock-starvation), [20. Problem: Payment system / digital wallet](hld-interview-guide.md#20-problem-payment-system--digital-wallet) · A common fintech machine-coding prompt (Paytm, PhonePe).

**Clarify:** users with wallets; top-up; transfer between users; statement (filter, sort); offers (e.g., cashback on a user's first transfer of the day, 5% capped at ₹50); no overdraft; concurrent transfers.

**Entities:** `Wallet(user, balance, lock)` · `Transaction(id, type, from, to, amount, ts)` (append-only ledger) · `Offer` strategy.

**Invariants:** balance ≥ 0; a transfer debits and credits together or not at all; total money changes only by top-ups and cashback; every movement is a transaction.

```python
from dataclasses import dataclass
from datetime import datetime
from enum import Enum, auto
from itertools import count
from threading import Lock, Thread

class TxnType(Enum):
    TOPUP = auto()
    TRANSFER = auto()
    CASHBACK = auto()

class WalletError(Exception): ...

@dataclass(frozen=True)
class Transaction:
    id: int
    type: TxnType
    from_user: str | None
    to_user: str
    amount: int
    ts: datetime

class Wallet:
    def __init__(self, user: str):
        self.user, self.balance, self.lock = user, 0, Lock()

def first_transfer_of_day_cashback(pct: int = 5, cap: int = 5000):
    def offer(service: "WalletService", txn: Transaction) -> int:
        same_day = [t for t in service.transactions
                    if t.type is TxnType.TRANSFER and t.from_user == txn.from_user and t.ts.date() == txn.ts.date()]
        return min(cap, txn.amount * pct // 100) if len(same_day) == 1 else 0
    return offer

class WalletService:
    def __init__(self, offers=(), clock=datetime.now):
        self.wallets: dict[str, Wallet] = {}
        self.transactions: list[Transaction] = []
        self._offers, self._clock = list(offers), clock
        self._ids, self._log_lock = count(1), Lock()

    def create(self, user: str) -> None:
        if user in self.wallets:
            raise WalletError("wallet exists")
        self.wallets[user] = Wallet(user)

    def _record(self, type_, from_user, to_user, amount) -> Transaction:
        with self._log_lock:
            txn = Transaction(next(self._ids), type_, from_user, to_user, amount, self._clock())
            self.transactions.append(txn)
            return txn

    def top_up(self, user: str, amount: int) -> None:
        if amount <= 0:
            raise WalletError("amount must be positive")
        w = self.wallets[user]
        with w.lock:
            w.balance += amount
        self._record(TxnType.TOPUP, None, user, amount)

    def transfer(self, src: str, dst: str, amount: int) -> Transaction:
        if src == dst or amount <= 0:
            raise WalletError("invalid transfer")
        a, b = self.wallets[src], self.wallets[dst]
        # fixed lock order: no deadlock
        first, second = sorted((a, b), key=lambda w: w.user)
        with first.lock, second.lock:
            if a.balance < amount:
                raise WalletError("insufficient balance")
            a.balance -= amount
            b.balance += amount
            txn = self._record(TxnType.TRANSFER, src, dst, amount)
        for offer in self._offers:
            cashback = offer(self, txn)
            if cashback:
                with a.lock:
                    a.balance += cashback
                self._record(TxnType.CASHBACK, None, src, cashback)
        return txn

    def statement(self, user: str, type_: TxnType | None = None, newest_first: bool = True) -> list[Transaction]:
        rows = [t for t in self.transactions if user in (t.from_user, t.to_user) and (type_ is None or t.type is type_)]
        return sorted(rows, key=lambda t: t.id, reverse=newest_first)

clock = [datetime(2025, 1, 1, 10)]
svc = WalletService([first_transfer_of_day_cashback()], clock=lambda: clock[0])
for u in ("a", "b"):
    svc.create(u)
svc.top_up("a", 100_000)
svc.transfer("a", "b", 20_000)                       # first today → 5% cashback = 1000
svc.transfer("a", "b", 20_000)                       # second today → none
assert svc.wallets["a"].balance == 61_000 and svc.wallets["b"].balance == 40_000
try:
    svc.transfer("b", "a", 10**9); assert False
except WalletError:
    pass
assert [t.type for t in svc.statement("a")] == [TxnType.TRANSFER, TxnType.CASHBACK, TxnType.TRANSFER, TxnType.TOPUP]

svc2 = WalletService()
for u in ("x", "y"):
    svc2.create(u); svc2.top_up(u, 10_000)
def ping(src, dst):
    for _ in range(500):
        try:
            svc2.transfer(src, dst, 7)
        except WalletError:
            pass
ts = [Thread(target=ping, args=p) for p in [("x", "y"), ("y", "x")] * 2]
for t in ts: t.start()
for t in ts: t.join(10)
# conserved, no deadlock
assert svc2.wallets["x"].balance + svc2.wallets["y"].balance == 20_000
```

**Follow-ups:** idempotency keys on transfer (client retries) · persistence with a double-entry ledger (HLD payment system) · offer engine with multiple rules (Strategy list, priority, stacking rules) · limits (KYC-based daily caps).

#### Why a ledger, and not just balances?

A `balance` field answers "how much do I have?", but not "why?". Every support ticket ("where did my ₹500 go?") and every reconciliation with the bank needs the history. So every movement is a `Transaction` in an append-only ledger, and the balance is a cache of the ledger's sum, updated in the same atomic step. Real systems go further with **double-entry** bookkeeping — every transfer is a debit on one account and a credit on another, so the total across all accounts never changes except for top-ups — described in [20. Problem: Payment system / digital wallet](hld-interview-guide.md#20-problem-payment-system--digital-wallet).

---

## 66. Concurrency interview Q&A

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

**Q: How do you test concurrent code?**
Start many threads behind a `Barrier` to maximize overlap; assert invariants (counts conserved, no double booking); join with timeouts to detect deadlocks; run many iterations; inject clocks to avoid sleeps; stress tests in CI.

**Q: What is thread starvation, and how did your RW lock avoid it?**
A thread never gets the resource. Writer-preferring: new readers wait while a writer is waiting, so a stream of readers can't block writers forever.

**Q: What's a thread-safe way to lazily initialize something expensive?**
Double-checked locking, or a per-key `Future`/`Event` (single-flight) so only one thread does the work and the others wait for its result.
