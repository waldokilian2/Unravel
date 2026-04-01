---
name: synthesize-business-spec
description: >
  This skill synthesizes ALL extracted artifacts into a comprehensive business specification document.
  Use this agent when the user asks to generate a business spec, create a master specification document,
  synthesize a complete spec, combine all extractions into one document, or mentions /synthesize-business-spec.
  This is the ultimate synthesis — it reads all 11 extraction types and produces a single stakeholder-ready
  BUSINESS-SPEC.md that serves as the complete specification for the system. Do NOT use this skill for extracting
  new artifacts; it only synthesizes existing ones.
user-invocable: true
---

# Business Specification Synthesis

You are a business specification synthesis specialist. Your job is to read ALL extracted artifacts produced by Unravel's extraction pipeline and consolidate them into a single, comprehensive business specification document that stakeholders can use as the complete reference for what the system does, how it works, and how it is built.

You do NOT extract anything from source code. You read only from the `docs/output/` directory tree, where Unravel's extraction skills have already written their results.

## Prerequisite Check (MANDATORY — Run First)

Before doing anything else, verify that ALL extraction artifacts exist. Use Glob to check for the `00-INDEX.md` file of each extraction type.

**All 11 extractions are required:**

| Type | Group | Index Path |
|------|-------|------------|
| business-rules | Business Logic | `docs/output/business-rules/00-INDEX.md` |
| user-stories | Business Logic | `docs/output/user-stories/00-INDEX.md` |
| process-flows | Business Logic | `docs/output/process-flows/00-INDEX.md` |
| data-specs | Data & Domain | `docs/output/data-specs/00-INDEX.md` |
| domain-vocabulary | Data & Domain | `docs/output/domain-vocabulary/00-INDEX.md` |
| api-contracts | Interfaces & Security | `docs/output/api-contracts/00-INDEX.md` |
| integrations | Interfaces & Security | `docs/output/integrations/00-INDEX.md` |
| security-nfrs | Interfaces & Security | `docs/output/security-nfrs/00-INDEX.md` |
| dependency-map | Architecture | `docs/output/dependency-map/00-INDEX.md` |
| test-coverage | Architecture | `docs/output/test-coverage/00-INDEX.md` |
| evolution-history | Architecture | `docs/output/evolution-history/00-INDEX.md` |

Run the following glob checks:

```
Glob: docs/output/business-rules/00-INDEX.md
Glob: docs/output/user-stories/00-INDEX.md
Glob: docs/output/process-flows/00-INDEX.md
Glob: docs/output/data-specs/00-INDEX.md
Glob: docs/output/domain-vocabulary/00-INDEX.md
Glob: docs/output/api-contracts/00-INDEX.md
Glob: docs/output/integrations/00-INDEX.md
Glob: docs/output/security-nfrs/00-INDEX.md
Glob: docs/output/dependency-map/00-INDEX.md
Glob: docs/output/test-coverage/00-INDEX.md
Glob: docs/output/evolution-history/00-INDEX.md
```

**If any prerequisite is missing**, print the following message and STOP. Do not proceed, do not attempt to synthesize, do not read any other files:

```
Cannot generate business specification. The following extractions are missing:

- [type] — Run /unravel and select [group name]
```

List only the types that are actually missing. If all prerequisites exist, proceed to the next step.

## Reading Inputs

### Step 1: Read Index Files

For each extraction type, read its `00-INDEX.md` to obtain the list of module files. The index will tell you which modules were extracted and where their output files live.

### Step 2: Read Module Files

Read all module files referenced in the index for each extraction type.

**Large artifact sets strategy (for extractions with 5+ module files):**

| Type | Strategy |
|------|----------|
| business-rules | Read all — these are the core functional requirements |
| user-stories | Read all — these define the system's capabilities |
| process-flows | Read all significant flows; skim simple ones |
| data-specs | Read core entities; use Grep for cross-references |
| domain-vocabulary | Read all for glossary; scan for entity relationships |
| api-contracts | Read all for interface spec |
| integrations | Read all — external dependencies are critical |
| security-nfrs | Read all; categorize by domain area |
| dependency-map | Read all — this is the structural backbone |
| test-coverage | Read summary; note gaps by module |
| evolution-history | Skim for deprecated items and tech debt |

### Step 3: Generate Visual Diagrams

Create three Mermaid diagrams to visualize different aspects of the system:

#### 3.1: System Context Diagram

Show the system boundary and external dependencies:

```mermaid
flowchart LR
    subgraph External
        [External Service 1]
        [External Service 2]
        [Users]
    end
    
    subgraph "System Boundary"
        [Module A]
        [Module B]
        [Module C]
    end
    
    Users --> Module A
    Module A --> External Service 1
    Module B --> External Service 2
    Module A --> Module B
    Module B --> Module C
```

#### 3.2: Component Architecture Diagram

Show internal module dependencies:

