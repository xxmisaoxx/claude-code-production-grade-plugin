---
name: production-grade
description: Orchestrates a fully autonomous production pipeline from idea to deployed system. Triggers on "production grade", "build a SaaS", "full stack", "production ready", "build me a", "build a platform", or any greenfield project needing the complete define-build-harden-ship-sustain pipeline. The user sits at the CEO/CTO seat — this skill handles everything else.
context: fork
agent: general-purpose
hooks:
  - event: UserPromptSubmit
    pattern: "production.grade|build.a.saas|full.stack|production.ready|build.me|build.a.platform"
    action: "evaluate-activate-implement"
---

# Production Grade

!`git status 2>/dev/null || echo "No git repo detected"`
!`cat CLAUDE.md 2>/dev/null || echo "No CLAUDE.md found"`
!`ls Claude-Production-Grade-Suite/ 2>/dev/null || echo "No existing workspace"`

## Overview

Fully autonomous meta-skill orchestrator. The user gives a high-level vision and this skill runs the entire production pipeline — define, build, harden, ship, sustain — with minimal user intervention. Five consolidated agents invoke 13 individual skills internally. The user approves at 3 strategic gates only.

**All skills are bundled in this plugin. Single install, everything included.**

**Partial execution:** Parse `$ARGUMENTS` to detect subset requests. If `$ARGUMENTS` contains "just build", "just define", "just harden", "just ship", or "just document", execute only that phase group. Use `$0` for the command and `$1` onward for scope qualifiers.

## When to Use

- Starting a new SaaS, platform, or service from scratch
- Building a complete production-ready system end-to-end
- Going from idea to working, tested, secured, deployed code
- Greenfield projects that need the full treatment
- User says "build me a...", "production grade", "production ready"

## Pipeline Kickoff

When triggered, follow this EXACT sequence:

1. **Print kickoff banner** immediately:
```
━━━ Production Grade Pipeline ━━━━━━━━━━━━━━━━━━━━━━
Project: [extracted from user's message]
Session: ${CLAUDE_SESSION_ID}
⧖ Initializing workspace and analyzing request...
```

2. **Create workspace** — `mkdir -p Claude-Production-Grade-Suite/.orchestrator/memory/shared`

3. **Detect existing workspace** — if `.orchestrator/pipeline-state.json` exists, offer to resume or restart.

4. **Research the domain** — use WebSearch before asking the user anything.

5. **Write execution plan** to `.orchestrator/execution-plan.md`

6. **Print plan summary** and immediately start Phase 1. Do NOT ask "should I proceed?"

7. **At each gate**, use the exact AskUserQuestion patterns from the UX Protocol below.

**Key principle:** The user already told you what to build. Research, plan, start building. Only pause at the 3 approval gates.

## User Experience Protocol

**CRITICAL: Follow this section exactly.**

### RULE 1: NEVER Ask Open-Ended Questions

**NEVER output text that expects the user to type a response.** Every interaction MUST use `AskUserQuestion` with predefined options.

**WRONG:**
```
Do you approve the BRD? Please type yes or no.
What do you think about the architecture?
```

**RIGHT:**
```python
AskUserQuestion(questions=[{
  "question": "BRD is complete (12 user stories, 8 acceptance criteria). Ready to proceed?",
  "header": "Gate 1",
  "options": [
    {"label": "Approve — start architecture (Recommended)", "description": "Lock BRD and proceed to Solution Architect phase"},
    {"label": "Show me the BRD details", "description": "Display the full BRD document before deciding"},
    {"label": "I have changes", "description": "Suggest specific modifications to the BRD"},
    {"label": "Chat about this", "description": "Type free-form input about the requirements"}
  ],
  "multiSelect": false
}])
```

### RULE 2: Always End Options with "Chat about this"

Every `AskUserQuestion` call MUST have `"Chat about this"` as the **last option**. This is the user's escape hatch to type free-form input when none of the preset options fit.

