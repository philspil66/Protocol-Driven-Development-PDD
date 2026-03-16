# Pipeline Diagram

The Protocol-Driven Development (PDD) pipeline can be expressed as a simple sequence of constrained stages.

Each stage reduces ambiguity and adds structure before the next stage begins.

```
Problem
    ↓
Specification
    ↓
Architecture
    ↓
Protocol Enforcement
    ↓
AI Implementation
    ↓
Validation
    ↓
Integration
    ↓
Production
```

This is the core flow of PDD.

---

## What the Diagram Means

### Problem

The starting point is a real system need.

This is not yet code and not yet architecture.

It is the statement of what must be solved.

### Specification

The system behaviour is defined before implementation begins.

This stage makes assumptions explicit:
- inputs
- outputs
- constraints
- edge cases
- expected behaviour

### Architecture

The system structure is defined.

This stage establishes:
- module boundaries
- interfaces
- dependencies
- responsibilities

The architecture exists before code generation.

### Protocol Enforcement

The rules governing development are applied.

These protocols determine:
- what must be true before generation starts
- what architecture constraints must be respected
- what outputs are allowed
- what validation rules must pass later

This is where the pipeline begins to control the generator.

### AI Implementation

AI generates implementation within the constraints defined by earlier stages.

### Validation

Generated code is verified before integration.

Typical validation includes:
- tests
- contracts
- static analysis
- architecture checks
- type validation

This restores determinism to the pipeline.

### Integration

Validated code is integrated into the wider system.

This ensures:
- compatibility with existing modules
- interface correctness
- safe system growth

### Production

Only after successful validation and integration does the implementation become part of the production system.

---

## The Key Shift

Traditional development is often understood as:

`Developer → Code`

Naive AI-assisted development often becomes:

`Prompt → AI → Code`

Protocol-Driven Development changes the model to:

`Protocol → AI → Code`

This is the central idea of PDD.

The protocol governs the generator.

---

## A More Compact View

The same pipeline can also be shown in a compact form:

`Define → Structure → Constrain → Generate → Verify → Integrate`

Where:
- Define = Problem + Specification
- Structure = Architecture
- Constrain = Protocol Enforcement
- Generate = AI Implementation
- Verify = Validation
- Integrate = Integration + Production

---

## Why This Matters

AI reduces the cost of generating code.

PDD reduces the risk of generating uncontrolled systems.

That is why the pipeline matters.

- The AI can be probabilistic.
- The pipeline cannot.

---

## Summary

The PDD pipeline is a controlled engineering sequence:

`Problem → Specification → Architecture → Protocol Enforcement → AI Implementation → Validation → Integration → Production`

Each stage exists to reduce ambiguity, preserve structure, and ensure that AI-generated implementation remains aligned with system design.