```mermaid
flowchart TD
    [Module A] --> [Module B]
    [Module A] --> [Module C]
    [Module B] --> [Module D]
```

Use subgraphs to group related modules. Highlight circular dependencies and hub modules.

#### 3.3: Entity Relationship Diagram

Show core data entities and their relationships:

```mermaid
erDiagram
    ENTITY_A ||--o{ ENTITY_B : "relationship label"
    ENTITY_A {
        string id PK
        string name
    }
```

Include 10-15 core entities (aggregate roots and central entities).

### Step 4: Cross-Reference Analysis

As you read, actively build connections between artifact types:

- **Business rules → User stories**: Each rule should relate to one or more user capabilities
- **User stories → API contracts**: Each user story should map to one or more endpoints
- **Business rules → Data entities**: Rules often validate or constrain entity fields
- **Process flows → Business rules**: Flows enforce sequences of rules
- **Security NFRs → User stories**: Auth requirements apply to specific capabilities
- **Integrations → Modules**: External services are used by specific modules
- **Domain vocabulary → Data entities**: Enums and constants define entity field values
- **API contracts → User stories**: Endpoints implement user capabilities
- **Test coverage → Business rules**: Rules should have corresponding test coverage
- **Evolution history → Current state**: Deprecated items explain design decisions

## Output

Write the synthesized business specification to:

```
docs/output/BUSINESS-SPEC.md
```

### Document Structure

```markdown
# Business Specification Document

**Generated:** [YYYY-MM-DD]
**System:** [inferred from modules/business domain]
**Source Artifacts:** All 11 extraction types

---

## Executive Summary

[2-3 paragraph executive summary synthesized from all artifacts. Describe:
- What the system does (from user-stories and business-rules)
- Who it serves (user roles from user-stories)
- How it is architected at a high level (from dependency-map)
- Key external dependencies (from integrations)
- Critical business constraints (from business-rules)
- Major areas covered (list domains from business-rules modules)]

---

## 1. System Overview

### 1.1 Business Context

[2-3 paragraphs describing the business problem this system solves, its objectives, and background. Synthesize from:
- The user capabilities (user-stories) to infer what business need exists
- Business rules to understand what constraints the business operates under
- External integrations to understand the business ecosystem
- Domain vocabulary to understand the business domain]

**Problem Statement:**
[What problem does this system solve? What pain points does it address? Inferred from the gap between current state and the capabilities provided.]

**Business Objectives:**
[What business goals does this system support? Inferred from user capabilities and business rules.]

**Business Background:**
[Context about the business domain, industry, or environment. Inferred from domain vocabulary and entity types.]

### 1.2 Purpose and Scope

[1-2 paragraphs describing the system's purpose and business scope. Synthesize from user-stories (what users can do) and business-rules (what constraints exist).]

### 1.3 Stakeholder Analysis

| Stakeholder | Interest | Influence | Primary Concerns | Source |
|-------------|----------|-----------|------------------|--------|
| [inferred role] | [what they care about] | [High/Medium/Low] | [key concerns] | `user-stories/[module].md` |

**Stakeholder Definitions:**
[Prose description of each stakeholder group and what they need from the system. Infer from user roles and capabilities.]

### 1.4 User Roles

| Role | Description | Capabilities | Source |
|------|-------------|--------------|--------|
| [role from user-stories] | [what this role is] | [count of capabilities] | `user-stories/[module].md` |

### 1.5 Scope Statement

**In Scope:**
[List what the system currently does and covers. Synthesize from all artifact types — the union of all capabilities, rules, data, and integrations.]

**Out of Scope:**
[Identify what is explicitly NOT covered based on the extracted artifacts. Look for:
- Missing capabilities that would be expected but aren't implemented
- Absence of integrations for common services (e.g., no payment processing in what would logically be an e-commerce system)
- Gaps in business rules for expected scenarios
- Unimplemented features mentioned in evolution-history (as TODO comments or deprecated stubs)]

**Scope Boundaries:**
[Where does the system start and end? What are the hard boundaries? Infer from:
- API contracts (external interface boundaries)
- Integrations (external system boundaries)
- Module structure (internal boundaries)]

### 1.6 System Context

```mermaid
flowchart LR
    [Generated system context diagram]
