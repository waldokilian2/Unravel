---
name: extract-data-specs
description: Use when analyzing code for data structures, schemas, or type definitions. Automatically triggers on: ORM classes, database schemas, DTOs, TypeScript interfaces, validation annotations.
---

# Extracting Data Specifications

## Overview
Document data structures, schemas, and type definitions to understand what data the system processes, how it's validated, and what constraints exist.

## When to Use
Use when analyzing code for data structures, schemas, or type definitions. Triggers on:
- ORM classes (Entity, Model, Table decorators)
- Database schema definitions
- DTOs (Data Transfer Objects)
- TypeScript interfaces/types
- Validation annotations
- Schema files (JSON Schema, GraphQL, etc.)

## Core Principle
**Schema-first: Find data definitions before reading implementation code**

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

# Find validation decorators
grep -r "@Is\|@Min\|@Max\|@Validate" --include="*.ts" -l | head -20

# Find DTOs
grep -r "DTO\|dto\|Request\|Response" --include="*.ts" --include="*.js" -l | head -20
```

Exclude generated code:
```bash
--exclude-dir=node_modules --exclude-dir=dist --exclude-dir=build --exclude-dir=.next
```

## Pattern Signals

| Pattern | Example | Data Spec |
|---------|---------|-----------|
| ORM entity | `@Entity class User` | Database table schema |
| Interface | `interface CreateUserRequest` | API contract |
| Validation | `@IsEmail() email: string` | Field constraint |
| Type decorator | `@Column({ type: 'uuid' })` | Database type |
| Enum | `enum UserRole { ADMIN, USER }` | Allowed values |

## Output Format

```markdown
## Data Specifications

Extraction: 2025-03-17

### User Entity
| Field | Type | Constraints | Source |
|-------|------|-------------|--------|
| id | UUID | PK, not null | src/models/User.ts:12 |
| email | string | unique, @Email | src/models/User.ts:15 |
| age | number | @Min(0), @Max(150) | src/models/User.ts:18 |

### CreateUserRequest DTO
| Field | Type | Required | Constraints | Source |
|-------|------|----------|-------------|--------|
| email | string | Yes | @IsEmail() | src/dto/User.dto.ts:8 |
| password | string | Yes | @MinLength(8) | src/dto/User.dto.ts:9 |
```

## Token Efficiency
- Only read files that match hotspot patterns
- Extract field summaries in tables
- Document constraints concisely
- If 50+ specs found, suggest analyzing by module
- Group related entities together

## Edge Cases
- **No patterns found**: "No data specifications detected. Check: are you in the right directory?"
- **Too many patterns**: "Large codebase detected. Analyzing module-by-module..."
- **Inherited fields**: Note "[INHERITED: Includes fields from X]"
- **Generic types**: Extract with note "[GENERIC: Type parameter T]"
- **Union types**: Extract all possible types
- **Optional fields**: Mark clearly in documentation
- **Circular references**: Flag as "CIRCULAR REFERENCE: X references Y which references X"

## Red Flags

**Never:**
- Infer field types without reading definitions
- Assume constraints exist (explicitly document only what's present)
- Skip validation annotations
- Ignore relationship definitions (foreign keys, refs)
- Document implied schemas without source verification

**Always:**
- Read actual schema/model definitions
- Document all constraints (validation, database, application)
- Include source locations
- Note relationships between entities
- Document required vs optional fields
- Extract default values if present

## Integration
- For complex files (10+ specs), dispatch agents/artifact-extractor.md
- Use business-analyst:verification-agent to verify output
