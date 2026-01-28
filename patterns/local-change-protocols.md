# Local Change Protocols (LCP)

## Problem

Some code is dangerous to change.
The risk is often undocumented and invisible.

## Solution

A Local Change Protocol embeds binding rules directly into high-risk code.

It governs how the code may be changed.

## Characteristics

- Local to the file
- Explicit about risk
- Enforceable by tooling
- Understandable by humans and AI

## Template

```php
/**
 * LOCAL_CHANGE_PROTOCOL
 * scope: <area>
 * risk_level: <low | medium | high>
 *
 * constraints:
 *   - do_not_change: <rule>
 *
 * requires_human_review:
 *   - <condition>
 *
 * verification:
 *   - <tests>
 */
```