```

**External Dependencies:**
| Service | Purpose | Criticality | Source |
|---------|---------|-------------|--------|
| [from integrations] | [what it's for] | [High/Medium/Low] | `integrations/[module].md` |

---

## 2. Business Objectives

[Numbered list of measurable business objectives that this system supports. These should be outcome-focused statements of what the business wants to achieve, not how it's achieved. Synthesize from:
- User capabilities to understand what outcomes are enabled
- Business rules to understand what constraints exist on achieving those outcomes
- Process flows to understand efficiency or automation goals
- Domain vocabulary to understand the business domain's success concepts]

### 2.1 Primary Objectives

| ID | Objective | Success Measure | Priority | Source |
|----|-----------|-----------------|----------|--------|
| BO-[nnn] | [The system shall [enable/improve/achieve] [outcome]] | [How achievement is measured] | [Critical/High/Medium/Low] | `user-stories/[module].md` |

**Example patterns to look for:**
- User story "As a user, I can export reports" → infer objective: "Enable users to access data for external analysis"
- Process flow "Automated batch processing" → infer objective: "Reduce manual processing time by X%"
- Business rule "Orders must be processed within 24 hours" → infer objective: "Maintain service level commitments"

### 2.2 Secondary Objectives

| ID | Objective | Success Measure | Priority | Source |
|----|-----------|-----------------|----------|--------|
| BO-[nnn] | [Supporting or enabling objective] | [How measured] | [High/Medium/Low] | inferred from artifacts |

[If no explicit objectives can be inferred, state: "No explicit business objectives identified in extracted artifacts. Recommend defining measurable objectives for: [list key functional areas]." ]

---

## 3. Success Criteria

[Define how success is measured for this system. These are business metrics and outcomes, not technical metrics. Infer from:
- Business rules that suggest performance or volume requirements
- User stories that suggest throughput or availability needs
- Process flows that suggest time-sensitive operations
- Integration criticality that suggests business impact]

### 3.1 Key Performance Indicators

| KPI | Target | How Measured | Source |
|-----|--------|--------------|--------|
| [inferred metric] | [target value] | [measurement approach] | `user-stories/[module].md` |

**Examples to look for in artifacts:**
- "Process payments within 5 seconds" → infer KPI: Payment processing latency < 5s
- "Handle 10,000 concurrent users" → infer KPI: Concurrent user capacity: 10,000
- "99.9% uptime required" → infer KPI: System availability: 99.9%
- "Daily batch job completes by 6 AM" → infer KPI: Batch job completion: 100% by 6 AM

### 3.2 Success Metrics by Domain

| Domain | Success Metric | Measurement | Source |
|--------|----------------|-------------|--------|
| [domain area] | [what success looks like] | [how measured] | inferred from artifacts |

[If no explicit success criteria can be inferred from the artifacts, state: "No explicit success criteria identified in the extracted artifacts. Recommend defining KPIs for the following domains: [list domains from business-rules modules]." ]

---

## 4. Assumptions and Constraints

### 4.1 Business Assumptions

[Assumptions about the business environment, users, or context that the system relies on. Look for:
- Implicit assumptions in business rules (e.g., "all users have email addresses" suggests email ownership assumption)
- User story assumptions (e.g., "users are authenticated" assumes authentication infrastructure exists)
- Data model assumptions (e.g., "one user per email" assumes email uniqueness)
- Integration assumptions (e.g., "Stripe processes payments" assumes Stripe availability and business relationship)]

| Assumption | Impact | If Wrong | Source |
|------------|--------|----------|--------|
| [inferred assumption] | [what depends on this] | [consequence if false] | `artifact-type/[module].md` |

### 4.2 Technical Assumptions

[Technical infrastructure or environmental assumptions. Look for:
- Platform assumptions (browser types, device types, OS requirements)
- Network assumptions (always-on connectivity, bandwidth requirements)
- Third-party service assumptions (API availability, uptime commitments)
- Data assumptions (data volume, growth rate, retention period)]

| Assumption | Impact | If Wrong | Source |
|------------|--------|----------|--------|
| [inferred assumption] | [what depends on this] | [consequence if false] | `integrations/[module].md` |

### 4.3 Constraints

[Limitations that the system must operate within. Categorize as:]

**Technical Constraints:**
| Constraint | Description | Impact | Source |
|------------|-------------|--------|--------|
| [from dependencies/platform] | [what limits exist] | [how this constrains the system] | `dependency-map/[module].md` |

**Business Constraints:**
| Constraint | Description | Impact | Source |
|------------|-------------|--------|--------|
| [from business rules] | [business-imposed limits] | [how this constrains the system] | `business-rules/[module].md` |

**Regulatory Constraints:**
| Constraint | Description | Impact | Source |
|------------|-------------|--------|--------|
| [if inferred from domain] | [regulatory requirements] | [how this constrains the system] | `security-nfrs/[module].md` |

[If a section has no content, state "None identified" rather than leaving it empty.]

---

## 5. Technical Requirements

[Platform, language, framework, and infrastructure requirements. Synthesize from dependency-map, integrations, and evolution-history.]

### 5.1 Platform & Framework

| Requirement | Current | Notes | Source |
|-------------|---------|-------|--------|
| Language | [from package.json, go.mod, etc.] | [primary language(s)] | `dependency-map/[module].md` |
| Framework | [major framework(s) used] | [e.g., NestJS, Django, Spring] | `dependency-map/[module].md` |
| Runtime | [Node.js, Python, Go, etc.] | [version if specified] | `dependency-map/[module].md` |
| Database | [PostgreSQL, MongoDB, etc.] | [persistence layer] | `integrations/[module].md` |

### 5.2 External Dependencies

| Dependency | Version | Type | Criticality | Purpose | Source |
|------------|---------|------|-------------|---------|--------|
| [from dependency-map] | [version constraint] | [runtime/dev] | [High/Medium/Low] | [what it's for] | `dependency-map/[module].md` |

**Key Dependencies:**
[List only the most significant dependencies — those that are:
- Security-critical (auth, encryption)
- Business-critical (payment, core services)
- High-volume (data processing, messaging)
- Or have upgrade/change implications]

### 5.3 Infrastructure Requirements

| Aspect | Requirement | Implementation | Source |
|--------|-------------|----------------|--------|
| Compute | [requirements inferred] | [current approach] | `dependency-map/[module].md` |
| Storage | [data volume, retention] | [current approach] | `integrations/[module].md` |
| Network | [bandwidth, connectivity] | [current approach] | `integrations/[module].md` |
| Security | [encryption, secrets] | [current approach] | `security-nfrs/[module].md` |

### 5.4 Build & Deployment

| Aspect | Tool/Process | Notes | Source |
|--------|-------------|-------|--------|
| Build system | [from dependency-map] | [e.g., npm, webpack, gradle] | `dependency-map/[module].md` |
| Packaging | [how it's deployed] | [e.g., Docker, jar, zip] | inferred |
| Deployment | [deployment approach] | [e.g., k8s, EC2, on-prem] | `evolution-history/[module].md` |

[If deployment approach cannot be inferred, state: "Deployment approach not explicitly documented. Recommend documenting: build process, packaging, deployment targets, and deployment procedure."]

---

## 6. Functional Requirements

[Organized by business domain area, not by artifact type. For each domain area from business-rules modules:]

### 2.1 [Domain Area Name]

[2-3 sentence prose summary of what this domain area covers and why it matters to the business.]

#### User Capabilities

| ID | Capability | Endpoint | Role | Source |
|----|------------|----------|------|--------|
| US-[nnn] | As a [role], I can [action] | [METHOD /path if applicable] | [role] | `user-stories/[module].md` |

#### Business Rules

| ID | Rule | Impact | Enforcement | Source |
|----|------|--------|-------------|--------|
| FR-[nnn] | [rule text] | [Critical/High/Medium/Low] | [mechanism] | `business-rules/[module].md` |

#### Process Context

```mermaid
flowchart TD
    [Generated flowchart for key process in this domain]
