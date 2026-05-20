# Working with Cross-Functional Partners

**Scaling Every Function Through Shared Systems and Process Design**

Satadru Biswas — Staff Software Engineer, CMMM Platform
September 2025 — May 2026

---

## The Scalability Thesis

Every project I have shipped over the past nine months was designed to answer the same question: how do we grow our capacity without growing our headcount proportionally? The answer, in every case, has been to identify where a function's time is spent on repeatable, codifiable work — and build systems that do it automatically, so the humans can focus on judgment calls, customer relationships, and creative problem-solving.

This is inherently cross-functional work. The systems I build serve data scientists, applied scientists, MSP/MSM teams, and fellow engineers. Each required deep collaboration to understand the real workflow, not just the idealized version.

---

## Scaling Data Scientists: MVR v3 and RoR

### The Problem

Data scientists review every model trained by the cMMM system. Before MVR v3, this meant manually inspecting validation outputs, comparing metrics across training runs, and making promotion decisions based on tribal knowledge and ad hoc scripts. The review queue was a bottleneck: models sat waiting for human attention.

### What I Built

MVR v3 is a 47-test automated validation framework that runs against every model at training time and posts structured HTML reports directly to Asana. The Run-over-Run system generates 20+ visual comparison charts and tables so scientists can review at a glance instead of digging through raw data. Together, these systems compress a multi-hour review into a quick visual scan for the straightforward cases, and a detailed, pre-analyzed report for the complex ones.

### Cross-Functional Collaboration

The UAT phase was a joint effort. I analyzed 425 candidate models across 53 org/KPI combinations and co-authored the launch proposal with Katya Jardim from the science team. The 0.5% false positive rate and 89.4% per-test agreement with MVR v2 were the numbers the science team needed to trust the system enough to adopt it as the automated promotion gate.

The auto-merge pipeline that followed closes the loop: models that pass E2E tests promote automatically, while manual training runs require explicit human review. This design was shaped by conversations with the science team about which decisions should be automated and which should not.

---

## Scaling Applied Scientists: Crucible

### The Problem

Applied scientists experimenting with model changes had to merge to main and deploy to test their hypotheses. This created a multi-day feedback loop and made the shared development environment fragile — one experiment could break the pipeline for everyone.

### What I Designed

Crucible is a 1-click ephemeral science testing environment. Each experiment gets its own isolated sandbox with production-like data, runs in parallel without affecting main, and tears down automatically. I authored 4 design documents (PRD, executive architecture, low-level implementation, FAQ) that were reviewed by both engineering and science leadership.

### Cross-Functional Collaboration

The Crucible design was driven by direct conversations with applied scientists about their iteration pain points. The PRD explicitly frames the problem in their terms: "I want to test a model change without waiting for a deploy cycle." The architecture was designed to be invisible to the scientist — they push code, they get a test environment, they see results. The infrastructure complexity is entirely behind the curtain.

---

## Scaling MSP/MSM Teams: E2E Tests and Chase

### The Problem

MSP and MSM team members manually review models that fail E2E validation. With only about 42% of models passing E2E tests at the time of measurement, the remaining 58% required manual review with a mean time to review of approximately 24 hours. As our model volume tripled in six months (to about 50 models), this was not sustainable.

### What I Built

The E2E test framework provides the first automated gate: passing models promote without human intervention. For the failing models, Chase provides an agentic triage layer: 8 parallel domain-specific analysis tests with a traffic-light severity system:

- 🟢 **Green:** E2E tests failed due to a known, explainable change (prior updates, new experiment added, significant spend baseline deviation)
- 🟡 **Yellow:** Some failures that require human review; the report clearly surfaces issues and suggests actions
- 🔴 **Red:** Fundamentally broken model; reject immediately

The deterministic CSV diff pipeline ensures no numerical hallucinations in the analysis.

### Cross-Functional Collaboration

I presented the Chase triage agent to the broader team including MSP leads, engineering managers, and science leadership, explicitly framing it as a tool that reduces the effort required to review models and drives down mean time to review. The response validated the approach: the team identified the next step as building cloud execution capability so Chase can run autonomously on incoming Asana tasks rather than from a human's laptop.

The structured triage reports Chase produces are designed for MSP/MSM consumption: they include full failure analysis, links to relevant Argo flows and GitHub Actions runs, and actionable items for the reviewer. The reviewer's job shifts from "figure out what happened" to "confirm the agent's assessment."

---

## Scaling Engineering: haus-oncall, Marshall, and Oncall Process

### Scaling Development Velocity

Marshall allows the engineering team to delegate well-specified Linear tasks to an autonomous coding agent that implements, verifies on real infrastructure, and adversarially reviews its own output. This is not about replacing engineers — it is about removing the mechanical work (boilerplate, straightforward feature implementation, test scaffolding) so engineers can focus on design, architecture, and hard problems.

### Scaling Oncall Operations

The oncall triage overhaul I proposed moves the entire cross-functional cMMM triage process to Linear Projects, with:

- **Automated intake:** An AI agent monitors incoming Slack alerts and creates/deduplicates tasks on a cmmm-eng-oncall board
- **Clean handoffs:** When an issue belongs with Science or MSM, the eng oncall adds context and routes the task to the respective board
- **Team-specific boards:** MSM and Science oncalls get their own views, so routed tasks surface clearly without the noise of the full queue
- **Queryable audit trail:** Linear gives us a record we can reference for recurring issues and use to spot patterns over time

This was explicitly framed as a cross-functional improvement: the proposal was shared with Engineering, MSM, and Science oncall leads, and the response was aligned adoption across all three functions. The AI agent that monitors Slack alerts and creates Linear tasks is already functional. The next step is integrating it with the existing Data/ML oncall issues project in Linear so there is continuity with the current process.

---

## Org-Wide Developer Experience

The Claude Code PR review workflow and developer documentation rollout across 9 repositories was a cross-functional infrastructure investment. Every cMMM repo now has consistent, discoverable documentation and AI-assisted code review. This benefits not just the core engineering team but every data scientist, applied scientist, or cross-functional partner who touches the codebase.

---

## Summary: Every Function, Multiplied

| Function | Bottleneck | System Built | Scalability Effect |
|----------|-----------|--------------|-------------------|
| Data Scientists | Manual model review | MVR v3 + RoR | Review compressed to visual scan |
| Applied Scientists | Multi-day test cycles | Crucible (design) | 1-click ephemeral environments |
| MSP/MSM Teams | 24-hr manual triage | E2E Tests + Chase | AI-triaged, minutes to confirm |
| Engineering | Mechanical coding work | Marshall + haus-oncall | Autonomous task implementation |
| All XFN | Fragmented oncall | Linear triage process | Single source of truth |
