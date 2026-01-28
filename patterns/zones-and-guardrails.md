# Zones and Guardrails

## Problem

Large repositories hide risk.
Not all code deserves the same level of automation.

## Solution

Zones classify code by risk level and define required protocols.

## Example

```md
High Risk
- payments
- auth
Rules:
- LCP required
- human review required

Medium Risk
- pricing
- order flow
Rules:
- LBP recommended

Low Risk
- ui
- logging
Rules:
- automated refactors allowed