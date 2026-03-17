---
name: extract-user-stories
description: Use when analyzing code for user-facing features or requirements. Automatically triggers on: controller definitions, route handlers, event handlers, CLI commands.
---

# Extracting User Stories

## Overview
Derive user stories from controllers, routes, and event handlers.

## Checklist

1. **Hotspot Discovery** - Find route/controller files
2. **Derive** - Extract user intent from endpoints
3. **Document** - Write to docs/output/user-stories.md
4. **Verify** - Confirm stories make sense

## Hotspot Discovery

```bash
# Find route definitions
grep -r "@Get\|@Post\|router\." --include="*.ts" --include="*.js" -l | head -20

# Find controllers
grep -r "Controller" --include="*.ts" -l | head -20
```

## Output Format

```markdown
## User Stories

### Authentication
- As a user, I can register with email/password
  Source: POST /api/auth/register (src/controllers/auth.ts:23)
  Implementation: register() function

- As a user, I can log in and receive a JWT
  Source: POST /api/auth/login (src/controllers/auth.ts:45)
  Implementation: login() function
```