```

[For each significant process flow in this domain:]
- **[Flow name]:** [1-2 sentence summary]
- Key decisions: [list]
- Related rules: [FR-IDs]
- Related capabilities: [US-IDs]

---

## 7. Data Model

### 7.1 Core Entities

```mermaid
erDiagram
    [Generated ER diagram]
```

### 7.2 Entity Reference

[Alphabetical list of core entities from data-specs, enriched with domain vocabulary.]

#### [Entity Name]
[1-2 sentence summary from data-specs, enriched with domain meaning.]

| Field | Type | Constraints | Domain Meaning | Source |
|-------|------|-------------|---------------|--------|
| [field] | [type] | [constraints] | [from domain-vocab] | `data-specs/[module].md` |

**Relationships:**
| To Entity | Type | Description | Source |
|-----------|------|-------------|--------|
| [entity] | [One-to-many/etc] | [business meaning] | `data-specs/[module].md` |

---

## 8. Interfaces & APIs

### 8.1 API Endpoints

[Grouped by business domain or functional area.]

#### [Domain Area] Endpoints

##### [METHOD] [path]

| Detail | Value |
|--------|-------|
| Purpose | [what this endpoint does, from user-stories] |
| Auth | [required/none, specific role] |
| Success Status | [HTTP code] |

**Request:**
| Field | Location | Type | Required | Constraints |
|-------|----------|------|----------|-------------|
| [field] | [body/query/param] | [type] | [yes/no] | [validation] |

**Response:**
| Field | Type | Description |
|-------|------|-------------|
| [field] | [type] | [what it represents] |

**Error Responses:**
| Status | Condition |
|--------|-----------|
| [code] | [when this occurs] |

[Repeat for each endpoint in api-contracts, grouped logically.]

### 8.2 Webhooks & Events

| Webhook/Event | Direction | Trigger | Payload | Source |
|---------------|-----------|---------|---------|--------|
| [name] | [inbound/outbound] | [when fired] | [shape] | `integrations/[module].md` or `api-contracts/[module].md` |

---

## 9. Non-Functional Requirements

### 9.1 Security

| ID | Requirement | Priority | Implementation | Source |
|----|-------------|----------|----------------|--------|
| NFR-SEC-[nnn] | [requirement] | [High/Medium/Low] | [how it's implemented] | `security-nfrs/[module].md` |

### 9.2 Compliance & Legal

[Regulatory, legal, and compliance requirements. Look for:
- Data protection requirements (GDPR, CCPA, data residency)
- Industry regulations (HIPAA for healthcare, PCI-DSS for payments, SOX for finance)
- Audit requirements (logging, traceability, data retention)
- Privacy requirements (data minimization, right to deletion, consent management)
- Accessibility requirements (WCAG compliance, screen reader support)
- Certification requirements (SOC2, ISO 27001, etc.)

If no explicit compliance requirements are found in artifacts, state: "No explicit compliance requirements identified in extracted artifacts. Verify applicable regulations for the business domain: [inferred industry/domain]." ]

| Requirement | Regulation/Standard | Description | Implementation Status | Source |
|-------------|---------------------|-------------|------------------------|--------|
| [inferred or stated] | [regulation name] | [what's required] | [Implemented/Partial/None] | `security-nfrs/[module].md` |

**Data Protection & Privacy:**
| Aspect | Requirement | Implementation | Source |
|--------|-------------|----------------|--------|
| Data at rest encryption | [requirement] | [status] | `security-nfrs/[module].md` |
| Data in transit encryption | [requirement] | [status] | `security-nfrs/[module].md` |
| Data retention | [requirement] | [status] | `security-nfrs/[module].md` |
| Right to deletion | [requirement] | [status] | `security-nfrs/[module].md` |
| Data export | [requirement] | [status] | `security-nfrs/[module].md` |

**Audit & Logging Requirements:**
| Requirement | Description | Implementation | Source |
|-------------|-------------|----------------|--------|
| [audit requirement] | [what must be logged/audited] | [status] | `security-nfrs/[module].md` |

### 9.3 Reliability & Performance

| ID | Requirement | Priority | Implementation | Source |
|----|-------------|----------|----------------|--------|
| NFR-REL-[nnn] | [requirement] | [High/Medium/Low] | [how it's implemented] | `security-nfrs/[module].md` |

### 9.4 Operational Requirements

[Operational and deployment requirements from a business perspective. Look for:
- Uptime/availability commitments (SLAs)
- Disaster recovery requirements
- Backup and restore requirements
- Deployment frequency/maintenance windows
- Monitoring and alerting requirements
- Capacity planning requirements
- Geographic/multi-region requirements]

| Requirement | Target | Measurement | Implementation | Source |
|-------------|--------|-------------|----------------|--------|
| [inferred SLA] | [target value] | [how measured] | [how implemented] | `security-nfrs/[module].md` |

**Service Level Agreements (SLAs):**
| Service | Availability Metric | Target | Current Capability | Source |
|---------|---------------------|--------|-------------------|--------|
| [system/API] | [e.g., uptime %] | [target] | [current status] | `security-nfrs/[module].md` |

**Disaster Recovery:**
| Aspect | Requirement | Implementation | Source |
|--------|-------------|----------------|--------|
| Backup frequency | [requirement] | [status] | `security-nfrs/[module].md` |
| Recovery time objective (RTO) | [requirement] | [status] | `security-nfrs/[module].md` |
| Recovery point objective (RPO) | [requirement] | [status] | `security-nfrs/[module].md` |
| Geographic redundancy | [requirement] | [status] | `integrations/[module].md` |

**Monitoring & Alerting:**
| Aspect | Requirement | Implementation | Source |
|--------|-------------|----------------|--------|
| Health checks | [requirement] | [status] | `security-nfrs/[module].md` |
| Performance monitoring | [requirement] | [status] | `security-nfrs/[module].md` |
| Error alerting | [requirement] | [status] | `security-nfrs/[module].md` |

[If no explicit operational requirements are found, state: "No explicit operational requirements identified. Recommend defining SLAs, DR targets, and monitoring requirements."]

### 9.5 Error Handling & Logging

| ID | Requirement | Priority | Implementation | Source |
|----|-------------|----------|----------------|--------|
| NFR-ERR-[nnn] | [requirement] | [High/Medium/Low] | [how it's implemented] | `security-nfrs/[module].md` |

---

## 10. System Architecture

### 10.1 Component Architecture

```mermaid
flowchart TD
    [Generated component diagram]
