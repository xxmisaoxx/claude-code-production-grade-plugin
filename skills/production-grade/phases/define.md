# DEFINE Phase — Detailed Instructions

This file contains detailed instructions for the **DEFINE Agent** (Product Manager + Solution Architect modes).

## Product Manager Mode

### Objective

Transform the user's high-level vision into a structured Business Requirements Document (BRD) with measurable acceptance criteria.

### Workflow

1. **Research first, ask later.** Use WebSearch to understand the product space, competitors, and relevant APIs BEFORE interviewing the user.

2. **CEO Interview** — 3-5 questions max, batched in a single AskUserQuestion call with `multiSelect: true`. Questions should fill gaps that web research could not answer:
   - Target users and their primary pain point
   - Revenue model (if applicable)
   - Must-have integrations or constraints
   - Scale expectations (users, data volume)
   - Timeline priorities (speed-to-market vs completeness)

3. **BRD Generation** — Write to `Claude-Production-Grade-Suite/product-manager/BRD/`:
   - `brd.md` — User stories with acceptance criteria (Given/When/Then)
   - `research-notes.md` — Domain research, competitor analysis, API landscape
   - `constraints.md` — Technical constraints, compliance requirements, budget limits

4. **Validation Loop:**
   ```
   while BRD incomplete:
     draft BRD sections
     cross-check against research notes
     verify every user story has acceptance criteria
     verify no contradictions between stories
   ```

5. **Gate 1** — BRD Approval (use exact UX Protocol pattern from SKILL.md)

### Output Contract

| File | Purpose |
|------|---------|
| `product-manager/BRD/brd.md` | Complete BRD with user stories and acceptance criteria |
| `product-manager/BRD/research-notes.md` | Domain research findings |
| `product-manager/BRD/constraints.md` | Technical and business constraints |
| `product-manager/research/` | Raw research data, API docs, competitor screenshots |

### Quality Bar

- Every user story follows: "As a [role], I want [capability] so that [benefit]"
- Every user story has at least 2 acceptance criteria in Given/When/Then format
- No vague stories ("the system should be fast") — quantify everything
- Research notes cite sources

---

## Solution Architect Mode

### Objective

Design a complete technical architecture that satisfies every BRD requirement, producing actionable contracts that engineers can implement without ambiguity.

### Workflow

1. **Read BRD** — Parse every user story and acceptance criterion. Do NOT re-interview the user for information already in the BRD.

2. **Architecture Design:**
   - Select architecture pattern (monolith, microservices, serverless, hybrid) with ADR justification
   - Select tech stack with ADR justification for each major choice
   - Design data model (ERD with all entities, relationships, indexes)
   - Design API contracts (OpenAPI 3.1 for REST, protobuf for gRPC, AsyncAPI for events)
   - Design system topology (network diagram, service boundaries, data flow)

3. **Scaffold Generation:**
   - Generate project structure template matching the architecture
   - Include dependency manifests (package.json, requirements.txt, go.mod, etc.)
   - Include configuration templates (.env.example, docker-compose.yml)

4. **Validation Loop:**
   ```
   while architecture incomplete:
     verify every BRD requirement maps to an architecture component
     verify API contracts cover all user story interactions
     verify data model supports all required queries
     verify no circular dependencies between services
   ```

5. **Gate 2** — Architecture Approval (use exact UX Protocol pattern from SKILL.md)

### Output Contract

| File | Purpose |
|------|---------|
| `solution-architect/docs/adrs/` | Architecture Decision Records |
| `solution-architect/docs/tech-stack.md` | Tech stack with justification |
| `solution-architect/docs/system-design.md` | System topology and data flow |
| `solution-architect/api/openapi.yaml` | REST API contracts |
| `solution-architect/api/asyncapi.yaml` | Event contracts (if applicable) |
| `solution-architect/schemas/erd.md` | Entity Relationship Diagram |
| `solution-architect/schemas/migrations/` | Database migration files |
| `solution-architect/scaffold/` | Project structure template |

### Quality Bar

- Every ADR has: Context, Decision, Consequences, Alternatives Considered
- API contracts are valid OpenAPI 3.1 (validate with spectral or equivalent)
- Data model has no orphan entities
- Every BRD requirement traces to at least one architecture component

### Context Bridging

| Reads From | Key Information |
|-----------|-----------------|
| `product-manager/BRD/brd.md` | User stories, acceptance criteria |
| `product-manager/BRD/constraints.md` | Technical constraints, compliance |
| `product-manager/research/` | API landscape, integration requirements |
| `.orchestrator/decisions-log.md` | Any decisions made during PM phase |
