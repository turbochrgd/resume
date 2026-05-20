# Technical Vision

**An Agentic AI Platform for the cMMM Model Lifecycle**

May 2026 and Beyond

---

## Where We Are

Over the past nine months, we have built the foundational components of an AI-augmented model lifecycle. Each system — MVR v3 for automated validation, Chase for agentic triage, Marshall for autonomous coding, haus-oncall for AI-powered investigation, the Runbook Agent design for autonomous incident resolution — was built to solve an immediate problem. But taken together, they describe something larger: the skeleton of a fully autonomous model operations platform.

Today, these systems are connected by human orchestration. A model trains, E2E tests run, if they fail someone triggers Chase, if the model promotes and breaks something someone triggers haus-oncall, if a runbook needs execution someone reads and follows it. The vision is to connect these into a continuous, agent-driven pipeline where humans intervene by exception, not by default.

---

## The Vision: Autonomous Model Lifecycle Operations

> **North Star:** A cMMM model trains, validates, promotes, serves, and self-heals with zero human intervention for the 80% of cases that are routine. Humans focus on the 20% that require judgment: model quality decisions, customer-specific tuning, novel failure modes, and product direction.

### Phase 1: Automated Validation and Promotion (Largely Complete)

This is where we are today. MVR v3 provides automated E2E validation with 47 tests. The auto-merge pipeline promotes passing models to production. The system has been validated against 425 candidate models with a 0.5% false positive rate. The roadmap targets 1,000 models per week.

**Remaining work:** Improve E2E test pass rates (currently around 42%), expand test coverage to new API endpoints, and integrate Chase triage reports into the auto-promotion decision logic so borderline cases get escalated rather than blanket-rejected.

### Phase 2: Agentic Triage at Scale (In Progress)

Chase currently runs from a human's laptop. The next step is cloud execution: an agent that watches Asana for incoming model review tasks, waits for E2E results, and automatically posts structured triage reports. This eliminates the human trigger step and makes triage truly continuous.

The traffic-light system (Green/Yellow/Red) is the key design choice: it does not try to make the final promotion decision. It compresses the information and presents a recommendation. The human reviewer confirms or overrides. Over time, as confidence in the Green-light accuracy grows, automatic re-promotion of Green-light models becomes viable — further reducing the human review queue.

### Phase 3: Autonomous Incident Resolution (Design Complete)

The Runbook Agent design represents the next major capability. The system ingests all cMMM operational runbooks into a knowledge graph, compiles them into executable state machines (via LangGraph/Temporal), and follows them autonomously at runtime. The strict READ/RETRY/WRITE permission model ensures safety:

- **READ operations** — execute freely (queries, status checks, log reads)
- **RETRY operations** — execute freely (re-running the same operation with the same parameters)
- **WRITE operations** — pause and request human approval via Slack

The three-tier validation framework (structural checks, golden dataset evaluation, integration testing) ensures that every regenerated graph maintains or improves correctness. The deployment gate is a ratchet: scores can only go up. Production traces feed back into the golden dataset, creating a continuous improvement loop.

This is the single highest-leverage investment for reducing oncall burden. Today, an on-call engineer reads a runbook, interprets system state, and manually executes each step. The Runbook Agent does all of that autonomously for the known failure modes, escalating to the engineer only for genuinely novel situations.

### Phase 4: Closed-Loop Model Lifecycle (Future)

The endgame connects all the pieces:

1. A model **trains** on Sunday
2. E2E tests **validate** it
3. If it passes, auto-merge **promotes** it
4. If it fails, Chase **triages** the failure: Green-light models re-promote, Yellow-light models get reviewed with a pre-analyzed report, Red-light models are rejected
5. Once promoted, the OTel observability stack **monitors** prediction latency, error rates, and model load times
6. If an anomaly is detected, the Runbook Agent **diagnoses and resolves** the issue
7. If it encounters a novel failure mode, it **escalates** with full context to the on-call engineer
8. The resolution becomes a new golden dataset entry for **future automation**

At this point, the human role shifts from operating the system to improving the system: better tests, better triage heuristics, better runbooks, better model architectures. The operational load becomes proportional to the rate of novel problems, not the volume of models.

---

## Agentic AI as Architecture Pattern

The common thread across all these systems is a specific pattern for building reliable AI agents in production:

- **Deterministic scaffolding, stochastic reasoning.** The agent follows a pre-built graph (runbook), a structured pipeline (Chase), or a defined workflow (Marshall). The LLM provides reasoning within the scaffolding, not the scaffolding itself. This makes the systems predictable and auditable.

- **Adversarial self-review.** Every agentic system includes a built-in critic. Chase has chase-calibrate. Marshall has adversarial review with binary PASSED/FAILED scoring. The Runbook Agent has a three-tier validation framework. Trust is earned through verification, not assumed.

- **Human-in-the-loop by exception.** The default is autonomous execution. Humans intervene for novel write operations (Runbook Agent), ambiguous triage cases (Chase Yellow-light), and review failures (Marshall). The system routes human attention to where it has the highest marginal value.

- **Continuous improvement through production feedback.** Chase's calibration loop, the Runbook Agent's golden dataset growth from production traces, and Marshall's rework cycle all follow the same pattern: production experience makes the agent better over time without manual retraining.

---

## Scalability Projection

The systems built and designed over the past nine months provide a roadmap for 10x-ing our model volume without proportionally scaling the team:

| Capability | Current State | With Full Vision |
|-----------|--------------|-----------------|
| Models validated/week | ~50 (manual review for 58%) | 1,000+ (human review for <10%) |
| Mean time to triage | ~24 hours | Minutes (Chase auto-triage) |
| Oncall incident resolution | Fully manual (read runbook, execute) | Autonomous for known patterns |
| Development task throughput | 1 engineer = 1 task stream | Marshall multiplies with agent tasks |
| Cross-functional handoffs | Slack threads, lost context | Linear with automated intake |

---

## Enabling Conditions

Realizing this vision depends on a few key investments:

- **Cloud execution for Chase.** Moving from laptop-based to Asana-triggered cloud execution is the immediate priority. This is the smallest effort with the largest impact on MSP/MSM team capacity.

- **Runbook Agent implementation.** The design is complete. Implementation requires building the knowledge graph ingestion pipeline, the compiler, and the Temporal-based execution runtime. This is a multi-quarter effort but the design work de-risks it significantly.

- **E2E test pass rate improvement.** The science team's ongoing work to improve pass rates directly reduces the volume of models that need triage. Every percentage point improvement in pass rate compounds with Chase's triage efficiency.

- **Crucible implementation.** Unlocking faster science iteration cycles means more experiments, better models, and higher E2E pass rates — feeding the same flywheel.

---

## Conclusion

The technical vision is not to build AI for its own sake. It is to build a model operations platform where the steady-state workload is handled by agents, and human expertise is concentrated where it creates the most value: designing better models, making better product decisions, and solving the problems no agent has seen before. The foundation is built. The path forward is execution.