```

### 10.2 Module Structure

| Module | Role | Dependencies | External Services | Data Entities | Source |
|--------|------|--------------|-------------------|---------------|--------|
| [name] | [purpose] | [internal deps] | [external deps] | [entities] | `dependency-map/[module].md` |

### 10.3 Dependency Analysis

| Module | Depends On | Coupling | Concerns |
|--------|-----------|----------|----------|
| [module] | [list] | [High/Medium/Low] | [if any] |

**Coupling Criteria:**
- **High:** 4+ internal dependencies, circular dependencies, or shared infrastructure
- **Medium:** 2-3 internal dependencies, no circular deps
- **Low:** 0-1 internal dependencies, or only leaf/utility dependencies

---

## 11. Domain Glossary

| Term | Definition | Type | Source |
|------|-----------|------|--------|
| [term] | [definition] | [enum/constant/concept/error] | `domain-vocabulary/[module].md` |

---

## 12. Integrations & Configuration

### 8.1 External Services

| Service | Purpose | Protocol | Auth Method | Criticality | Source |
|---------|---------|----------|-------------|-------------|--------|
| [service] | [what for] | [HTTP/gRPC/etc] | [API Key/OAuth/etc] | [High/Medium/Low] | `integrations/[module].md` |

### 8.2 Configuration Requirements

| Variable | Required | Default | Description | Source |
|----------|----------|---------|-------------|--------|
| [VAR_NAME] | [yes/no] | [default] | [purpose] | `integrations/[module].md` |

### 8.3 Notifications

| Type | Trigger | Template | Recipients | Source |
|------|---------|----------|------------|--------|
| [email/SMS/push] | [when sent] | [name] | [who] | `integrations/[module].md` |

---

## 13. Testing Status

### 11.1 Coverage by Domain

| Domain Area | Coverage Status | Gaps | Source |
|-------------|-----------------|------|--------|
| [area] | [Good/Partial/None] | [what's missing] | `test-coverage/[module].md` |

### 11.2 Critical Untested Rules

| Rule ID | Rule | Risk | Source |
|---------|------|------|--------|
| [FR-nnn] | [rule text] | [why untested is risky] | `test-coverage/[module].md` |

---

## 14. Risks & Dependencies

### 12.1 Business Risks

[Risks from a business perspective that could impact the system's success or operation. Look for:
- Single points of failure in business processes (from process-flows)
- Critical external dependencies (from integrations)
- Gaps in business rules that suggest incomplete validation
- Missing capabilities that could impact business operations
- Dependencies on specific third-party services or providers]

| Risk | Category | Likelihood | Impact | Mitigation | Source |
|------|----------|------------|--------|------------|--------|
| [inferred risk] | [business/operational/strategic] | [High/Medium/Low] | [High/Medium/Low] | [how to address] | `artifact-type/[module].md` |

**Risk Categories:**
- **Operational Risks:** Day-to-day operational concerns (e.g., payment service downtime)
- **Strategic Risks:** Long-term business impact (e.g., vendor lock-in)
- **Compliance Risks:** Regulatory or legal exposure
- **Technical Debt Risks:** Known issues that could impact operations

### 12.2 External Dependencies

[Critical external services and their business impact. Look for:
- Services marked as "critical" in integrations
- Services without fallback or redundancy
- Services with single points of failure
- Services with business or financial dependencies]

| Service | Dependency Type | Criticality | Risk Level | Fallback Available | Source |
|---------|-----------------|-------------|------------|-------------------|--------|
| [service name] | [infrastructure/data/business] | [High/Medium/Low] | [High/Medium/Low] | [Yes/No] | `integrations/[module].md` |

**Dependency Analysis:**
| Dependency | Business Impact | If Unavailable | Mitigation | Source |
|------------|-----------------|----------------|------------|--------|
| [external service] | [what business impact] | [consequence] | [fallback plan] | `integrations/[module].md` |

### 12.3 Data Migration & Transition Risks

[Risks related to data handling, migration, or system transitions. Look for:
- Deprecated items in evolution-history that suggest migration needs
- Data model complexity that suggests migration challenges
- Integration patterns that suggest data synchronization needs]

| Risk | Area | Impact | Mitigation | Source |
|------|------|--------|------------|--------|
| [inferred risk] | [data migration/sync/compatibility] | [impact] | [how to address] | `evolution-history/[module].md` |

---

## 15. Technical Debt & Evolution

### 13.1 Deprecated Items

| Item | Deprecation | Migration Path | Source |
|------|-------------|----------------|--------|
| [what] | [deprecated when/why] | [how to migrate] | `evolution-history/[module].md` |

### 13.2 Known Technical Debt

| Area | Issue | Impact | Source |
|------|-------|--------|--------|
| [module/area] | [what's wrong] | [impact] | `evolution-history/[module].md` |

---

## 16. Traceability Matrix

| Requirement ID | Type | Domain | Source Artifact | Module |
|----------------|------|--------|-----------------|--------|
| FR-001 | Business Rule | [domain] | business-rules/[module].md | [module] |
| US-001 | User Story | [domain] | user-stories/[module].md | [module] |
| NFR-SEC-001 | Security | — | security-nfrs/[module].md | [module] |

[Every requirement listed in the document must appear in this matrix.]

---

## 17. Gaps & Recommendations

### 17.1 Documentation Gaps

[List observations about missing connections or undocumented items.]

### 17.2 Architecture Concerns

[List concerns from dependency analysis, circular deps, coupling issues.]

### 17.3 Security Considerations

[List security gaps or areas needing attention.]

### 17.4 Compliance Gaps

[List compliance requirements that may be missing or incomplete based on the inferred business domain. For example:
- "No explicit data retention policy found — recommend defining retention periods for [entity types]"
- "No audit logging for [sensitive operations] — recommend implementing for compliance"
- "No consent management mechanism found — may be required for GDPR compliance"]

### 17.5 Operational Gaps

[List operational concerns such as:
- Missing monitoring or alerting
- No defined backup strategy
- No disaster recovery plan
- Missing health checks]

### 17.6 Recommendations

[Prioritized list of recommendations for improvement, synthesized from all gap analyses. Group by priority:]

**High Priority:**
- [Critical issues that should be addressed soon]

**Medium Priority:**
- [Important improvements that should be planned]

**Low Priority:**
- [Nice-to-have improvements]

---

## 18. Decisions & Open Questions

[This section tracks architectural and business decisions made during development, and open questions that need resolution.]

### 18.1 Decisions

[Decisions are significant choices that shaped the system design or implementation. Look for:
- Deprecated items that suggest previous design choices (from evolution-history)
- Architecture patterns in dependency-map that represent design decisions
- Technology choices in external dependencies
- Business rule implementations that represent policy decisions]

| ID | Decision | Rationale | Impact | Source |
|----|----------|-----------|--------|--------|
| D-[nnn] | [What was decided] | [Why this choice was made] | [What this affects] | `artifact-type/[module].md` |

**Example patterns to look for:**
- "PostgreSQL chosen over MongoDB" → D001: Database technology selection
- "JWT-based authentication" → D002: Authentication approach
- "Message queue for async processing" → D003: Processing architecture
- "Deprecated module X in favor of Y" → D004: Refactoring decision

[If no explicit decisions can be inferred, state: "No explicit design decisions documented in extracted artifacts. Consider documenting: architectural patterns, technology choices, and significant design tradeoffs."]

### 18.2 Open Questions

[Questions that need answers before implementation can proceed or be considered complete. Look for:
- TODO comments in evolution-history
- Stub implementations or placeholder code
- Missing business rules for expected scenarios
- Ambiguous or incomplete process flows]

| ID | Question | Category | Priority | Source |
|----|----------|----------|----------|--------|
| Q-[nnn] | [What needs to be decided or clarified] | [Business/Technical/Operational] | [High/Medium/Low] | `artifact-type/[module].md` |

**Categories:**
- **Business:** Questions about requirements, scope, or business rules
- **Technical:** Questions about implementation, architecture, or technology
- **Operational:** Questions about deployment, monitoring, or processes

[If no open questions are identified, state: "No open questions identified in extracted artifacts. Verify with stakeholders that all requirements are clear and complete."]

---

## 19. Approval and Sign-off

[This section requires human input and is not auto-generated. Placeholders are provided for stakeholders to review and approve the specification.]

### 19.1 Approval Status

| Role | Name | Status | Date | Comments |
|------|------|--------|------|----------|
| Product Owner | `[TBC]` | `[Pending / Approved / Rejected]` | `[YYYY-MM-DD]` | `[Optional comments]` |
| Business Analyst | `[TBC]` | `[Pending / Approved / Rejected]` | `[YYYY-MM-DD]` | `[Optional comments]` |
| Solution Architect | `[TBC]` | `[Pending / Approved / Rejected]` | `[YYYY-MM-DD]` | `[Optional comments]` |
| Lead Developer | `[TBC]` | `[Pending / Approved / Rejected]` | `[YYYY-MM-DD]` | `[Optional comments]` |
| QA Lead | `[TBC]` | `[Pending / Approved / Rejected]` | `[YYYY-MM-DD]` | `[Optional comments]` |
| Compliance/Risk | `[TBC]` | `[Pending / Approved / Rejected]` | `[YYYY-MM-DD]` | `[Optional comments]` |

### 19.2 Sign-off Notes

[Stakeholders may add notes or conditions for their approval:]

**Product Owner:**
```
[Approval conditions or notes]
```

**Solution Architect:**
```
[Approval conditions or notes]
```

**[Other roles as needed]**

### 19.3 Change History

| Version | Date | Changed By | Changes | Approval |
|---------|------|------------|---------|----------|
| 1.0 | `[YYYY-MM-DD]` | `[Author]` | `Initial specification from code analysis` | `[Pending]` |
| | | | | |

---

## Appendix A: Error Catalog

| Error Code | Error Type | HTTP Status | Description | User Message | Source |
|------------|------------|-------------|-------------|--------------|--------|
| [code] | [class] | [status] | [what it means] | [user-facing] | `domain-vocabulary/[module].md` |

---

## Appendix B: Enum & Constant Reference

| Name | Values | Usage | Source |
|------|--------|-------|--------|
| [enum/constant] | [values] | [where used] | `domain-vocabulary/[module].md` |

---

*Generated by Unravel on [YYYY-MM-DD HH:MM]*
```

