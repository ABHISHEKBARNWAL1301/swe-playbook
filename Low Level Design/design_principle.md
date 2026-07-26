# Design Principles


## SOLID PRINCIPLE

The SOLID principles are a set of five design principles that help software developers create maintainable, scalable, and robust software systems 


1. **Single Responsibility Principle (SRP)**: A class should have only one reason to change, meaning it should have only one job or responsibility. Instead of having one massive class doing everything poorly, you have smaller, focused classes doing one thing well. These classes are: Easier to read,test,maintain,reuse  
    - Focus on cohesion, not fragmentation. Group logic that changes together or belongs to the same business concern.
    - Watch for creeping responsibilities even in utility classes. Apply SRP early, before small classes become unmanageable.
    - SRP can and should be applied across multiple levels:Class, Method, Module, Service, System. SRP is a mindset: separate concerns to improve clarity and adaptability, no matter the scale.
