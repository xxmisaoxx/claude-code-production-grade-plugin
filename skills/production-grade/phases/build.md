# BUILD Phase — Detailed Instructions

This file contains detailed instructions for the **BUILD Agent** (Software Engineer + Frontend Engineer + QA Engineer modes).

## TDD Enforcement

The BUILD phase enforces Test-Driven Development. Every service follows RED-GREEN-REFACTOR:

```
for each service/module:
  1. RED    — Write failing test based on API contract/acceptance criteria
  2. GREEN  — Write minimal code to make the test pass
  3. REFACTOR — Improve code quality while keeping tests green
  4. VALIDATE — Run full test suite, fix any regressions
```

Tests are written ALONGSIDE implementation, not after. A service is not "done" until its unit tests pass.

---

## Software Engineer Mode

### Objective

Implement the complete backend based on architecture contracts. Every service must compile, run, and pass its unit tests before moving to the next.

### Workflow

1. **Read architecture contracts** from `solution-architect/`:
   - API contracts define endpoints, request/response schemas
   - Data model defines entities and relationships
   - Scaffold defines project structure

2. **Set up local dev environment:**
   ```bash
   # Initialize from scaffold
   cp -r Claude-Production-Grade-Suite/solution-architect/scaffold/* Claude-Production-Grade-Suite/software-engineer/services/
   # Install dependencies
   cd Claude-Production-Grade-Suite/software-engineer/services/ && [package-manager] install
   # Start infrastructure (DB, cache, queues)
   docker-compose up -d
   ```

3. **Implement each service with TDD:**
   ```
   for each API endpoint / service module:
     write unit test covering happy path + error cases
     run test — confirm it FAILS (RED)
     write implementation code
     run test — confirm it PASSES (GREEN)
     refactor if needed — confirm tests still pass
   ```

4. **Validation Loop (per service):**
   ```
   while not valid:
     compile/build the service
     if build fails: read error, fix, rebuild
     run unit tests
     if tests fail: read output, fix code or test, rerun
     start the service
     if startup fails: read logs, fix, restart
   ```

5. **Validation Loop (full stack):**
   ```
   docker-compose up
   verify all services start
   verify health check endpoints respond
   verify inter-service communication works
   ```

### Autonomous Debugging Protocol

1. Run build/compile
2. If error: analyze error message, identify root cause, fix, retry
3. If still failing after 3 attempts: log the issue with full error trace and notify user
4. Never leave broken code — either fix it or document exactly what is wrong and why

### Output Contract

| File | Purpose |
|------|---------|
| `software-engineer/services/` | Implemented backend services |
| `software-engineer/services/**/tests/` | Unit tests co-located with services |
| `software-engineer/libs/` | Shared libraries |
| `software-engineer/scripts/` | Dev scripts, seed data, migrations runner |
| `software-engineer/logs/` | Build logs, debug traces |

---

## Frontend Engineer Mode

### Objective

Implement the frontend application based on architecture contracts and BRD user stories. Runs in parallel with Software Engineer when applicable.

### Workflow

1. **Read inputs:**
   - API contracts from `solution-architect/api/` for backend integration
   - BRD user stories from `product-manager/BRD/` for UX flows
   - Design system decisions from architecture ADRs

2. **Set up frontend project:**
   ```bash
   cd Claude-Production-Grade-Suite/frontend-engineer/app/
   [framework-cli] create . # (next, vite, create-react-app, etc.)
   [package-manager] install
   ```

3. **Implement with component-level tests:**
   ```
   for each page/feature:
     write component test (render, user interaction, API mock)
     run test — confirm FAILS
     implement component
     run test — confirm PASSES
     refactor for accessibility and performance
   ```

4. **Validation Loop:**
   ```
   while not valid:
     build the frontend (npm run build / equivalent)
     if build fails: fix, rebuild
     run component tests
     if tests fail: fix, rerun
     start dev server
     verify pages render without console errors
   ```

### Output Contract

| File | Purpose |
|------|---------|
| `frontend-engineer/app/` | Frontend application |
| `frontend-engineer/app/**/tests/` | Component tests |
| `frontend-engineer/storybook/` | Component documentation (if applicable) |
| `frontend-engineer/logs/` | Build logs |

---

## QA Engineer Mode

### Objective

Write and execute higher-level tests (integration, end-to-end, performance) that validate the system as a whole. Unit tests are already written by the engineers — QA adds the next layers.

### Workflow

1. **Read inputs:**
   - API contracts for integration test design
   - BRD acceptance criteria for e2e test scenarios
   - Implementation code for understanding actual behavior
   - Unit test results for coverage gap analysis

2. **Integration Tests:**
   ```
   for each API endpoint:
     write integration test (real DB, real service, mocked externals)
     run test — verify behavior matches API contract
   ```

3. **End-to-End Tests:**
   ```
   for each user story:
     write e2e test covering the full user flow
     run test against docker-compose environment
   ```

4. **Performance Tests:**
   ```
   for critical endpoints:
     write load test (target: response time, throughput, error rate)
     run baseline benchmark
     document results
   ```

5. **Validation Loop:**
   ```
   while test suite not stable:
     run full test suite
     distinguish test bugs from implementation bugs
     fix test bugs immediately
     log implementation bugs as findings for code-reviewer
     rerun
   ```

### Self-Healing Test Protocol

1. Write tests based on API contracts and acceptance criteria
2. Run tests via `make test` or equivalent
3. Distinguish between test bugs and implementation bugs:
   - Test bug: test expectation is wrong, fixture is stale, timing issue
   - Implementation bug: code does not match contract
4. Fix test bugs immediately
5. Implementation bugs: log as findings in `qa-engineer/findings.md`

### Output Contract

| File | Purpose |
|------|---------|
| `qa-engineer/integration/` | Integration test suites |
| `qa-engineer/e2e/` | End-to-end test suites |
| `qa-engineer/performance/` | Load tests and benchmarks |
| `qa-engineer/coverage/` | Coverage reports |
| `qa-engineer/findings.md` | Implementation bugs found during testing |

### Context Bridging

| Reads From | Key Information |
|-----------|-----------------|
| `solution-architect/api/` | API contracts for test design |
| `solution-architect/schemas/` | Data model for fixture generation |
| `product-manager/BRD/brd.md` | Acceptance criteria for e2e scenarios |
| `software-engineer/services/` | Implementation code and unit tests |
| `frontend-engineer/app/` | Frontend code (if applicable) |
| `.orchestrator/decisions-log.md` | Architecture decisions affecting test strategy |
