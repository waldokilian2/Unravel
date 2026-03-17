---
name: extract-process-flows
description: Use when analyzing code for function call sequences, state machine transitions, or workflow orchestrators. Automatically triggers on: function call chains, state transitions, workflow definitions, async/await sequences, promise chains.
---

# Extracting Process Flows

## Overview
Map process flows by identifying function call stacks, state machine transitions, and workflow orchestrators.

## Checklist

1. **Hotspot Discovery** - Find files with flow patterns
2. **Trace** - Follow function call chains
3. **Document** - Write to docs/output/process-flows.md
4. **Verify** - Confirm flows are complete

## Hotspot Discovery

```bash
# Find function call chains
grep -r "await.*\." --include="*.ts" --include="*.js" -l | head -20

# Find state machines
grep -r "state\|transition\|StateMachine" --include="*.ts" -l | head -20
```

## Output Format

```markdown
## Process Flows

### User Registration
1. validateInput() → createUser() → sendWelcomeEmail()
   Source: src/auth/registration.ts:45-67

### Payment Processing
1. validatePayment() → chargeCard() → updateOrder() → sendReceipt()
   Source: src/payment/process.ts:12-45
```

## Token Efficiency
- Trace one level deep unless complex workflow
- Use numbered lists for sequences