## ID Numbering

- **US-[nnn]:** Sequential for user stories across all domains. Start at 001.
- **FR-[nnn]:** Sequential for business rules (functional requirements). Start at 001.
- **BO-[nnn]:** Sequential for business objectives. Start at 001.
- **TR-[nnn]:** Sequential for technical requirements. Start at 001.
- **NFR-SEC-[nnn]:** Sequential for security NFRs. Start at 001.
- **NFR-COM-[nnn]:** Sequential for compliance & legal NFRs. Start at 001.
- **NFR-REL-[nnn]:** Sequential for reliability/performance NFRs. Start at 001.
- **NFR-OPS-[nnn]:** Sequential for operational requirements (SLAs, DR, monitoring). Start at 001.
- **NFR-ERR-[nnn]:** Sequential for error handling/logging NFRs. Start at 001.
- **D-[nnn]:** Sequential for decisions. Start at 001.
- **Q-[nnn]:** Sequential for open questions. Start at 001.

## Core Principles

**Synthesize, don concatenate.** The value of this document is integration — bringing together information from all 11 artifact types into a coherent narrative. A stakeholder should be able to read a domain section and see the capabilities, rules, processes, data, and APIs all in one place.

**Organize by domain, not artifact type.** Business stakeholders think in terms of "Order Management" or "User Authentication," not "business rules plus user stories plus API contracts." Group functional requirements by business domain area.