### RULE 3: Recommended Option First

The first option should always be the recommended/default action with `(Recommended)` in the label. This is what the user will select 80% of the time — make it easy.

### RULE 4: Continuous Execution Between Gates

- Once the user selects an option, **work continuously until the next gate or task completion**
- Never stop to ask "should I continue?" — just keep going
- Print progress constantly (see Progress Format below)
- If the user presses ESC, pause and accept additional input before resuming

### RULE 5: Real-Time Terminal Updates

**Constantly update the user** on what you're doing. Never go silent.

```
━━━ Phase N: [Phase Name] ━━━━━━━━━━━━━━━━━━━━━━

⧖ Setting up project structure...
✓ Project structure created (12 directories)
⧖ Writing database schema...
✓ Database schema created (9 tables, 13 migrations)
⧖ Implementing API routes...
✓ API routes implemented (17 endpoints)

━━━ Phase N Complete ━━━━━━━━━━━━━━━━━━━━━━━━━━━
Summary: Backend service with 17 endpoints, 9 DB tables
```

### RULE 6: Gate Interactions

**Gate 1 — BRD Approval:**
```python
AskUserQuestion(questions=[{
  "question": "BRD complete: [X] user stories, [Y] acceptance criteria. Approve?",
  "header": "Gate 1: BRD",
  "options": [
    {"label": "Approve — start architecture (Recommended)", "description": "BRD locked, proceed to Solution Architect"},
    {"label": "Show BRD details", "description": "Display the full BRD before deciding"},
    {"label": "I have changes", "description": "Request modifications to requirements"},
    {"label": "Chat about this", "description": "Free-form input about the BRD"}
  ],
  "multiSelect": false
}])
```

**Gate 2 — Architecture Approval:**
```python
AskUserQuestion(questions=[{
  "question": "Architecture complete: [tech stack summary]. Approve to start building?",
  "header": "Gate 2: Arch",
  "options": [
    {"label": "Approve — start building (Recommended)", "description": "Architecture locked, begin autonomous BUILD phase"},
    {"label": "Show architecture details", "description": "Walk through ADRs, diagrams, and API spec"},
    {"label": "I have concerns", "description": "Flag issues with architecture decisions"},
    {"label": "Chat about this", "description": "Free-form input about the architecture"}
  ],
  "multiSelect": false
}])
```

**Gate 3 — Production Readiness:**
```python
AskUserQuestion(questions=[{
  "question": "All phases complete. [summary of what was built]. Ship it?",
  "header": "Gate 3: Ship",
  "options": [
    {"label": "Ship it — production ready (Recommended)", "description": "Finalize assembly and deploy"},
    {"label": "Show full report", "description": "Display complete pipeline summary"},
    {"label": "Fix issues first", "description": "Address remaining findings before shipping"},
    {"label": "Chat about this", "description": "Free-form input about production readiness"}
  ],
  "multiSelect": false
}])
```

### RULE 7: Autonomy Between Gates

1. **Default to sensible choices** — don't ask the user for every minor decision
2. **Only use AskUserQuestion at strategic gates** — the 3 gates above + major blockers
3. **Self-resolve issues** — debug and fix before bothering the user
4. **Report, don't ask** — "I chose PostgreSQL because [reason]" not "Which database?"
5. **Batch questions** — use `multiSelect: true` or multiple questions in one AskUserQuestion call

## Consolidated Agent Architecture

Five agents with sub-modes replace the previous 13-agent design. Each agent invokes individual skills internally but operates with a single context window per agent group, reducing context waste.

| Agent | Modes | Skills Invoked | Gates |
|-------|-------|---------------|-------|
| **DEFINE** | PM, Architect | `product-manager`, `solution-architect` | Gate 1, Gate 2 |
| **BUILD** | Backend, Frontend, QA | `software-engineer`, `frontend-engineer`, `qa-engineer` | None (autonomous) |
| **HARDEN** | Security, Code Review | `security-engineer`, `code-reviewer` | None (autonomous) |
| **SHIP** | DevOps, SRE, Data Science | `devops`, `sre`, `data-scientist` | Gate 3 |
| **SUSTAIN** | Docs, Skills, Learning | `technical-writer`, `skill-maker` | None (autonomous) |

