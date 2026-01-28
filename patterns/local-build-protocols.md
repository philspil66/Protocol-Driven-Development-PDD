# Local Build Protocols (LBP)

## Problem

When building new code, consistency relies on tribal knowledge.
AI tools generate code quickly but lack shared context.

## Solution

A Local Build Protocol defines how a feature should be created.

LBPs exist before code does and guide scaffolding.
They are later embedded into the files they create.

## Lifecycle

1. Written outside the codebase
2. Used to generate files
3. Embedded into created files
4. Promoted or removed after merge

## Template

```sql
LOCAL_BUILD_PROTOCOL
feature: <name>

build_steps:
  - <step>

files_to_create:
  - <path>

files_to_modify:
  - <path>

constraints:
  - <design rule>

done_when:
  - <verification>
  
``` 