**Cross-reference relentlessly.** Every section should link to related sections. User capabilities should reference the business rules they enforce. Process flows should reference the rules and capabilities they implement. API endpoints should reference the user capabilities they provide.

**Write for business stakeholders.** Use plain language. Explain the "why" before the "what." A product manager should be able to read the Executive Summary and Functional Requirements sections without technical help.

**Every claim must have a source.** Every row in every table must include a source reference pointing to the extraction file it came from. This enables traceability and verification.

**Flag gaps explicitly.** The Gaps & Recommendations section is critical. Surface missing connections, undocumented items, architectural concerns, and areas needing improvement.

**Be comprehensive but concise.** This document will be long by nature (it combines 11 artifact types), but within each section, be concise. Use tables for structured data; use prose for narrative explanation.

**Use diagrams effectively.** The three diagrams (System Context, Component Architecture, Entity Relationships) provide visual anchors that help stakeholders understand the system at different levels of abstraction.

**Preserve impact assessments.** Carry through Critical/High/Medium/Low impact levels from business-rules and security-nfrs into priority assessments in the document.

**Handle empty sections gracefully.** If a section has no content (e.g., no webhooks), state "None" rather than leaving it empty. If an extraction produced no artifacts for a type, note it in Gaps.

**De-duplicate intelligently.** If the same business rule appears in multiple modules, include it once in the most relevant domain and note additional sources in the traceability matrix.

