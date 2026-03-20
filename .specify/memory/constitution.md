<!--
Sync Impact Report (2026-03-20):
- Version change: initial → 1.0.0
- List of modified principles: none (initial creation)
- Added sections: Core Principles (5), Technology Standards, Development Workflow, Governance
- Removed sections: none
- Templates requiring updates: none
- Follow-up TODOs: none
-->
# ContosoDashboard Constitution

## Core Principles

### I. Spec-Driven Development
Every feature must begin with a detailed specification document that clearly defines requirements, user stories, acceptance criteria, and success metrics. Specifications serve as the single source of truth for development.

### II. Plan-First Implementation
Implementation must follow a comprehensive plan that outlines technical architecture, component design, data models, and integration points. Plans ensure systematic and traceable development.

### III. Task-Based Execution
Features are decomposed into small, actionable tasks with clear dependencies, deliverables, and ownership. Tasks enable parallel development and incremental progress tracking.

### IV. Test-First Development
Automated tests are written before code implementation. Unit tests, integration tests, and acceptance tests ensure quality, prevent regressions, and validate functionality.

### V. Iterative Refinement
Development proceeds in iterations with regular reviews, feedback incorporation, and adjustments. Each iteration produces a potentially shippable increment.

## Technology Standards

The application uses .NET 8.0 or later, Blazor Server for UI, Entity Framework Core for data access, and follows Microsoft coding guidelines and security best practices. All code must be compatible with .NET 8+ and use modern C# features.

## Development Workflow

Use GitHub Spec Kit agents (/speckit.specify, /speckit.plan, /speckit.tasks, /speckit.implement) for structured development. All changes require code reviews, automated testing, and compliance with this constitution. Branches follow the pattern [###-feature-name].

## Governance

This constitution supersedes all other practices and guides all project decisions. Amendments require documentation, team consensus, and a migration plan for existing code. All pull requests must verify compliance with these principles.

**Version**: 1.0.0 | **Ratified**: 2026-03-20 | **Last Amended**: 2026-03-20
