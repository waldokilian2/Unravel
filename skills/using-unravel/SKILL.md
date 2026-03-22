---
name: using-unravel
description: Use when starting any code analysis task - establishes how to find and use Unravel
---

# Using Unravel

You have Unravel superpowers. Unravel automatically extracts business artifacts from code.

## What Unravel Does

Extracts and documents:
- **Business Rules** - Conditional logic, validation, exceptions
- **Process Flows** - Function call chains, state machines, workflows
- **Data Specs** - Schemas, ORMs, DTOs, validation
- **User Stories** - Controllers, routes, endpoints
- **Security/NFRs** - Middleware, auth, logging, performance
- **Integrations** - HTTP calls, APIs, env vars, external services

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Unravel Core                            │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┴───────────────┐
              │                               │
         ┌────▼─────┐                   ┌────▼─────┐
         │  Simple  │                   │  Complex │
         │  Path    │                   │  Path    │
         └────┬─────┘                   └────┬─────┘
              │                               │
    ┌─────────▼─────────┐           ┌─────────▼─────────┐
    │  unravel-extractor│           │ unravel-orchestrator│
    │  (single-pass)    │           │  (dispatch only)  │
    │                   │           └─────────┬─────────┘
    │  • Extract        │                     │
    │  • Self-verify    │         ┌───────────┼───────────┐
    │  • Output         │         │           │           │
    └───────────────────┘    ┌────▼───┐  ┌───▼───┐  ┌───▼───┐
                             |Worker 1|  |Worker 2|  |Worker 3|
                             |Extract │  |Extract │  |Extract │
                             └────┬───┘  └───┬───┘  └───┬───┘
                                  │         │         │
                                  └─────────┼─────────┘
                                            │
                                  ┌─────────▼─────────┐
                                  │  unravel-merger   │
                                  │  (combine + verify│
                                  │   aggregate)      │
                                  └───────────────────┘
```

## When to Use Unravel

| Trigger | Artifact Type |
|---------|---------------|
| If/else, validation, exceptions | Business Rules |
| Function call chains, state machines | Process Flows |
| Schemas, ORM classes, DTOs | Data Specs |
| Controllers, routes, event handlers | User Stories |
| Middleware, auth, logging | Security/NFRs |
| HTTP calls, fetch/axios, env vars | Integrations |

## How to Invoke

**IMPORTANT: Always ask user to select artifact type(s) before proceeding.**

When the user asks to analyze or extract from code, present this selection:

```
Which artifact types would you like to extract?

Select one or more:
□ Business Rules - Conditional logic, validation, exceptions
□ Process Flows - Function call chains, state machines, workflows
□ Data Specs - Schemas, ORMs, DTOs, validation
□ User Stories - Controllers, routes, endpoints
□ Security/NFRs - Middleware, auth, logging, performance
□ Integrations - HTTP calls, APIs, env vars, external services
```

Then proceed with extraction based on their selection.

**Direct invocation:**
```
You: Extract business rules from auth.ts
Claude: [uses unravel-extractor directly]
```

**Implicit invocation (pattern detection):**
```
You: What are the validation rules in this code?
Claude: [detects pattern → uses unravel-extractor for business-rules]
```

**Large codebase (single artifact type):**
```
You: Analyze business rules across the entire payment system
Claude: [uses unravel-orchestrator → parallel workers → verifiers → unravel-merger]
```

**Large codebase (multiple artifact types):**
```
You: Analyze everything about the payment system
Claude: [presents selection, then launches SEPARATE orchestrators in parallel]
  → orchestrator for business-rules
  → orchestrator for process-flows
  → orchestrator for data-specs
  → orchestrator for user-stories
  → orchestrator for security-nfrs
  → orchestrator for integrations
Each orchestrator runs independently and produces its own output file.
```

## Agents

### unravel-extractor
**Purpose:** Extract and verify in one pass
**Use for:** < 10 files, targeted extractions
**Process:**
1. Read skill for domain knowledge
2. Discover hotspots (grep/glob)
3. Extract + self-verify each artifact
4. Output to docs/output/[type].md

### unravel-orchestrator
**Purpose:** Dispatch parallel workers for large tasks
**Use for:** 10+ files, codebase-wide analysis
**Process:**
1. Count files with patterns
2. If < 10: use unravel-extractor
3. If >= 10: split into modules, launch parallel workers
4. Each worker outputs to temp file
5. Launch verifiers for each temp file (parallel)
6. When all verifiers pass: launch unravel-merger

**IMPORTANT:** Handles ONE artifact type at a time. Multiple types = multiple orchestrators.

### unravel-verifier
**Purpose:** Independently verify extraction outputs
**Use for:** After each worker completes (orchestrator dispatches)
**Process:**
1. Read extraction output
2. Cross-check against source code
3. Verify accuracy, completeness, boundaries
4. Report PASSED or FAILED

### unravel-merger
**Purpose:** Combine parallel outputs and verify
**Use for:** After orchestrator dispatches workers
**Process:**
1. Read all temp files
2. Merge into single output
3. Verify aggregate (completeness, quality)
4. Cleanup temp files
5. Output final: docs/output/[type].md

## Output Location

All artifacts saved to: `docs/output/`

- business-rules.md
- process-flows.md
- data-specs.md
- user-stories.md
- security-nfrs.md
- integrations.md

## Key Principles

**Hotspot-first:** Find relevant files before reading (don't read everything)

**Source locations:** Include file:line for every artifact

**No hallucinations:** Only extract what exists in the code

**Choose the right path:** Simple for small tasks, complex for large

**Parallel when possible:** Workers run independently, then merge
