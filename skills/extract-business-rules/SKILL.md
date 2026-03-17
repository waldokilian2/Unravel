---
name: extract-business-rules
description: Use when analyzing code for conditional logic, validation rules, exception handling, or business constraints. Automatically triggers on: if/else chains, guard clauses, validation libraries, throw statements, regex patterns, condition checks.
---

# Extracting Business Rules

## Overview
Extract business rules from code by identifying conditional logic, validation patterns, and exception handling.

## When to Use
Use when analyzing code for conditional logic, validation rules, exception handling, or business constraints. Triggers on:
- If/else chains and guard clauses
- Validation decorators and libraries
- Exception throwing and error handling
- Regex patterns and format validation
- Condition checks and assertions

## Core Principle
**Rule-first: Extract the business constraint, not the implementation**

## Checklist

1. **Hotspot Discovery** - Find files with business rule patterns
2. **Pattern Extraction** - For each hotspot, extract the rule logic
3. **Document** - Write to docs/output/business-rules.md
4. **Verify** - Confirm all rules captured

## Hotspot Discovery

Use grep to find files with business rule patterns:

```bash
# Find conditional logic
grep -r "if.*:" --include="*.py" --include="*.ts" --include="*.js" -l | head -20

# Find validation calls
grep -r "validate\|verify\|check" --include="*.py" --include="*.ts" -l | head -20

# Find exception throwing
grep -r "throw\|raise\|throwError" --include="*.ts" --include="*.js" -l | head -20
```

Exclude generated code:
```bash
--exclude-dir=node_modules --exclude-dir=dist --exclude-dir=build --exclude-dir=.next
```

## Pattern Signals to Extract

| Pattern | Example | Business Rule |
|---------|---------|---------------|
| Guard clause | `if (age < 18) return` | Minimum age: 18 |
| Validation decorator | `@Min(1) @Max(100)` | Range: 1-100 |
| Exception throw | `throw new InvalidStateError()` | State validation required |
| Regex validation | `/^[A-Z]{2}\d{4}$/` | Format: 2 letters + 4 digits |
| Conditional check | `if (!user.canEdit)` | Permission check required |

## Output Format

```markdown
## Business Rules

Extraction: 2025-03-17

### Authentication
| Rule | Source | Enforcement |
|------|--------|-------------|
| Age must be >= 18 | src/auth.ts:45 | Guard clause returns early |
| Email required | src/user.ts:123 | Validation decorator @Email() |
| Password min 8 chars | src/auth.ts:78 | Regex /^[a-zA-Z0-9]{8,}$/ |

### Payment
| Rule | Source | Enforcement |
|------|--------|-------------|
| Amount > 0 | src/payment.ts:56 | if (amount <= 0) throw Error |
| Currency supported | src/payment.ts:89 | Set check against SUPPORTED_CURRENCIES |
```

## Token Efficiency

- Only read files that match hotspot patterns
- Extract rule summaries, not full code blocks
- Use tables for compact representation
- If 50+ rules found, suggest analyzing by module

## Edge Cases

- **No patterns found**: "No business rules detected. Check: are you in the right directory?"
- **Too many patterns**: "Large codebase detected. Analyzing module-by-module..."
- **Ambiguous logic**: Extract with note "[CONFIRM: Does this mean...?]"
- **Conflicting rules**: Flag as "CONFLICT: Same rule differs between X and Y"
- **Overly complex logic**: Extract with note "[COMPLEX: Simplify for documentation]"
- **Business rule hidden**: Extract with note "[REVIEW: May be business rule, confirm]"

## Red Flags

**Never:**
- Extract implementation details instead of business rules
- Document code mechanics without business meaning
- Skip validation logic (decorators, exceptions, checks)
- Assume rule semantics without reading context
- Document inferred rules without source verification

**Always:**
- Extract the business constraint (what), not code (how)
- Include enforcement mechanism (guard clause, decorator, exception)
- Provide source location for every rule
- Clarify ambiguous rules with [CONFIRM] notes
- Flag conflicting rules explicitly

## Integration

- For complex files (10+ rules), dispatch agents/artifact-extractor.md
- Use with business-analyst:verification-agent to verify output
