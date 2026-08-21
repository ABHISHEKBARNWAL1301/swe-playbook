# Design Principles


## SOLID PRINCIPLE

The SOLID principles are a set of five design principles that help software developers create maintainable, scalable, and robust software systems 


1. **Single Responsibility Principle (SRP)**: A class should have only one reason to change, meaning it should have only one job or responsibility. Instead of having one massive class doing everything poorly, you have smaller, focused classes doing one thing well. These classes are: Easier to read,test,maintain,reuse  
    - Focus on cohesion, not fragmentation. Group logic that changes together or belongs to the same business concern.
    - Watch for creeping responsibilities even in utility classes. Apply SRP early, before small classes become unmanageable.
    - SRP can and should be applied across multiple levels:Class, Method, Module, Service, System. SRP is a mindset: separate concerns to improve clarity and adaptability, no matter the scale.

## OOP design decision checks

- Put mutable state on the instance that owns the invariant. Class-level mutable state is shared by every instance and can accidentally couple unrelated objects.
- Use inheritance only when the subtype is safely substitutable for the base type: callers should not need type checks, weakened guarantees, or special cases.
- Prefer composition or delegation when the goal is to reuse one collaborator's behavior, vary one policy, wrap a dependency, or swap implementations for tests.
- Keep interfaces small and client-shaped. A broad interface that forces clients to depend on unused methods is a sign to split roles.
- Depend on stable abstractions at boundaries such as storage, notification, payment, search, and clock/time. Let concrete adapters sit at the edge.

Interview rule of thumb: start with responsibilities and invariants, then choose the mechanism. Inheritance models an "is-a and remains substitutable" relationship; composition models a "has-a or uses-a" relationship and usually keeps change localized.

## LLD extension-point checklist

For object-model design questions, first model the stable domain entities and then isolate the rules likely to change independently. A good extension point has a small interface, a clear caller, and a concrete reason it may vary.

- Policy objects fit rules such as pricing, spot assignment, ranking, eligibility, discounting, retry, and notification selection.
- Coordinator services fit workflows that must update several entities while preserving an invariant, such as issuing a ticket only after reserving a spot.
- Repositories or gateways fit boundaries that may later move from in-memory data to a database, payment provider, hardware device, or external service.
- Domain entities should still own their local invariant. Do not move every method into a service just because services are easy to extend.
- In an interview, name the first three expected changes and show where each change lands without editing the core entity model.
