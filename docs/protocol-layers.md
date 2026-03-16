# Protocol Layers

Protocol-Driven Development organises the development process into distinct layers of protocols, each governing a different phase of the lifecycle (from specification through to validation). This document will describe those layers in detail.

Protocol-Driven Development (PDD) structures AI-assisted software development as a sequence of **protocol layers**. Each layer defines a set of rules that must be satisfied before the development process can move forward.

These protocols ensure that code generation occurs within a controlled engineering pipeline rather than as an unconstrained generative process.

Instead of allowing implementation details to emerge unpredictably, the system is built through **progressively refined stages**, each governed by explicit constraints.

Problem
↓
Specification Protocol
↓
Architecture Protocol
↓
Generation Protocol
↓
Validation Protocol
↓
Production System


Each layer serves a different purpose, but together they form the structured environment that allows AI to assist with implementation safely and reliably.

---

# 1. Specification Protocol

The Specification Protocol defines **what the system must do** before any implementation begins.

Its purpose is to capture the intended behaviour of the system in a clear, explicit form that both humans and AI systems can reason about.

A specification typically includes:

- system purpose
- inputs and outputs
- functional behaviour
- constraints
- edge cases
- error handling

The specification stage forces assumptions to become explicit.

Without this stage, misunderstandings often remain hidden inside generated implementation code. When specification exists first, these issues tend to surface early.

Example specification structure:
System: Payment Processor

Inputs
- payment request
- customer identifier

Outputs
- payment confirmation
- error response

Constraints
- transaction must be idempotent
- payment validation required


The AI should not generate implementation until the specification protocol has been satisfied.

---

# 2. Architecture Protocol

Once the behaviour of the system is defined, the Architecture Protocol describes **how the system is structured**.

Architecture in PDD is treated as a protocol rather than a static diagram. It defines the structural rules that generated code must follow.

An architecture protocol may define:

- module boundaries
- component responsibilities
- allowed dependencies
- data models
- interface contracts
- system layering

Example architecture structure:
Modules

PaymentService
Handles payment processing logic

ValidationService
Handles input validation and security checks

PaymentRepository
Handles persistence of payment data


AI-generated code must operate within these defined boundaries.

Architecture should not emerge accidentally from generated implementation. Instead, the architecture protocol defines the structural rules that constrain generation.

---

# 3. Generation Protocol

The Generation Protocol governs **how AI may generate implementation code**.

It ensures that code generation occurs only when the necessary information and constraints are available.

Typical rules within a generation protocol may include:

- code generation cannot begin until specification is approved
- generated modules must conform to the architecture definition
- interfaces must match defined contracts
- dependencies must follow architecture rules

Example generation protocol rule:
Rule: Generated modules must not introduce new dependencies outside the architecture definition.


The purpose of this protocol is not to restrict creativity but to ensure that generated implementation remains consistent with the system design.

AI becomes an implementation assistant operating inside clearly defined boundaries.

---

# 4. Validation Protocol

The Validation Protocol ensures that generated code meets required standards before becoming part of the system.

Validation restores determinism to the development pipeline.

Typical validation mechanisms include:

- automated tests
- contract enforcement
- type checks
- static analysis
- architecture rule validation

Example validation checks:
- All public interfaces must have test coverage
- Module dependencies must match architecture rules
- Data models must conform to defined schemas


If validation fails, the generated implementation must be revised before integration.

This ensures that even though AI generation may vary slightly, the resulting system remains reliable and consistent.

---

# 5. Integration Protocol

The Integration Protocol governs how validated code becomes part of the larger system.

It ensures that new components integrate safely without introducing architectural drift or hidden coupling.

Integration protocols may include:

- dependency verification
- integration tests
- deployment checks
- performance validation

Example integration rule:
New modules must pass integration tests against existing system interfaces before deployment.


Integration protocols maintain system coherence as the codebase grows.

---

# How the Protocol Layers Work Together

Each protocol layer performs a different role:

| Protocol | Purpose |
|--------|--------|
| Specification Protocol | defines system behaviour |
| Architecture Protocol | defines system structure |
| Generation Protocol | governs AI code creation |
| Validation Protocol | ensures correctness |
| Integration Protocol | ensures safe system growth |

Together they create a structured pipeline where:

- assumptions are made explicit early
- architecture is enforced consistently
- implementation is constrained by design
- validation ensures reliability

The result is a development process where AI can generate code rapidly while engineering discipline maintains control over the system.

---

# Protocols as Engineering Guardrails

The purpose of protocol layers is not to slow development but to **prevent uncontrolled complexity**.

AI dramatically reduces the cost of producing code. Without structure, this can lead to rapid architectural drift and systems that are difficult to maintain.

Protocols act as guardrails around the generative process.

They ensure that even when implementation is produced automatically, the system evolves in a controlled and predictable manner.

---

# Summary

Protocol layers form the backbone of Protocol-Driven Development.

They define the rules that govern each stage of the development pipeline:
Specification → Architecture → Generation → Validation → Integration


By enforcing these layers, PDD enables AI-assisted development to scale while preserving the engineering discipline required to build reliable software systems.