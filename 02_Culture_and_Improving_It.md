# Culture and Improving It

**Building an Engineering Culture of Automation, Shared Ownership, and AI-Augmented Work**

Satadru Biswas — Staff Software Engineer, CMMM Platform
September 2025 — May 2026

---

## Thesis: Culture Scales When Toil Doesn't

The strongest engineering cultures share a trait: they actively refuse to let manual toil become permanent. Every recurring manual process is treated as a design failure — not something to endure, but something to engineer away. Over the past nine months, I have worked to embed this principle into how we build, operate, and collaborate on the cMMM platform.

The projects I have shipped are not just technical deliverables — they are cultural interventions. Each one replaces an implicit expectation that someone will manually handle a task with an explicit system that handles it automatically, freeing the team to invest in higher-value work.

---

## Normalizing Agentic AI as a Daily Tool

### From Novelty to Expectation

When I started building haus-oncall in January 2026, AI-assisted development was a curiosity at Haus. By May 2026, Claude Code is a standard part of our PR review workflow across 9 repositories, Marshall is autonomously implementing Linear tickets as PRs, and Chase is triaging model validation failures that previously required 24-hour turnaround from senior team members.

The cultural shift was not a single announcement. It was a series of small, concrete demonstrations: the first oncall investigation that surfaced context from 5 sources in seconds, the first PR review that caught a bug a human reviewer missed, the first model triage report that compressed a 2-hour review into a 5-minute confirmation. Each one moved the conversation from "can AI help here?" to "why aren't we using AI here?"

### Making AI Assistants Accessible, Not Exclusive

A key cultural design choice: every agentic system I built is packaged as a shareable skill, not a personal workflow. The oncall investigation skills were migrated from my personal haus-oncall repo to the shared haus-claude repo so every engineer can invoke them. Marshall runs as Claude Desktop routines with zero custom infrastructure, meaning any team member can adopt it without ops overhead. Chase is designed to eventually run autonomously on incoming Asana tasks, removing even the step of a human triggering it.

This matters because AI tools that only one person can run are productivity hacks. AI tools that the whole team can run are cultural shifts.

---

## Reducing Oncall Burden Through Process Design

### The Oncall Triage Overhaul

Our cMMM customer base grew significantly over the past year, and the operational issue volume grew with it. Last Friday's oncall retro surfaced a clear pattern: Slack worked for quick conversations, but at our current scale, context was getting fragmented across threads, alerts blended into general chatter, and it was hard to track what had been addressed versus what was still open.

I proposed and drove the initiative to move cMMM operational triage to Linear Projects, with a clear structure:

- An **AI agent monitors incoming Slack alerts** and creates (or deduplicates) tasks on a cmmm-eng-oncall board
- The **engineering oncall triages from one place** rather than scanning multiple Slack threads
- When an issue belongs with Science or MSM, the oncall **adds context and routes the task** to the respective board — clean handoffs, preserved context, queryable audit trail

The cross-functional response was strong and immediate. The MSM, Science, and Engineering teams all aligned on adopting Linear as the single source of truth, with the understanding that even if discussions start in Slack, the DRI ensures the summary lands in Linear. The plan includes a 5-minute walkthrough in standup and a 1-pager to formalize adoption.

### Shift Handover as Institutional Memory

One of the subtlest cultural problems in oncall is knowledge loss at rotation boundaries. The outgoing oncall knows what's in progress, what's been tried, and what's fragile. The incoming oncall starts from scratch.

I built automatic investigation logging into haus-oncall so every investigation produces structured notes that feed into shift handover documents. This isn't just a tool — it's a cultural norm: the handover is a first-class artifact, not an afterthought.

---

## Documentation as Cultural Infrastructure

### Org-Wide Developer Documentation Rollout

In April 2026, I deployed CLAUDE.md, CONTRIBUTING.md, and architecture.md files across 9 repositories. This was 17 PRs of infrastructure, but the cultural impact is larger than the code: every cMMM repository now has a discoverable, consistent guide to how it works, how to contribute, and how AI tools interact with it.

This sets an expectation: documentation is not optional, it is part of the codebase. New engineers can onboard faster. AI assistants can operate more effectively. Tribal knowledge becomes written knowledge.

### Design Documents as Shared Thinking

I authored 24 Notion documents over this period, including design docs for Crucible, MVR v3, the Runbook Agent, Marshall, and the E2E launch proposal. These documents serve a dual purpose: they are the technical record of how systems were designed, and they are the shared thinking artifact that invites cross-functional input before code is written.

The Crucible PRD, for example, explicitly addresses the science team's iteration bottleneck and was shaped by conversations with applied scientists about their workflow pain points.

---

## The Compounding Effect

The cultural pattern I am working to establish is a flywheel:

1. Automate a manual process
2. Free up team capacity
3. Use that capacity to identify the next manual process to automate
4. Repeat

MVR v3 automated model validation. Chase automated failure triage. Marshall automated task implementation. The Runbook Agent design targets incident resolution. Each system builds on the last and expands the team's capacity to focus on the genuinely hard problems — model quality, customer impact, and product direction.

Culture does not change because someone declares a new value. It changes because people experience a different way of working and don't want to go back.
