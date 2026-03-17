---
name: extract-integrations
description: Use when analyzing code for external service integrations or API calls. Automatically triggers on: HTTP requests, fetch/axios calls, environment variables for endpoints, API key usage.
---

# Extracting Integrations

## Overview
Document external service integrations and API dependencies to understand what third-party services the system relies on, how it communicates with them, and what credentials are required.

## When to Use
Use when analyzing code for external service integrations or API calls. Triggers on:
- HTTP requests (fetch, axios, http module)
- API client libraries
- Environment variables for endpoints
- API key usage
- Webhook endpoints
- Message queue connections
- Database connections (external databases)

## Core Principle
**Dependency-first: Identify external system dependencies**

## Checklist

1. **Hotspot Discovery** - Find HTTP calls and env vars
2. **Extract** - Document external services
3. **Document** - Write to docs/output/integrations.md
4. **Verify** - Confirm all integrations captured

## Hotspot Discovery

```bash
# Find HTTP calls
grep -r "fetch\|axios\|http\.\|request" --include="*.ts" --include="*.js" -l | head -20

# Find env usage
grep -r "process\.env\|import\.meta\.env" --include="*.ts" --include="*.js" -l | head -20

# Find API clients
grep -r "client\|Client\|sdk\|SDK" --include="*.ts" --include="*.js" -l | head -20

# Find webhook/message queue
grep -r "webhook\|queue\|pubsub\|kafka\|sqs" --include="*.ts" --include="*.js" -l | head -20
```

Exclude generated code:
```bash
--exclude-dir=node_modules --exclude-dir=dist --exclude-dir=build --exclude-dir=.next
```

## Pattern Signals

| Pattern | Example | Integration |
|---------|---------|-------------|
| HTTP call | `axios.post('https://api.stripe.com')` | Stripe API |
| Env var | `process.env.SENDGRID_API_KEY` | SendGrid (API key) |
| SDK import | `import { S3Client } from '@aws-sdk'` | AWS S3 |
| Webhook handler | `app.post('/webhook/stripe')` | Stripe webhook receiver |
| Queue publish | `await queue.publish('order-created')` | Message queue |

## Output Format

```markdown
## Integrations

Extraction: 2025-03-17

### Payment Gateway
| Detail | Value | Source |
|--------|-------|--------|
| Provider | Stripe | src/payment/stripe.ts:1 |
| Endpoint | https://api.stripe.com | env: STRIPE_API_URL |
| Auth Method | API Key | env: STRIPE_API_KEY |
| SDK | stripe@14.0.0 | package.json |
| Webhook | POST /webhook/stripe | src/webhooks/stripe.ts:12 |

### Email Service
| Detail | Value | Source |
|--------|-------|--------|
| Provider | SendGrid | src/email/sendgrid.ts:1 |
| Endpoint | https://api.sendgrid.com | env: SENDGRID_URL |
| Auth Method | API Key | env: SENDGRID_API_KEY |

### Cloud Storage
| Detail | Value | Source |
|--------|-------|--------|
| Provider | AWS S3 | src/storage/s3.ts:1 |
| Region | us-east-1 | env: AWS_REGION |
| Auth Method | IAM credentials | env: AWS_ACCESS_KEY_ID |
```

## Token Efficiency
- Only read files that match hotspot patterns
- Extract integration summaries, not full implementations
- Group related endpoints together
- If 50+ integrations found, suggest analyzing by module
- Document environment variable dependencies

## Edge Cases
- **No patterns found**: "No integrations detected. Check: are you in the right directory?"
- **Too many patterns**: "Large codebase detected. Analyzing module-by-module..."
- **Dynamic endpoints**: Extract with note "[DYNAMIC: Endpoint determined at runtime]"
- **Multiple environments**: Document all environment variables used
- **Conditional integrations**: Note "[CONDITIONAL: Used only when...]"
- **Internal APIs**: Mark as "[INTERNAL: Calls internal microservice]"
- **Deprecated integrations**: Flag as "DEPRECATED: Still using X, should migrate to Y"

## Red Flags

**Never:**
- Infer API endpoints without reading actual calls
- Assume authentication methods without verification
- Document integrations without source locations
- Skip environment variable documentation
- Ignore error handling for external calls

**Always:**
- Read actual HTTP/client implementation code
- Document all required environment variables
- Include source locations
- Note authentication methods explicitly
- Document webhook receivers if present
- Include error handling patterns for each integration
- Flag missing error handling if absent

## Integration
- For complex files (10+ integrations), dispatch agents/artifact-extractor.md
- Use business-analyst:verification-agent to verify output
