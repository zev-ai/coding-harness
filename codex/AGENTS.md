# AGENTS.md

This file is the project-level operating guide for Codex agents working in this
repository.

## Project Mission

YOYO Dataset Viewer is a local dataset review workstation. Product behavior,
data contracts, and review workflows are more important than technical
rewrites. Refactors must preserve the V1 user workflows unless the docs are
updated first and the change is explicitly approved.

## Source Of Truth

Read the relevant docs before changing behavior, API contracts, data shape,
tests, or UI workflows:

- `docs/product-v1.md` for product requirements and user workflows.
- `docs/data-contract.md` for data sources, field meanings, and metrics.
- `docs/conceptual-schema.md` for conceptual entities and schema boundaries.
- `docs/api-contract.md` for frontend/backend API routes and response shapes.

When code and docs conflict, stop and resolve the mismatch before
implementation. Do not silently change behavior, schema, API shape, or UI
workflow.

## Persistent Subagents

Project-scoped custom agents live in `.codex/agents/`:

- `architect`: owns requirements, business flow, data model, API contract,
  high-level architecture, and acceptance criteria.
- `ui-designer`: turns architect output into UI and interaction design,
  including HTML prototypes and Figma-ready specifications.
- `frontend-engineer`: implements frontend behavior from UI design and API
  contracts, then validates with Playwright.
- `backend-engineer`: implements backend APIs, persistence, migrations, and
  backend tests from the data model and API contract.
- `test-engineer`: writes test plans early and performs full-chain regression
  after implementation.

The main agent acts as integrator: it coordinates subagents, checks handoffs,
guards scope, and performs final review.

## Workflow

For non-trivial work:

1. If requirements, business process, data model, API, or technical direction
   are unclear, spawn `architect` first.
2. For UI or interaction changes, spawn `ui-designer` after architect produces
   the product and interaction requirements.
3. For frontend implementation, spawn `frontend-engineer` with the UI design
   and API contract as inputs.
4. For backend implementation, spawn `backend-engineer` with the data model,
   API contract, and business rules as inputs.
5. For non-small changes, spawn `test-engineer` before implementation to draft
   a test plan, and again after implementation for regression.
6. The main agent integrates results and verifies docs, tests, and code agree.

If requirements change during implementation, return to `architect` and update
the relevant docs before continuing.

## Blocking Rules

- Do not mix runtime separation, architecture refactors, and product UI rewrites
  in one change.
- Do not change requirements, API shape, schema, data semantics, or UI
  workflows without updating the source-of-truth docs first.
- Do not let frontend work delete, weaken, or replace V1 workflows unless the
  product docs and acceptance criteria explicitly say so.
- Do not let backend work silently alter API response shapes, error JSON,
  snapshot isolation, metrics, asset JSON, or persistence compatibility.
- Do not accept shallow smoke tests as sufficient for UI or workflow changes.
- Do not revert user changes or unrelated dirty worktree changes unless the
  user explicitly requests it.

## Required Verification

Use the existing Python environment:

```bash
/home/rs1/wanger/miniconda3/envs/py310torch251/bin/python -m pytest
```

Frontend checks:

```bash
cd frontend && npm run typecheck
cd frontend && npm run build
cd frontend && npm run test:e2e
```

For service-management or release-entry changes, additionally validate the
admin script manually:

```bash
./admin_py310torch251_frontend_6680_backend_6681.sh --status
```

Report any verification that could not be run and why.