### How to Dispatch Agents

**Option A: Skill Tool (simple, sequential, needs user interaction)**
```python
Skill(skill="product-manager")
```

**Option B: Agent Tool (complex, parallel, background work)**
```python
Agent(
  prompt="You are the BUILD Agent in Backend mode. Read architecture at Claude-Production-Grade-Suite/solution-architect/ and implement the full backend...",
  subagent_type="general-purpose",
  mode="bypassPermissions",
  run_in_background=True
)
```

Use forked context (`context: fork`) for heavy subagent work. Subagents return condensed 1-2k token summaries. Main context stays clean.

### Parallelization

- **BUILD:** Backend + Frontend modes run in parallel (both as background Agents)
- **HARDEN:** Security + Code Review modes run in parallel (both as background Agents)
- **All other transitions:** Sequential (each depends on previous output)

## Workspace Architecture

```
Claude-Production-Grade-Suite/
├── .orchestrator/                # Pipeline state and memory
│   ├── pipeline-state.json       # Current phase, branch, timestamps
│   ├── execution-plan.md         # Pipeline configuration
│   ├── decisions-log.md          # All decisions with git commit refs
│   ├── learnings.md              # Compound learning accumulation
│   ├── agent-activity.log        # Cross-agent activity feed
│   └── memory/                   # Branch-aware persistent memory
│       ├── {branch-name}/        # Per-branch state
│       └── shared/               # Cross-branch knowledge
├── product-manager/              # DEFINE: Requirements
├── solution-architect/           # DEFINE: Architecture
├── software-engineer/            # BUILD: Backend
├── frontend-engineer/            # BUILD: Frontend (if applicable)
├── qa-engineer/                  # BUILD: Testing
├── security-engineer/            # HARDEN: Security audit
├── code-reviewer/                # HARDEN: Quality gate
├── devops/                       # SHIP: Infrastructure
├── sre/                          # SHIP: Production readiness
├── data-scientist/               # SHIP: AI/ML optimization (conditional)
├── technical-writer/             # SUSTAIN: Documentation
└── skill-maker/                  # SUSTAIN: Custom skills
```

## Execution Protocol

### Phase 0: Initialize

Create workspace. Initialize `pipeline-state.json` with current git branch. Write execution plan. Start immediately.

```python
# Detect branch for memory isolation
branch = !`git branch --show-current 2>/dev/null || echo "main"`
# Initialize state
pipeline-state.json = {"phase": 0, "branch": branch, "status": "initializing"}
```

### DEFINE Agent (Phases 1-2)

See `phases/define.md` for detailed PM and Architect mode instructions.

**Phase 1: Product Manager mode.** Invoke `Skill: product-manager`. Research domain, write BRD, present Gate 1.

**Phase 2: Solution Architect mode.** Invoke `Skill: solution-architect`. Read BRD, design architecture, present Gate 2.

### BUILD Agent (Phases 3-4)

See `phases/build.md` for detailed Backend, Frontend, and QA mode instructions.

**Phase 3: Backend + Frontend modes (parallel).** Invoke `Skill: software-engineer` and optionally `Skill: frontend-engineer` as background Agents. TDD enforced: write test, watch fail, implement, watch pass, refactor.

**Validation loop runs per service:**
```
while not valid: build() -> if fail: fix(errors) -> rebuild()
while not valid: test()  -> if fail: fix(errors) -> retest()
```

**Phase 4: QA mode.** Invoke `Skill: qa-engineer`. Integration, e2e, and performance tests. Distinguishes test bugs from implementation bugs.

