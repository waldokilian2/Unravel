---
name: using-business-analyst
description: Use when starting any business analysis task - establishes how to find and use business analyst skills
---

# Using Business Analyst Skills

You have business analysis superpowers. These skills automatically extract business artifacts from code.

## What Business Analyst Does

Automatically extracts and documents:
- **Business Rules** - Conditional logic, validation, exceptions
- **Process Flows** - Function stacks, state machines, workflows
- **Data Specs** - Schemas, ORMs, DTOs, validation annotations
- **User Stories** - Controller/route definitions imply user intent
- **Security/NFRs** - Middleware, auth, logging
- **Integrations** - HTTP calls, env vars, API keys

## How It Works

## Rule
**Before any code analysis task, check if a business analyst skill applies.**

If there's even a 1% chance a skill might apply, you MUST use it.

## When Skills Trigger

| Pattern | Triggers Skill |
|---------|----------------|
| If/else, guard clauses, validation | extract-business-rules |
| Function call chains, state machines | extract-process-flows |
| Schemas, ORM classes, DTOs | extract-data-specs |
| Controllers, routes, event handlers | extract-user-stories |
| Middleware, auth decorators, logging | extract-security-nfrs |
| HTTP requests, fetch/axios, env vars | extract-integrations |

## Workflow

1. **Identify** - Recognize code pattern matches a category
2. **Invoke** - Use matching skill
3. **Extract** - Skill guides discovery and extraction
4. **Output** - Artifacts saved to `docs/output/`
5. **Verify** - Optional verification agent review

## Key Principles

- **Hotspot-first** - Find relevant files before reading
- **Token-efficient** - Only read files with patterns
- **Accurate** - Source locations with every artifact
- **No hallucinations** - Only extract what exists

## Available Skills

- business-analyst:extract-business-rules
- business-analyst:extract-process-flows
- business-analyst:extract-data-specs
- business-analyst:extract-user-stories
- business-analyst:extract-security-nfrs
- business-analyst:extract-integrations
- business-analyst:verification-agent
