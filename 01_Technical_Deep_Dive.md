# Technical Deep Dive

**Scaling cMMM Through Agentic AI and Platform Engineering**

Satadru Biswas — Staff Software Engineer, CMMM Platform
September 2025 — May 2026

---

## Overview

Over nine months (Sep 2025 – May 2026), I shipped 126 pull requests across 14 repositories, contributing +85,000 / -5,700 lines of code and authoring 24 Notion design documents. This body of work represents a deliberate strategy: replace manual, human-in-the-loop processes with autonomous, AI-driven systems at every stage of the cMMM model lifecycle — from validation to deployment to incident response.

The unifying thesis is simple: every hour a data scientist, MSP, or engineer spends on a task an agent can do is an hour not spent on the work only humans can do. The projects below operationalize that thesis.

| Metric | Sep 2025 – Mar 2026 | Mar – May 2026 | Total |
|--------|---------------------|----------------|-------|
| PRs Merged | 83 | 43 | 126 |
| Lines Added | +64,285 | +20,862 | +85,147 |
| Repositories | 10 | 12 | 14 |
| Notion Documents | 17 | 7 | 24 |

---

## Agentic AI Systems

The most impactful thread of my work has been building autonomous AI agents that replace manual operational workflows. Each system listed below was designed from scratch, operates with minimal human intervention, and includes built-in safety mechanisms (adversarial review, deterministic cross-checks, human-in-the-loop gates for write operations).

### Chase: Agentic E2E Validation Triage

> **Impact:** Targets 58% of models that fail E2E tests and currently require 24-hour mean-time-to-review by MSP/MSM teams. Uses a traffic-light system (Green/Yellow/Red) to compress reviewer effort to minutes.

Chase is an agentic triage system for cMMM E2E validation failures. When a model fails its automated E2E tests, Chase runs 8 parallel domain-specific analysis tests with weighted scoring to determine whether the failure is explainable (a prior update, a new experiment, a spend baseline shift) or indicative of a fundamentally broken model.

The critical engineering innovation is a **deterministic CSV diff pipeline** that eliminates LLM numerical hallucinations entirely — all numerical comparisons are performed computationally, not by the language model. The LLM interprets the results, not the numbers. A magnitude cross-validation layer independently verifies every claim.

Chase includes a self-improvement loop called **chase-calibrate**: the agent triages a failure, an adversarial reviewer critiques the triage, the agent fixes issues, and the cycle repeats until the reviewer passes. In live calibration, this converged in 2 iterations with zero fabricated numbers.

The envisioned production workflow watches Asana for incoming model review tasks, waits for E2E results, and automatically posts structured triage reports — reducing the MSP/MSM review queue from a pile of raw test failures to pre-analyzed, actionable summaries.

### Marshall: Zero-Infrastructure Agentic Coding

> **Impact:** Autonomous implementation of Linear tasks as pull requests, with adversarial review, real-infrastructure verification, and auto-rework on failure. No custom infrastructure — runs entirely within Claude Desktop.

Marshall is a three-stage autonomous coding pipeline. Given a set of Linear tasks, it:

1. **Implements** each as a PR using Claude Code with full repo context
2. **Verifies** the implementation using haus-development on real infrastructure (not just linting — actual build and test execution)
3. **Reviews** adversarially with binary PASSED/FAILED scoring

If the review fails, Marshall automatically reworks the implementation and re-verifies, looping until it passes or hits a retry limit.

The key architectural decision was **zero custom infrastructure**. Marshall runs as Claude Desktop routines, leveraging existing CI/CD and development tooling. This means the team can adopt it incrementally with no ops overhead. The system was designed for engineering tasks that are well-specified in Linear tickets, allowing the team to focus cognitive effort on design, architecture, and ambiguous problems.

### haus-oncall: AI-Powered Oncall Investigation

> **Impact:** Built from scratch: multi-source alert triage, PR review with learned patterns, shift handover notes, and work summary generation. 15 PRs, +5,852 lines. Later migrated to haus-claude as a shared org resource.