### HARDEN Agent (Phases 5a-5b)

See `phases/harden.md` for detailed Security and Code Review mode instructions.

**Phases 5a + 5b run in parallel.** Invoke `Skill: security-engineer` and `Skill: code-reviewer` as background Agents.

**Multi-model review:** If external model APIs are available, dispatch reviews to OpenAI Codex and Google Gemini in parallel with Claude as primary + fallback. Aggregate findings, deduplicate, rank by severity. 3x review coverage when available.

**Auto-remediation:** Critical/High issues fixed immediately with regression tests. Medium/Low documented but do not block pipeline.

**Validation loop:**
```
while critical issues remain: fix() -> rescan() -> retest()
```

### SHIP Agent (Phases 6-7)

See `phases/ship.md` for detailed DevOps, SRE, and Data Scientist mode instructions.

**Phase 6: DevOps mode.** Invoke `Skill: devops`. Containerize, CI/CD, IaC, monitoring. Validate everything:
```
docker build -> docker-compose up -> terraform validate -> pipeline lint
```

**Phase 7: SRE mode.** Invoke `Skill: sre`. Production readiness review, SLOs, chaos scenarios, runbooks. Present Gate 3.

**Phase 7b: Data Scientist mode (conditional).** Auto-triggers if code imports LLM/ML libraries. Invoke `Skill: data-scientist`.

### SUSTAIN Agent (Phases 8-9-10)

See `phases/sustain.md` for detailed Docs, Skills, and Compound Learning instructions.

**Phase 8: Technical Writer mode.** Invoke `Skill: technical-writer`. API reference, dev guides, Docusaurus scaffold.

**Phase 9: Skill Maker mode.** Invoke `Skill: skill-maker`. 3-5 project-specific skills.

**Phase 10: Compound Learning.** Capture what went wrong, patterns discovered, decisions made. Write to `.orchestrator/learnings.md`. Optionally append key patterns to project `CLAUDE.md`.

### Final Assembly

1. **Integrate code** from workspace to project root (ask user with AskUserQuestion).
2. **Run final validation:** `docker-compose up`, `make test`, `terraform validate`, health checks.
3. **Present final summary** (see template below).

## Orchestrator Intelligence

The orchestrator is NOT a dumb sequential runner. It observes, adapts, and coordinates.

### Adaptive Rules

| Situation | Action |
|-----------|--------|
| "Simple API, no frontend" | Skip Frontend mode, simplify DevOps |
| Architect picks monolith | Single Dockerfile, skip K8s/service mesh |
| Implementation uses LLM APIs | Auto-enable Data Scientist mode |
| Security finds critical vuln | Re-invoke Backend mode to fix before SHIP |
| QA failures > 20% | Flag to user: implementation may need review |
| Code reviewer finds arch drift | Warn user (arch decisions are user-approved) |
| User says "skip testing" | Warn against it, proceed if insisted, log in decisions |

### Context Bridging

Each agent reads from previous agents' workspace folders. No redundant interviews.

| Agent | Reads From | Writes To |
|-------|-----------|-----------|
| DEFINE (PM) | User interview, web research | `product-manager/` |
| DEFINE (Arch) | `product-manager/BRD/` | `solution-architect/` |
| BUILD (Backend) | `solution-architect/api/`, `schemas/`, `scaffold/` | `software-engineer/` |
| BUILD (Frontend) | `solution-architect/api/`, `product-manager/BRD/` | `frontend-engineer/` |
| BUILD (QA) | `software-engineer/`, `frontend-engineer/`, `solution-architect/` | `qa-engineer/` |
| HARDEN | All implementation + architecture folders | `security-engineer/`, `code-reviewer/` |
| SHIP | `solution-architect/`, `software-engineer/`, HARDEN output | `devops/`, `sre/`, `data-scientist/` |
| SUSTAIN | ALL workspace folders | `technical-writer/`, `skill-maker/`, `.orchestrator/` |

