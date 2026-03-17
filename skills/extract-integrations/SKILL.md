---
name: extract-integrations
description: Use when analyzing code for external service integrations or API calls. Automatically triggers on: HTTP requests, fetch/axios calls, environment variables for endpoints, API key usage.
---

# Extracting Integrations

## Overview
Document external service integrations and API dependencies.

## Checklist

1. **Hotspot Discovery** - Find HTTP calls and env vars
2. **Extract** - Document external services
3. **Document** - Write to docs/output/integrations.md
4. **Verify** - Confirm all integrations captured

## Hotspot Discovery

```bash
# Find HTTP calls
grep -r "fetch\|axios\|http\." --include="*.ts" --include="*.js" -l | head -20

# Find env usage
grep -r "process\.env\|import\.meta\.env" --include="*.ts" --include="*.js" -l | head -20
```

## Output Format

```markdown
## Integrations

### Payment Gateway
| Detail | Value | Source |
|--------|-------|--------|
| Provider | Stripe | src/payment/stripe.ts:1 |
| Endpoint | https://api.stripe.com | env: STRIPE_API_URL |
| Auth Method | API Key | env: STRIPE_API_KEY |

### Email Service
| Detail | Value | Source |
|--------|-------|--------|
| Provider | SendGrid | src/email/sendgrid.ts:1 |
| Endpoint | https://api.sendgrid.com | env: SENDGRID_URL |
```
