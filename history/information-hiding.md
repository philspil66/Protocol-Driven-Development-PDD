# Information Hiding

Information Hiding is a fundamental principle of software engineering introduced by **David L. Parnas** in his 1972 paper *On the Criteria to Be Used in Decomposing Systems into Modules*.

The central idea is simple:

> Modules should hide design decisions that are likely to change.

Instead of organizing a system around the order of processing or the structure of algorithms, systems should be decomposed into modules that **encapsulate implementation details behind clear interfaces**.

This makes systems easier to understand, modify, and maintain as they evolve.

---

# The Core Idea

In many early software systems, modules were structured around the sequence of operations in a program.

For example:

```
Read Input
Process Data
Write Output
```

While this structure may reflect how a program executes, it does not protect the system from change. If the internal data structures or algorithms change, multiple parts of the system may need to be modified.

Information Hiding proposes a different approach.

Each module should encapsulate **a specific design decision**, and other parts of the system should interact with that module only through a defined interface.

```
Module Interface
    ↓
Hidden Implementation
```

Other components of the system do not need to know how the module works internally.

---

# Example

Consider a module responsible for storing payment records.

A simple interface might look like:

```
storePayment(transaction)
getPayment(transactionId)
```

The implementation might use:

- a relational database
- a document store
- an in-memory cache
- a distributed storage system

With proper information hiding, the rest of the system does not depend on the implementation choice.

Only the interface matters.

This means the storage mechanism can change without affecting the rest of the system.

---

# Why Information Hiding Matters

Information Hiding improves software systems in several ways:

## Reduced Coupling

Modules depend only on interfaces, not internal implementation details.

This reduces the number of dependencies between components.

---

## Easier Maintenance

Because design decisions are localized, changes can often be made inside a module without affecting the rest of the system.

---

## Improved System Understanding

Clear module boundaries make it easier for developers to reason about system structure.

Instead of understanding the entire codebase, developers can focus on individual modules.

---

## Safer Evolution

Systems evolve over time. New requirements, technologies, and constraints often force implementation changes.

Information hiding ensures that these changes have minimal impact on the rest of the system.

---

# Information Hiding vs Encapsulation

Information Hiding is often confused with encapsulation, but the concepts are slightly different.

Encapsulation is a language-level mechanism for restricting access to data or functions.

Information Hiding is a **design principle** that guides how systems should be decomposed into modules.

Encapsulation can help implement information hiding, but the architectural decision about what to hide is made during system design.

---

# Information Hiding in Protocol-Driven Development

Protocol-Driven Development relies heavily on information hiding when defining system architecture.

During the **Architecture Protocol**, developers define module boundaries that hide implementation decisions behind clear interfaces.

Example architecture:

```
PaymentService
ValidationService
PaymentRepository
```

Each module exposes a defined interface while hiding internal implementation details.

This structure allows AI-generated implementation to change internally without affecting other parts of the system.

---

# Protecting the System from AI-Generated Complexity

AI code generation can produce large volumes of implementation quickly.

Without strong module boundaries, this can lead to:

- tightly coupled systems
- hidden dependencies
- architecture drift

Information hiding acts as a protective mechanism.

By enforcing clear interfaces and module responsibilities, PDD ensures that generated code remains isolated within well-defined boundaries.

---

# Interaction with Protocol Layers

In Protocol-Driven Development, information hiding is primarily enforced through the **Architecture Protocol**.

The architecture protocol defines:

- module boundaries
- allowed dependencies
- interface definitions
- responsibilities of each component

Generated implementation must operate within these boundaries.

This ensures that internal design decisions remain hidden while the system structure remains stable.

---

# Historical Importance

Information Hiding was a turning point in software engineering.

Parnas argued that modules should be designed to **protect the system from change**, rather than simply reflect the order of execution.

This idea strongly influenced later developments in:

- modular programming
- object-oriented design
- layered architecture
- microservices

Today, the same principle helps control complexity in AI-assisted development.

---

# Summary

Information Hiding is the principle that modules should conceal internal design decisions behind stable interfaces.

By hiding implementation details, systems gain:

- reduced coupling
- improved maintainability
- clearer architecture
- safer evolution

In Protocol-Driven Development, information hiding ensures that AI-generated implementation remains contained within well-defined module boundaries, allowing the system architecture to remain stable even as the implementation evolves.