## Security Hooks (Continuous)

Security is a continuous concern, not just a HARDEN phase activity. These hooks run during ALL phases:

**PreToolUse — Block dangerous commands:**
- Block `rm -rf /`, `chmod 777`, and destructive operations
- Block execution of downloaded scripts without review

**Pre-commit — Credential scanning:**
- Block `.env`, `.key`, `.pem`, `credentials.json` from git
- Scan staged files for API keys, tokens, passwords, hardcoded secrets

**During implementation — Continuous scanning:**
- Software Engineer mode scans for hardcoded secrets as it writes code
- Any credential found is immediately replaced with env var reference

## Autonomous Agent Behavior Protocol

Every agent in this pipeline follows these rules:

### Build and Verify
1. After writing code, **run it**. Compile, build, start services.
2. After writing tests, **execute them**. Do not assume they pass.
3. After writing infrastructure, **validate it** (`terraform validate`, `docker build`).

### Validation Loop Pattern
Every critical operation uses: `while not valid: fix(errors); validate()`
- Code: compile -> fix -> rebuild
- Tests: run -> fix -> rerun
- IaC: validate -> fix -> revalidate
- Security: verify fixes -> re-audit

### Self-Debug
1. Read the error output. Identify root cause — do not guess.
2. Fix and retry. After 3 failed attempts, stop and report to user with: error message, what you tried, what you think is wrong.

### Quality Bar
1. No TODOs in production code. No placeholder/stub implementations.
2. All code must compile/build without errors.
3. All tests must pass (or failures are documented with reasons).
4. Security issues above Medium must be fixed, not just documented.

## Partial Execution

Users can run subsets via `$ARGUMENTS`:

| Command | Phases Run |
|---------|-----------|
| `/production-grade just define` | PM + Architect only |
| `/production-grade just build` | Engineer + QA (requires architecture) |
| `/production-grade just harden` | Security + Review (requires implementation) |
| `/production-grade just ship` | DevOps + SRE (requires implementation) |
| `/production-grade just document` | Technical Writer (requires any prior phase) |
| `/production-grade skip frontend` | Omit Frontend mode |
| `/production-grade start from architecture` | Skip PM, start at Architect |

## Final Summary Template

```
╔══════════════════════════════════════════════════════════════╗
║                 PRODUCTION GRADE — COMPLETE                  ║
╠══════════════════════════════════════════════════════════════╣
║  Project: <name>                                             ║
║  Session: ${CLAUDE_SESSION_ID}                               ║
║  Duration: <time>                                            ║
║                                                              ║
║  DEFINE:  ✓ BRD (<X> stories) ✓ Architecture (<pattern>)     ║
║  BUILD:   ✓ Backend (<N> services) ✓ Tests (<N> passing)     ║
║  HARDEN:  ✓ Security (<N> fixed) ✓ Code Review (<N> fixed)   ║
║  SHIP:    ✓ Docker ✓ CI/CD ✓ Terraform ✓ SRE approved       ║
║  SUSTAIN: ✓ Docs ✓ Skills (<N> created) ✓ Learnings captured ║
║                                                              ║
║  Workspace: Claude-Production-Grade-Suite/                   ║
╚══════════════════════════════════════════════════════════════╝
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Running BUILD without DEFINE | Architecture decisions must exist first |
| Skipping tests | Production grade means tested. Always run QA |
| Not running code after writing it | Every agent verifies their output compiles and runs |
| Leaving broken code | Self-debug: fix or clearly report why it cannot be fixed |
| Agents working in isolation | Cross-reference workspace folders |
| Over-asking the user | Batch questions, sensible defaults, 3 gates only |
| Writing stubs instead of real code | No `// TODO: implement` in production code |
| Writing tests after all code is done | TDD: write test first, then implement |
| Security only in HARDEN phase | Credential scanning runs continuously |
| Not capturing learnings | Always run Compound Learning at pipeline end |
