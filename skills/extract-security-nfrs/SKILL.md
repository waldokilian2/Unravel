---
name: extract-security-nfrs
description: Use when analyzing code for security measures or non-functional requirements. Automatically triggers on: middleware layers, auth decorators, rate limiting, logging, error handling.
---

# Extracting Security & NFRs

## Overview
Document security measures, logging, and non-functional requirements.

## Checklist

1. **Hotspot Discovery** - Find middleware/auth files
2. **Extract** - Document security measures and NFRs
3. **Document** - Write to docs/output/security-nfrs.md
4. **Verify** - Confirm all measures captured

## Hotspot Discovery

```bash
# Find middleware
grep -r "middleware\|@UseGuards\|@RolesAllowed" --include="*.ts" -l | head -20

# Find logging
grep -r "logger\|console\.\|log" --include="*.ts" --include="*.js" -l | head -20
```

## Output Format

```markdown
## Security & Non-Functional Requirements

### Authentication
| Requirement | Implementation | Source |
|-------------|----------------|--------|
| JWT required | @UseGuards(JwtGuard) | src/auth/auth.controller.ts:5 |
| Role-based access | @RolesAllowed('admin') | src/admin/controller.ts:8 |

### Logging
| Component | Implementation | Source |
|-----------|----------------|--------|
| Request logging | Winston logger | src/middleware/logger.ts:12 |
```
