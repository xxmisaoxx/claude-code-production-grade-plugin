# HARDEN Phase — Detailed Instructions

This file contains detailed instructions for the **HARDEN Agent** (Security Engineer + Code Reviewer modes).

## Continuous Security Principle

Security is NOT just a post-build phase. The HARDEN Agent enforces security throughout the pipeline:
- PreToolUse hooks block dangerous commands during ALL phases
- Pre-commit hooks scan for credentials before any git operation
- Credential scanning runs DURING implementation, not just during audit
- HARDEN phase performs the deep audit; security hooks provide continuous coverage

---

## Security Engineer Mode

### Objective

Perform a comprehensive security audit of all implementation code, infrastructure, and dependencies. Auto-fix critical and high severity issues.

### Workflow

1. **Threat Modeling (STRIDE):**
   ```
   for each service boundary:
     analyze Spoofing threats
     analyze Tampering threats
     analyze Repudiation threats
     analyze Information Disclosure threats
     analyze Denial of Service threats
     analyze Elevation of Privilege threats
     document findings in threat-model/
   ```

2. **OWASP Audit:**
   ```
   for each API endpoint:
     check Injection (SQL, NoSQL, OS, LDAP)
     check Broken Authentication
     check Sensitive Data Exposure
     check XML External Entities
     check Broken Access Control
     check Security Misconfiguration
     check Cross-Site Scripting
     check Insecure Deserialization
     check Using Components with Known Vulnerabilities
     check Insufficient Logging & Monitoring
   ```

3. **Dependency Scan:**
   ```bash
   # Scan for known vulnerabilities
   npm audit / pip-audit / go mod verify / equivalent
   # Check for outdated dependencies
   # Check for license compliance
   ```

4. **Credential Scan:**
   ```
   scan all files for:
     API keys, tokens, passwords
     .env files with real values
     hardcoded secrets in source code
     private keys, certificates
   ```

5. **Multi-Model Review (if available):**
   ```
   dispatch security review to external models in parallel:
     - OpenAI Codex: focus on injection and auth patterns
     - Google Gemini: focus on cryptographic and data exposure patterns
     - Claude (primary): comprehensive STRIDE + OWASP review
   aggregate all findings
   deduplicate and rank by severity
   ```
   If external models are unavailable, Claude performs all reviews as primary. External models enhance coverage but are not required.

6. **Auto-Remediation:**
   ```
   for each finding:
     if Critical or High:
       fix immediately in source code
       add regression test
       verify fix does not break existing tests
     if Medium or Low:
       document in remediation plan
       do not block pipeline
   ```

7. **Validation Loop:**
   ```
   while critical/high findings remain unfixed:
     apply fix
     run security scan on fixed code
     run full test suite to verify no regressions
     if new issues introduced: fix those too
   ```

### Output Contract

| File | Purpose |
|------|---------|
| `security-engineer/threat-model/` | STRIDE analysis per service |
| `security-engineer/code-audit/` | OWASP findings with severity |
| `security-engineer/pen-test/` | Penetration test plans |
| `security-engineer/remediation/` | Fix plans for medium/low issues |
| `security-engineer/dependency-scan.md` | Dependency vulnerability report |

---

## Code Reviewer Mode

### Objective

Review all implementation code for quality, architecture conformance, performance, and maintainability. Auto-fix high-severity issues.

### Workflow

1. **Architecture Conformance:**
   ```
   for each service:
     verify matches architecture ADRs
     verify follows agreed patterns (error handling, logging, auth)
     verify API implementation matches OpenAPI contract
     verify data access follows defined patterns (repository, ORM usage)
   flag any architecture drift — warn user, do not auto-fix
   ```

2. **Code Quality:**
   ```
   check for:
     DRY violations (duplicated logic across services)
     KISS violations (unnecessarily complex patterns)
     YAGNI violations (unused code, over-engineered abstractions)
     Missing error handling (unhandled promises, uncaught exceptions)
     N+1 query patterns
     Resource leaks (unclosed connections, streams)
     Race conditions in concurrent code
   ```

3. **Performance Review:**
   ```
   check for:
     Missing database indexes for common queries
     Unbounded queries (no pagination, no limits)
     Missing caching for expensive operations
     Synchronous operations that should be async
     Memory-intensive patterns (loading full datasets)
   ```

4. **Multi-Model Review (if available):**
   ```
   dispatch code review to external models in parallel:
     - Focus areas distributed across models
     - Claude as primary reviewer
     - Aggregate findings, deduplicate
   ```

5. **Auto-Fix Protocol:**
   ```
   for each finding:
     if auto-fixable (missing error handling, N+1, resource leaks):
       apply fix
       run tests to verify no regressions
     if architecture drift:
       document and warn user (architecture decisions are user-approved)
     if subjective (naming, style):
       document as suggestion, do not fix
   ```

6. **Validation Loop:**
   ```
   while critical quality issues remain:
     apply fixes
     run full test suite
     verify code still builds
     if new issues introduced: fix those too
   ```

### Output Contract

| File | Purpose |
|------|---------|
| `code-reviewer/findings/` | Review findings by severity |
| `code-reviewer/metrics/` | Code quality metrics (complexity, coverage, duplication) |
| `code-reviewer/auto-fixes/` | Applied fixes with before/after diffs |

### Context Bridging

| Reads From | Key Information |
|-----------|-----------------|
| `solution-architect/docs/adrs/` | Architecture decisions for conformance check |
| `solution-architect/api/` | API contracts for implementation validation |
| `software-engineer/services/` | Backend implementation code |
| `frontend-engineer/app/` | Frontend implementation code |
| `qa-engineer/` | Test suites and coverage reports |
| `qa-engineer/findings.md` | Implementation bugs found by QA |
| `.orchestrator/decisions-log.md` | User-approved decisions (do not override) |
