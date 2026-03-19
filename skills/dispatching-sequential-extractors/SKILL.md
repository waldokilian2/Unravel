---
name: dispatching-sequential-extractors
description: Use when extracting from multiple files that write to the same output file - processes them sequentially
---

# Dispatching Sequential Extractors

## Overview

When extracting from multiple files that all write to the same output file, process them one at a time. Each file analysis appends to the same file, so they must happen sequentially to avoid conflicts.

**Core principle:** Dispatch one extractor subagent at a time. Each completes and writes to the output file before the next starts.

**Announce at start:** "I'm using the dispatching-sequential-extractors skill to analyze these files sequentially."

## When to Use

**Use when:**
- 3+ files that write to the same output file
- Files within the same artifact category (e.g., all business-rules, all user-stories)
- Each file has 5+ patterns (worth a dedicated agent)
- Need to process multiple file groups that append to one file

**Don't use when:**
- Single file extraction - use the extraction skill directly
- Need to understand cross-file relationships - use orchestrating-extractions
- Small number of patterns (< 5 per file) - single agent is faster
- Mixed artifact types - use orchestrating-extractions

## The Pattern

### 1. Identify Extraction Units

Group files by the output file they write to:

**By Module:**
- Auth module files (login.ts, register.ts, middleware.ts) → all write to business-rules.md
- Payment module files (process.ts, validate.ts, refund.ts) → all write to business-rules.md

**By File:**
- Large files with 10+ patterns each
- Each file processed one at a time

**By Pattern Group:**
- Single large file split by pattern groups
- Lines 1-100, 101-200, etc.

### 2. Create Focused Subagent Tasks

Each subagent gets:
- **Specific files:** Exact file paths to analyze
- **Artifact type:** business-rules, process-flows, etc.
- **Output format:** How to structure results
- **Clear scope:** What to extract, what to skip
- **Output location:** The shared output file to append to

### 3. Dispatch Sequentially

```
Step 1: Agent("Extract business rules from auth module")
→ Wait for completion →
Step 2: Agent("Extract business rules from payment module")
→ Wait for completion →
Step 3: Agent("Extract business rules from user module")
→ Wait for completion →
Done: All extractions complete, output file complete
```

Each agent completes and writes to the output file before the next starts.

### 4. Review Output

When all subagents complete:
1. **Run spec compliance review** on the final output
2. **Run quality review** on the final output
3. **Verify completeness** - all files represented

## Subagent Prompt Structure

Good subagent prompts are:
1. **Focused** - Specific files, specific artifact type
2. **Self-contained** - All context included
3. **Specific about output** - What format, where to save

```markdown
Extract business rules from auth module:

**Files:**
- src/auth/login.ts
- src/auth/register.ts
- src/auth/middleware.ts

**Artifact Type:** business-rules

**Your Process:**
1. Read each file
2. Extract conditional logic, validation, exception handling
3. Format as table with Rule | Source | Enforcement
4. Append to docs/output/business-rules.md

**Return:**
- Rules extracted: [count]
- Files analyzed: [list]
- Output location: docs/output/business-rules.md
```

## Common Mistakes

**❌ Too broad:** "Extract all business rules" - subagent doesn't know where
**✅ Specific:** "Extract business rules from auth module (3 files)" - focused scope

**❌ No format:** "Extract and save somewhere" - unclear output
**✅ Specific:** "Format as table, append to docs/output/business-rules.md" - clear output

**❌ Separate temp files:** Each agent writes to a different temp file - unnecessary complexity
**✅ Shared output:** Each agent appends to the same file - simpler

## When NOT to Use

**Single file:** Just use the extraction skill directly
**Small patterns:** < 5 patterns per file - single agent is faster
**Mixed artifact types:** Different types per file - use orchestrating-extractions

## Real Example

**Scenario:** Extract business rules from 15 files across 3 modules

**Files:**
- Auth: login.ts, register.ts, middleware.ts (5 files)
- Payment: process.ts, validate.ts, refund.ts (4 files)
- User: profile.ts, settings.ts, permissions.ts (6 files)

**Decision:** All write to business-rules.md - process one module at a time

**Dispatch:**
```
Step 1: Agent → Extract business rules from auth module (5 files)
→ Wait for completion →
Step 2: Agent → Extract business rules from payment module (4 files)
→ Wait for completion →
Step 3: Agent → Extract business rules from user module (6 files)
→ Wait for completion
```

**Results:**
- Agent 1: 20 rules extracted, appended to business-rules.md
- Agent 2: 25 rules extracted, appended to business-rules.md
- Agent 3: 30 rules extracted, appended to business-rules.md
- Total: 75 rules in docs/output/business-rules.md

**Review:**
- Spec compliance review on final output: Pass
- Quality review on final output: Pass

**Result:** Clean output, no file conflicts

## Key Benefits

1. **No file conflicts** - Sequential processing ensures clean writes
2. **Focus** - Each subagent has narrow scope, less context pollution
3. **Simpler** - No intermediate files or merge step needed
4. **Complete** - Output file is built incrementally, ready for review

## Verification

After all subagents complete:
1. **Review final output** - Verify completeness and accuracy
2. **Check for all modules** - Each file group represented
3. **Run quality reviews** - Spec compliance then quality
4. **Verify formatting** - File should be cleanly formatted
5. **Spot check** - Verify key patterns against source code
