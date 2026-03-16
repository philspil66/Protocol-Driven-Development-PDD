# Protocol-Driven Development (PDD)

Protocol-Driven Development (PDD) is a software engineering approach for building reliable systems using AI-assisted code generation.

Instead of generating code directly from prompts, PDD introduces **explicit engineering protocols** that govern how software is specified, generated, and validated.

In PDD:

- The AI can be probabilistic.
- The pipeline cannot.

AI becomes the **implementation engine**, while the engineering process ensures determinism, structure, and reliability.

---

# The Problem PDD Addresses

Modern AI coding tools can generate large volumes of working code very quickly.

However, this often introduces new problems:

- architecture drift
- hidden design assumptions
- inconsistent patterns
- review bottlenecks
- fragile systems that compile but are difficult to reason about

In many workflows the process looks like:

`prompt → AI → code`


While this may work for small experiments, it does not scale well for large systems.

PDD shifts the focus away from **code generation** and toward **controlling the process that governs generation**.

---

# The Core Idea

Protocol-Driven Development introduces **explicit protocols** that define how software must be developed.

Instead of allowing AI to improvise architecture and implementation details, the system enforces a structured pipeline.

```
Problem
    ↓
Specification
    ↓
Architecture Protocol
    ↓
AI Code Generation
    ↓
Validation & Contracts
    ↓
Production System
```

The protocol governs the generator.

---

# Key Principles

## Deterministic Process

AI models are inherently probabilistic.

The development process around them must be deterministic.

Protocols define the rules that constrain generation and ensure predictable outcomes.

---

## Specification Before Implementation

Systems should be described before they are implemented.

Specifications define behaviour, inputs, outputs, constraints, and edge cases before code is produced.

---

## Architecture as a Protocol

Architecture defines the structural rules of the system:

- module boundaries
- interfaces
- data models
- dependencies
- system constraints  

AI generation must conform to these rules.

---

## Validation Restores Determinism

Generated code must pass explicit validation layers:

- tests
- contracts
- static analysis
- architectural constraints  

These mechanisms ensure reliability regardless of how the code was generated.

---

# Relationship to Classic Software Engineering

Protocol-Driven Development builds on several foundational ideas in software engineering:

| Concept | Origin |
|---------|--------|
| Stepwise Refinement | Niklaus Wirth (1971) |
| Information Hiding | David Parnas (1972) |
| Design by Contract | Bertrand Meyer |
| Structured Programming | Edsger Dijkstra |

These ideas were originally developed to control **software complexity**.

AI code generation makes them relevant again.

---

# Why This Matters

AI has made code generation extremely inexpensive.

The bottleneck in software development is shifting away from writing code and toward:

- architectural consistency
- system reasoning
- validation
- maintainability  

Protocol-Driven Development focuses on the **engineering process that governs AI generation**, rather than the generation itself.

---

# Repository Structure

This repository is an evolving exploration of Protocol-Driven Development.

- **docs/**
  - [introduction.md](docs/introduction.md)
  - [core-principles.md](docs/core-principles.md)
  - [protocol-layers.md](docs/protocol-layers.md)
  - [ai-development-pipeline.md](docs/ai-development-pipeline.md)
- **history/**
  - [stepwise-refinement.md](history/stepwise-refinement.md)
  - [design-by-contract.md](history/design-by-contract.md)
  - [information-hiding.md](history/information-hiding.md)
- **protocols/**
  - [specification-protocol.md](protocols/specification-protocol.md)
  - [architecture-protocol.md](protocols/architecture-protocol.md)
  - [generation-protocol.md](protocols/generation-protocol.md)
  - [validation-protocol.md](protocols/validation-protocol.md)
- **examples/**
  - [simple-pipeline-example.md](examples/simple-pipeline-example.md)
- **diagrams/**
  - [pdd-overview.md](diagrams/pdd-overview.md)

---

# A Simple Definition

Protocol-Driven Development is an engineering approach where:

> **AI-assisted code generation is governed by explicit protocols that define specification, architecture, generation, and validation.**

Or more simply: `Protocol → AI → Code`

The protocol controls the generator.

---

# Status

This repository is an early exploration of the concept.

The goal is to develop a practical framework for building reliable software systems using AI-assisted development while maintaining strong engineering discipline.

---

# Contributing

Contributions and discussion are welcome.

Areas of interest include:

- engineering protocols for AI development
- architecture constraint systems
- validation pipelines
- specification-first workflows
- practical implementations of PDD  

---

# License

TBD
