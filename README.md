# Production Grade Plugin for Claude Code

Meta-skill orchestrator that runs the full production pipeline: **Product Manager → Solution Architect → DevOps**. Takes a project from idea to deployment-ready codebase.

## Prerequisites

This orchestrator invokes three sub-skills that must be installed:

| Skill | Install Command |
|-------|----------------|
| product-manager | `/plugin install product-manager@nagisanzenin` |
| solution-architect | `/plugin install solution-architect@nagisanzenin` |
| devops | `/plugin install devops@nagisanzenin` |

## Installation

### Via Marketplace
```
/plugin marketplace add nagisanzenin/nagisanzenin-plugins
/plugin install production-grade@nagisanzenin
```

### Load Directly
```
claude --plugin-dir /path/to/production-grade-plugin
```

## Usage

Trigger with phrases like:
- "Build a production-grade SaaS"
- "Full production pipeline for this project"
- "Production ready setup"

### Pipeline Output

```
Project Root/
├── BRD/PRD documents
├── SolutionArchitect-Suite/
│   ├── docs/ (ADRs, diagrams, tech stack)
│   ├── api/ (OpenAPI, gRPC, AsyncAPI)
│   ├── schemas/ (ERD, migrations)
│   └── scaffold/ (project structure)
└── DevOps-Suite/
    ├── terraform/ (multi-cloud IaC)
    ├── ci-cd/ (pipelines)
    ├── containers/ (Docker, K8s)
    ├── monitoring/ (Prometheus, Grafana)
    └── security/ (scanning, IAM)
```

## License

MIT
