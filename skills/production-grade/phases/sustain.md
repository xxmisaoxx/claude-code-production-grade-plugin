# SUSTAIN Phase — Detailed Instructions

This file contains detailed instructions for the **SUSTAIN Agent** (Technical Writer + Skill Maker modes + Compound Learning).

---

## Technical Writer Mode

### Objective

Generate comprehensive documentation by reading ALL workspace folders. Scaffold a documentation site.

### Workflow

1. **Read all workspace outputs:**
   - BRD for product context and user stories
   - Architecture for system design and API contracts
   - Implementation for code structure and patterns
   - Tests for coverage and testing strategies
   - Security for threat model and audit results
   - DevOps for deployment and infrastructure
   - SRE for operational runbooks

2. **Generate documentation:**
   ```
   for each doc type:
     API Reference — auto-generate from OpenAPI contracts
     Developer Guide — getting started, authentication, examples
     Operational Guide — deployment, monitoring, incident response
     Architecture Guide — system design, ADRs, data flow
     Contributing Guide — code style, PR process, testing
   ```

3. **Scaffold Docusaurus site:**
   ```
   generate:
     docusaurus.config.js
     sidebars.js
     docs/ directory with all generated content
     static/ with diagrams from architecture phase
   ```

4. **Validation Loop:**
   ```
   while docs incomplete:
     verify every public API endpoint is documented
     verify every BRD user story has a corresponding guide section
     verify all code examples compile/run
     verify internal links resolve
   ```

### Output Contract

| File | Purpose |
|------|---------|
| `technical-writer/docs/` | All documentation files |
| `technical-writer/docusaurus/` | Documentation site scaffold |
| `technical-writer/api-reference/` | Auto-generated API reference |

---

## Skill Maker Mode

### Objective

Analyze the completed project's patterns and create 3-5 project-specific skills for ongoing development.

### Workflow

1. **Pattern analysis:**
   ```
   scan workspace for:
     recurring code patterns (API routes, DB queries, auth checks)
     deployment procedures
     testing patterns
     debugging patterns specific to this stack
     domain-specific workflows
   ```

2. **Skill generation:**
   ```
   for each identified pattern:
     write SKILL.md following skill-maker conventions
     include: trigger conditions, workflow steps, common mistakes
     validate against official SKILL.md spec
   ```

3. **Installation:**
   ```
   copy skills to project's .claude/skills/ directory
   verify skills are discoverable
   ```

### Output Contract

| File | Purpose |
|------|---------|
| `skill-maker/custom-skills/` | Generated project-specific skills |

---

## Compound Learning Loop

### Objective

After every pipeline run, capture lessons learned that make the NEXT run better. This is the key differentiator from dumb sequential execution.

### What to Capture

After the pipeline completes (or at any major failure), write to `.orchestrator/compound-learnings.md`:

```markdown
## Learning: [date] — [project name]

### What Worked
- [patterns, decisions, tools that worked well]

### What Failed
- [errors encountered, root causes, how they were fixed]

### Architecture Insights
- [patterns that emerged, tech stack fit/misfit]

### Time Sinks
- [phases that took longest, what slowed them down]

### Skip Next Time
- [unnecessary steps for this project type]

### Add Next Time
- [missing steps that should have been included]
```

### How to Use Learnings

At pipeline kickoff, check if `.orchestrator/compound-learnings.md` exists from a previous run:
- If yes: read it and adapt the execution plan (skip known unnecessary steps, pre-apply known fixes)
- If no: run standard pipeline

### Memory Persistence

For cross-session learning, write key insights to the project memory directory. This persists across conversations and is automatically loaded.

---

## Final Assembly

After all phases complete:

1. **Integration decision** — ask user via AskUserQuestion:
   ```python
   AskUserQuestion(questions=[{
     "question": "Code is ready. Integrate into your project root?",
     "header": "Assembly",
     "options": [
       {"label": "Integrate all code (Recommended)", "description": "Copy services, frontend, infra to project root"},
       {"label": "Keep in workspace only", "description": "Leave everything in Claude-Production-Grade-Suite/"},
       {"label": "Let me choose what to copy", "description": "Select which components to integrate"},
       {"label": "Chat about this", "description": "Discuss integration strategy"}
     ],
     "multiSelect": false
   }])
   ```

2. **Copy mapping:**
   - `software-engineer/services/` → project `src/` or `services/`
   - `frontend-engineer/app/` → project `frontend/` or `web/`
   - `devops/containers/` → project root (Dockerfiles, docker-compose)
   - `devops/ci-cd/` → `.github/workflows/` or equivalent
   - `devops/terraform/` → `terraform/` or `infra/`

3. **Final validation:**
   ```
   docker-compose up — full stack starts
   make test — all tests pass
   terraform validate — IaC is valid
   health check endpoints respond
   ```

4. **Compound learning** — capture learnings (see above)

5. **Present final summary:**

```
===== PRODUCTION GRADE — COMPLETE ========================

Project: <name>
Duration: <time from start to finish>

-- DEFINE -----------------------------------------------
 BRD: <X> user stories, <Y> acceptance criteria
 Architecture: <pattern>, <N> services
 Tech Stack: <language>, <framework>, <database>
 API Contracts: <N> endpoints defined

-- BUILD ------------------------------------------------
 Backend: <N> services implemented
 Frontend: <N> pages, <N> components (if applicable)
 Tests: <N> unit, <N> integration, <N> e2e
 Coverage: <X>%

-- HARDEN -----------------------------------------------
 Security: <N> findings (<N> auto-fixed)
 Code Review: <N> findings (<N> auto-fixed)
 Remaining: <N> medium, <N> low (documented)

-- SHIP -------------------------------------------------
 Terraform: <cloud> validated
 CI/CD: <N> pipelines (GitHub Actions)
 Docker: All images build successfully
 Monitoring: <N> dashboards, <N> alerts
 SRE: Production readiness approved

-- SUSTAIN ----------------------------------------------
 Docs: API reference, dev guides, operational docs
 Doc site: Docusaurus scaffolded
 Custom skills: <N> project-specific skills created
 Learnings: Captured for next pipeline run

Workspace: Claude-Production-Grade-Suite/
==========================================================
```

### Context Bridging

| Reads From | Key Information |
|-----------|-----------------|
| ALL workspace folders | Complete project context |
| `.orchestrator/decisions-log.md` | All user decisions |
| `.orchestrator/compound-learnings.md` | Previous run learnings (if exists) |