## Edge Cases

- **Missing artifact types:** The prerequisite check handles this — STOP and instruct the user to run missing extractions.
- **Single-module projects:** Sections will have one entry. Adjust language accordingly.
- **No external integrations:** State "No external service integrations detected" in the appropriate table.
- **Sparse test coverage:** State what coverage exists and flag gaps prominently.
- **Conflicting information:** Report both perspectives and note discrepancies in Gaps & Recommendations.
- **Very large systems:** For systems with 20+ modules or entities, focus on core/significant items in diagrams and summaries. Include all items in tables but may need to summarize some areas.

## After Generation

After writing BUSINESS-SPEC.md, print a summary message:

```
Business specification document generated: docs/output/BUSINESS-SPEC.md

Summary:
- [N] functional requirements across [M] domain areas
- [N] user capabilities
- [N] business objectives defined
- [N] technical requirements identified
- [N] stakeholder groups identified
- [N] success criteria/KPIs defined
- [N] business and technical assumptions documented
- [N] non-functional requirements (security, compliance, reliability, operational, error handling)
- [N] core data entities
- [N] API endpoints
- [N] external service integrations
- [N] business risks identified
- [N] design decisions documented
- [N] open questions requiring resolution
- [N] modules with [X] high coupling concerns
- [N] identified gaps/recommendations

Next steps:
- Review the Executive Summary and Business Context for accuracy
- Verify Business Objectives align with stakeholder expectations
- Review Technical Requirements with architecture team
- Verify Success Criteria align with business objectives
- Check the Risks & Dependencies section for mitigation planning
- Review Decisions & Open Questions - resolve or escalate open questions
- Review Compliance & Legal section for regulatory alignment
- Complete Approval & Sign-off section with relevant stakeholders
- Check the Gaps & Recommendations section for prioritized improvements
- Use the Traceability Matrix to verify all requirements are documented
- Share with stakeholders for review and approval
