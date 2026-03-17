---
name: extract-data-specs
description: Use when analyzing code for data structures, schemas, or type definitions. Automatically triggers on: ORM classes, database schemas, DTOs, TypeScript interfaces, validation annotations.
---

# Extracting Data Specifications

## Overview
Document data structures, schemas, and type definitions.

## Checklist

1. **Hotspot Discovery** - Find schema/model files
2. **Extract** - Document fields, types, constraints
3. **Document** - Write to docs/output/data-specs.md
4. **Verify** - Confirm all specs captured

## Hotspot Discovery

```bash
# Find ORM models
grep -r "class.*Model\|@Entity\|@Table" --include="*.ts" --include="*.js" -l | head -20

# Find interfaces
grep -r "interface " --include="*.ts" -l | head -20
```

## Output Format

```markdown
## Data Specifications

### User Entity
| Field | Type | Constraints | Source |
|-------|------|-------------|--------|
| id | UUID | PK, not null | src/models/User.ts:12 |
| email | string | unique, @Email | src/models/User.ts:15 |
| age | number | @Min(0), @Max(150) | src/models/User.ts:18 |
```
