# Protocol Driven Development (PDD)

Protocol-Driven Development is a lightweight set of patterns for building and evolving software safely in a world where humans and AI systems both participate in development and maintenance.

PDD is not a framework, a methodology, or a replacement for Agile.
It is a way of making intent, constraints, and change explicit so systems can evolve without fear.

---

## Why Protocol-Driven Development?

Modern teams face a growing set of problems:

- Codebases are fragile and difficult to change safely
- Critical knowledge lives in people’s heads or tribal memory
- Agile processes often optimise for flow, not safety
- AI tools can generate and refactor code quickly, but can also break important invariants
- Maintenance now matters more than greenfield development

PDD starts from a simple observation:

> Code is no longer maintained only by humans.  
> Change itself must become explicit and governed.

---

## Core Idea

PDD treats **change** as a first-class concern.

Instead of relying on:
- conventions
- unwritten rules
- process ceremony
- implicit assumptions

PDD introduces **protocols**: explicit, local, enforceable rules that define how code is built and how it may be safely changed.

These protocols are:
- colocated with the code they govern
- readable by humans
- actionable by AI tools
- enforceable by workflow and tooling

---

## What PDD Is Not

- Not a programming language
- Not an AI framework
- Not a replacement for testing
- Not another Agile variant
- Not a rigid methodology

PDD is designed to be adopted incrementally.
You can add one protocol to one file and stop there.

---

## The PDD Pattern Set

PDD is an umbrella for a small family of patterns.
You do not need all of them.

### 1. Intent Contracts (IC)

Intent Contracts describe **what is being built and why**, before implementation begins.

They capture:
- goals
- non-goals
- invariants
- error modes
- verification criteria

Intent Contracts live outside code initially and guide creation.

---

### 2. Local Build Protocols (LBP)

Local Build Protocols guide **how new code should be created**.

They:
- define build steps
- constrain design decisions
- specify tests and verification
- act as scaffolding instructions for humans and AI tools

LBPs usually start outside the codebase and are embedded into files when they are created.

---

### 3. Local Change Protocols (LCP)

Local Change Protocols govern **how existing code may be modified**.

They:
- identify dangerous areas
- define constraints and invariants
- require tests or human review for specific changes
- prevent silent breakage by automated tools

LCPs are embedded directly in high-risk files.

---

### 4. Zones and Guardrails (ZON)

Zones and Guardrails define **risk boundaries** in a repository.

They:
- classify folders or modules by risk level
- determine which protocols are required
- set expectations for automation vs human review

Zones make large systems navigable for both humans and AI.

---

### 5. Change Units (CU)

Change Units treat **change itself as a structured artifact**.

Instead of a diff or ticket, a Change Unit captures:
- intent
- scope
- constraints
- risk
- verification
- rollback strategy

Code becomes an output of a Change Unit, not the primary artifact.

---

## A Simple Example

```php
/**
 * LOCAL_CHANGE_PROTOCOL
 * scope: payment amount calculation
 * risk_level: high
 *
 * constraints:
 *   - do_not_change: idempotency key generation
 *   - do_not_change: rounding rules
 *
 * requires_human_review:
 *   - any change to amount calculation logic
 *
 * verification:
 *   - PaymentContractTests must pass
 */
final class PaymentCalculator
{
    // ...
}

## This Is Not Documentation

This is not documentation.  
It is a binding rule that governs change.

---

## How PDD Works With AI Tools

PDD is designed to work with AI-assisted tools such as Cursor, Copilot, or autonomous agents.

Protocols:

- give AI systems explicit constraints
- prevent unsafe automated changes
- enable safe refactoring and maintenance
- allow AI to refuse changes when rules are violated

AI becomes a collaborator, not a risk.

---

## Adoption Strategy

PDD is intentionally incremental.

Suggested starting points:

1. Identify one high-risk area
2. Add a single Local Change Protocol
3. Use it during the next modification
4. Expand only if it helps

There is no requirement to convert an entire codebase.

---

## Relationship to Agile and Existing Practices

PDD does not replace Agile, Scrum, Kanban, TDD, or DDD.

Instead, it addresses gaps teams often feel but struggle to name:

- change fear
- fragile systems
- lack of ownership
- unclear invariants
- unsafe automation

PDD complements existing practices by making constraints explicit.

---

## Project Status

This project is exploratory and evolving.

The goal is to:

- study how developers experience change today
- experiment with protocol-based patterns
- validate what works in real systems
- share practical templates and examples

This is not a finished methodology.  
It is a working set of ideas under active refinement.

---

## Contributing

Contributions are welcome, especially:

- real-world examples
- protocol templates
- failure cases
- critiques and refinements

This project values practical experience over theory.

---

## Philosophy

PDD is built on a simple belief:

> The future of software is not about writing code faster.  
> It is about changing systems safely.

Protocols make that possible.
