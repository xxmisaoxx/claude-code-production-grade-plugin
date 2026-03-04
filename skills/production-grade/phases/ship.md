# SHIP Phase — Detailed Instructions

This file contains detailed instructions for the **SHIP Agent** (DevOps + SRE + Data Scientist modes).

---

## DevOps Mode

### Objective

Generate production infrastructure: containerization, CI/CD pipelines, infrastructure-as-code, and monitoring. All configurations must be validated before proceeding.

### Workflow

1. **Read architecture and implementation:**
   - Architecture ADRs for deployment topology decisions
   - Service structure for containerization
   - Orchestrator decisions-log for any infrastructure preferences

2. **Containerization:**
   ```
   for each service:
     write Dockerfile (multi-stage, minimal base image)
     build image locally: docker build -t service-name .
     if build fails: fix Dockerfile, rebuild
     verify image starts: docker run service-name

   write docker-compose.yml for full stack
   run: docker-compose up
   verify all services start and communicate
   ```

3. **CI/CD Pipelines:**
   ```
   generate pipelines for:
     build (compile, lint, test)
     test (integration, e2e in CI environment)
     security (dependency scan, SAST)
     deploy (staging, production with approval gates)

   validate pipeline syntax:
     GitHub Actions: actionlint or schema validation
     GitLab CI: gitlab-ci-lint equivalent
   ```

4. **Infrastructure-as-Code:**
   ```
   generate Terraform/Pulumi/CDK for:
     compute (ECS, K8s, Lambda — based on architecture)
     database (RDS, DynamoDB, Cloud SQL — based on data model)
     networking (VPC, subnets, security groups, load balancer)
     storage (S3, GCS — based on requirements)
     secrets (AWS Secrets Manager, Vault)

   validation loop:
     terraform init
     terraform validate
     if invalid: fix, re-validate
     terraform plan (dry run)
   ```

5. **Monitoring and Observability:**
   ```
   generate:
     Prometheus/Grafana dashboards (or cloud-native equivalent)
     Alert rules for SLO violations
     Structured logging configuration
     Distributed tracing setup (if microservices)
   ```

6. **Validation Loop:**
   ```
   while not valid:
     docker-compose up — full stack starts
     all health checks pass
     terraform validate — IaC is valid
     CI pipeline syntax is valid
     if any fail: fix, re-validate
   ```

### Adaptive Rules

| Architecture Decision | DevOps Adjustment |
|----------------------|-------------------|
| Monolith | Single Dockerfile, simpler CI, no service mesh |
| Microservices | Per-service Dockerfiles, K8s manifests, service mesh |
| Serverless | SAM/CDK templates, no containers |
| No frontend | Skip CDN, frontend CI pipeline |
| Simple API | Skip K8s, use docker-compose for production |

### Output Contract

| File | Purpose |
|------|---------|
| `devops/containers/` | Dockerfiles, docker-compose.yml |
| `devops/ci-cd/` | CI/CD pipeline configurations |
| `devops/terraform/` | Infrastructure-as-code |
| `devops/monitoring/` | Dashboards, alerts, logging config |

---

## SRE Mode

### Objective

Validate production readiness and establish operational excellence: SLOs, incident response, chaos engineering, runbooks.

### Workflow

1. **Production Readiness Review:**
   ```
   checklist:
     [ ] All services have health check endpoints
     [ ] Graceful shutdown handling
     [ ] Configuration via environment variables (12-factor)
     [ ] Secrets not hardcoded
     [ ] Database migrations are reversible
     [ ] Rate limiting on public endpoints
     [ ] Circuit breakers for external dependencies
     [ ] Structured logging with correlation IDs
     [ ] Metrics exposed (latency, throughput, error rate)
     [ ] Alerting configured for SLO violations
     [ ] Backup and restore procedures documented
     [ ] Horizontal scaling tested
   ```

2. **SLO Definition:**
   ```
   for each service:
     define availability target (e.g., 99.9%)
     define latency targets (p50, p95, p99)
     define error rate budget
     configure alerts when error budget is burning too fast
   ```

3. **Chaos Engineering:**
   ```
   design scenarios:
     service failure (kill random service, verify recovery)
     database failure (verify failover)
     network partition (verify timeout handling)
     resource exhaustion (CPU, memory pressure)
     dependency failure (external API down)
   ```

4. **Incident Management:**
   ```
   generate:
     incident response playbook
     escalation paths
     communication templates
     post-mortem template
   ```

5. **Runbooks:**
   ```
   for each operational procedure:
     deployment steps
     rollback procedure
     scaling procedure
     database maintenance
     log investigation guide
   ```

6. **Gate 3** — Production Readiness Approval (use exact UX Protocol pattern from SKILL.md)

### Output Contract

| File | Purpose |
|------|---------|
| `sre/production-readiness/` | Checklist results, gap analysis |
| `sre/chaos/` | Chaos engineering scenarios |
| `sre/incidents/` | Incident management setup |
| `sre/runbooks/` | Operational runbooks |

---

## Data Scientist Mode (Conditional)

### Trigger

Runs automatically if implementation code imports `openai`, `anthropic`, `langchain`, `transformers`, `torch`, `tensorflow`, or any LLM/ML API.

### Objective

Optimize AI/ML/LLM systems for cost, quality, and performance. Design experiment frameworks and data pipelines.

### Workflow

1. **LLM Usage Audit:**
   ```
   scan codebase for:
     prompt patterns and token usage
     model selection (could cheaper model work?)
     caching opportunities (repeated identical prompts)
     streaming vs batch patterns
     error handling for API failures
   ```

2. **Optimization:**
   ```
   generate:
     prompt library with optimized versions
     token cost analysis and reduction plan
     semantic caching layer design
     fallback chain (primary model -> cheaper model -> cached response)
   ```

3. **Experiment Framework:**
   ```
   design:
     A/B testing infrastructure for prompt variants
     Evaluation metrics (quality, cost, latency)
     Statistical significance thresholds
   ```

4. **Data Pipeline (if applicable):**
   ```
   design:
     Analytics event schema
     ETL pipeline architecture
     Feature store for ML models
     Model serving infrastructure
   ```

### Output Contract

| File | Purpose |
|------|---------|
| `data-scientist/analysis/` | System audit, cost models |
| `data-scientist/llm-optimization/` | Prompt library, token analysis, caching |
| `data-scientist/experiments/` | A/B testing framework |
| `data-scientist/data-pipeline/` | Analytics architecture, ETL |
| `data-scientist/ml-infrastructure/` | Feature store, model serving |

### Context Bridging

| Reads From | Key Information |
|-----------|-----------------|
| `solution-architect/docs/` | Architecture decisions, tech stack |
| `solution-architect/api/` | API contracts for infrastructure design |
| `software-engineer/services/` | Implementation for containerization and LLM audit |
| `frontend-engineer/app/` | Frontend for CDN and static hosting decisions |
| `security-engineer/remediation/` | Security fixes affecting infrastructure |
| `code-reviewer/findings/` | Performance findings affecting infrastructure |
| `product-manager/BRD/` | Scale requirements, SLO expectations |
| `.orchestrator/decisions-log.md` | Infrastructure preferences |