The haus-oncall system is a Claude Code skill-based oncall investigation toolkit. It aggregates alert context from PagerDuty, Slack, Argo workflows, GCS logs, Datastore, and Linear, then performs structured triage using runbook-trained reasoning patterns. The multi-agent parallel PR review system runs domain-specific reviewers (correctness, performance, security, style) concurrently and synthesizes their findings.

Investigation logging is automatic: every oncall investigation produces structured notes that feed into shift handover documents. This creates institutional memory that persists across rotation boundaries, solving the perennial oncall problem of lost context between shifts.

### Runbook Agent: Autonomous Incident Resolution (Design Phase)

> **Impact:** Comprehensive design for an autonomous agent that receives production alerts, matches them to the correct runbook, and executes resolution steps with a strict READ/RETRY/WRITE permission model. Currently in design/ideation phase.

The Runbook Agent design represents the next evolution of our oncall automation. The core insight is separating **build-time intelligence** from **runtime execution**: all runbooks are ingested into a knowledge graph at build time, compiled into executable LangGraph definitions, and validated against a golden dataset using a three-tier evaluation framework (white-box, glass-box, black-box via LangFuse).

At runtime, the agent follows a pre-compiled graph rather than improvising from raw runbook text. The only fuzzy step is initial alert-to-entry-point matching. READ and RETRY operations execute autonomously; novel WRITE operations pause for human approval via Slack. The design includes a Temporal-based durable execution runtime for production reliability, with LangFuse providing correctness observability and LLM quality monitoring.

The system is designed for continuous improvement: production traces grow the golden dataset, and regeneration is event-driven (runbook edits trigger rebuilds) with a weekly floor.

---

## Platform Infrastructure

### MVR v3: Automated E2E Test Framework

Designed and built a complete end-to-end model validation test framework from scratch. The system spans three repositories (cmmm-serving, cmmm-metaflow, cmmm-science) and delivers: 47 test cases covering every cMMM API endpoint, a reusable GitHub Actions workflow, Asana/Datastore/GCS integrations for result tracking, and HTML validation reports posted directly to Asana tasks.

The UAT phase validated the framework against 425 candidate models across 53 org/KPI combinations, demonstrating a **0.5% false positive rate** and **89.4% per-test agreement** with the previous manual review process (MVR v2). This directly enabled the proposal to launch E2E tests as the automated promotion gate, with a roadmap to 1,000 models per week.

The auto-merge promotion pipeline completes the automation loop: models that pass E2E tests are auto-promoted to production via automatic PR merge, with guards that block auto-merge for manual training runs where human review is required.

### Run-over-Run (RoR) Validation Report System

Created 20+ Plotly chart and table generators for model validation reports in cmmm-science. The architecture uses a schema-driven design: base classes define the contract, validation data is separated from presentation logic, and GCS-backed integration tests ensure rendering fidelity. This system produces the visual reports data scientists use to review model quality across training runs.

### OpenTelemetry Observability Stack

Instrumented the full cMMM prediction path (harbor → cmmm-serving → cmmm-science) with OpenTelemetry: W3C trace context propagation for distributed tracing, manual prediction handler spans, custom metrics (prediction duration/count/errors, model load duration), fine-grained histogram bucket boundaries in otel-python-haus, FastAPI auto-instrumentation, Sentry sample rate optimization, and a GCP Cloud Monitoring dashboard with alert policies. 9 PRs across 5 repositories.

### Crucible Platform (Design Phase)

Authored 4 Notion design documents for the Crucible Platform: 1-click ephemeral science testing environments that allow applied scientists to experiment with model changes without waiting for merges to main and full deployments. The design covers PRD, executive architecture, low-level implementation, and FAQ. Crucible directly addresses a bottleneck in the science iteration cycle.

---

## Developer Experience

Deployed Claude Code PR review GitHub Actions workflows and comprehensive developer documentation (CLAUDE.md, CONTRIBUTING.md, architecture.md) across 9 repositories org-wide. This establishes consistent AI-assisted code review standards and onboarding infrastructure, reducing the ramp-up time for new engineers and ensuring every repo has discoverable, up-to-date documentation.
