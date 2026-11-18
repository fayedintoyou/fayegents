# The Evolution of the AI Developer

> **Format:** 10-minute talk · 6 slides  
> **Perspective:** Late 2026, looking toward 2027  
> **Thesis:** The next leap in AI-assisted development will come less from generating more code and more from engineering environments where agents can safely produce *verified outcomes* with less human intervention.

This is a public, tool-agnostic presentation outline. It describes emerging patterns and a proposed engineering direction, not a product roadmap or a prediction that every team will operate autonomously next year.

---

## Slide 1 — The developer is evolving (1:00)

**On slide**

**Code producer → Agent supervisor → Systems designer**

- **Code producer:** Human implements; AI assists with suggestions and small tasks.
- **Agent supervisor:** Human delegates scoped work; agent implements; human steers and reviews.
- **Systems designer:** Human defines intent, constraints, verification, and autonomy boundaries; agents execute within them.

**Speaker notes**

These aren't exclusive or sequential job titles. Engineers will still write code, solve hard problems, and make architecture decisions. What's changing is the *center of gravity*: from producing every implementation directly, to designing the conditions under which implementations can be produced and trusted.

**Transition:** Why now? Because the capability of the underlying agents has changed.

## Slide 2 — The frontier has moved (1:30)

**On slide**

**From interactive coding sessions to sustained, parallel execution**

- Longer-running tasks with less mid-session steering.
- Multiple independent sessions operating concurrently.
- Agents that run, inspect, debug, and revise their own changes.
- Early examples of sessions coordinating other sessions.

**Speaker notes**

Recent practitioner talks from teams building coding-agent products describe hours-long execution, many concurrent sessions, and increasingly automated change delivery. Those are *reported examples from particular organizations*, not proof that the same throughput or reliability generalizes to every codebase.

The interesting development isn't the raw number of pull requests. It's that the human no longer necessarily has to approve every intermediate command, manually shepherd every attempt, or read every line of every change. That creates a new engineering problem: what takes over when the human steps out of the inner loop?

**Transition:** The bottleneck moves from producing implementations to establishing confidence in them.

## Slide 3 — The bottleneck has moved (1:30)

**On slide**

| Historically optimized for | Increasingly constrained by |
| --- | --- |
| Writing implementations | Defining the intended outcome |
| Repetitive mechanical work | Verifying behavior and invariants |
| Individual coding throughput | Integration and architectural coherence |
| Reviewing each change by hand | Assessing evidence and operational risk |

**Code is becoming cheaper to produce. Confidence is not automatically cheaper.**

**Speaker notes**

A coding agent can generate a technically plausible change that solves the wrong interpretation of the problem. It can pass the tests that exist while missing an unstated requirement. It can replicate architectural debt because that debt looks like the dominant pattern in the repository.

The goal cannot be maximizing tokens, PRs, or generated lines. A better optimization target is *verified, intent-aligned outcomes per unit of human attention and total cost*.

**Transition:** That changes where we invest our engineering effort.

## Slide 4 — Verification is the foundation of autonomy (2:00)

**On slide**

**Make the correct path easy; make known failures detectable; make critical checks unavoidable.**

1. **Architecture and APIs:** Constrain the solution space; remove common footguns.
2. **Deterministic checks:** Types, lint rules, dependency boundaries, contract tests.
3. **Runtime verification:** Run the application; inspect behavior, screenshots, traces, and performance.
4. **Enforcement:** Hooks, CI, permissions, and approval policies.
5. **Prose instructions:** Reserve for intent, context, and judgment that code cannot express.

**Speaker notes**

Two complementary lessons recur in practitioner reports:

- **The codebase is part of the agent's input.** Bad patterns get copied. If an agent repeatedly makes a mistake, ask whether architecture, a better API, or a deterministic rule can prevent that class of error rather than appending another warning to an instruction file.
- **Requesting a check is not enforcing a check.** A prompt can tell an agent to run tests; a required CI check, policy gate, or properly configured hook can enforce a specific condition independently of model memory.

Imagine an agent changing an API. The meaningful definition of 'done' is not 'wrote code and said tests passed.' It might require schema compatibility, consumer contracts, integration tests, and evidence from a running application. Failed checks should generate actionable feedback so the agent can correct the result.

Verification is broader than test volume. Tests can pass while missing intent. Independent review, executable acceptance criteria, and human judgment remain important—especially for changes with high consequences.

**Transition:** Once one inner loop is reliable, we can scale it.

## Slide 5 — Software factories emerge from reliable inner loops (2:00)

**On slide**

```text
Human intent and acceptance criteria
                ↓
     Orchestration / scheduling
                ↓
   Isolated agent execution loops
     implement → verify → revise
                ↓
    Independent review + CI gates
                ↓
      Approved, delivered change
                ↺
     Evidence and lessons feed back
```

**The factory is the outcome, not the starting point.**

**Speaker notes**

Custom agent workflows, lifecycle hooks, durable task context, isolated cloud execution, and independently evaluated results make more sustained autonomy possible. But orchestration by itself doesn't improve a faulty development loop. If each agent produces unreliable changes, orchestrating more agents scales that unreliability—and the verification bill.

The useful progression is:

- Start with a reproducible, agent-ready repository.
- Build a strong implement–execute–verify–revise loop.
- Establish external safety boundaries and risk-based approval.
- Package what works into reusable workflows.
- Only then automate initiation, parallelism, and delivery where evidence justifies it.

The guardrails differ by risk. Updating documentation is not the same as modifying authorization logic or production infrastructure. An isolated runtime reduces some risks, but anything it pushes or sends outside that runtime still has consequences.

**Transition:** This suggests a practical investment agenda rather than a race to create the most elaborate orchestrator.

## Slide 6 — What to build toward in 2027 (2:00)

**On slide**

**Three investments**

1. **Agent-ready codebases** — Discoverable architecture, explicit contracts, reproducible dev environments, fewer ambiguous footguns.
2. **Verification infrastructure** — Runnable acceptance criteria, CI and lifecycle checks, runtime inspection, evidence, risk-based review.
3. **Reusable automation** — Shared capabilities, programmable agent workflows, isolated execution, context and evaluation that survive individual sessions.

**Measure outcomes, not agent activity.**

**Speaker notes**

As coding-agent ecosystems expose more programmable SDKs, customization points, and hosted execution environments, developers can construct richer workflows without inventing every primitive themselves. These are general industry capability areas, not a claim that a particular organization will ship them on a particular date.

What matters now: improve the inner loop. Make the next failure machine-detectable when possible. Give agents the tools to observe their own results. Create shared primitives instead of every team building a bespoke multi-agent framework.

Potential measures: lead time from intent to verified change; escaped defects; human interventions per completed outcome; cost per successful outcome; repeatability of checks; and operational impact. PR count and token throughput alone are poor proxies for quality.

**Close**

> We spent the first phase of AI-assisted development teaching developers to work with agents.
>
> The next phase is teaching our engineering environments to support agents.
>
> **The AI developer of 2027 isn't just someone who knows how to use an agent. It's someone who knows how to build a system that can be trusted with one.**

---

## Notes on claims and sources

This presentation synthesizes two practitioner talks shared in transcript form:

- **Lauren (“Potato”) — codebase constraints, organic architecture, verification, and high-throughput agent workflows.** Examples include structural lint rules, restricting error-prone APIs, runtime and formal verification, and automated release testing.
- **Lydia (Anthropic Claude Code team) — changing agent execution practices.** Examples include longer autonomous sessions, permission classifiers versus enforced restrictions, sandboxing, cloud isolation, lifecycle hooks, CI, independent review, and multi-session orchestration.

Their throughput and reliability numbers are self-reported case studies, not independently audited benchmarks. The proposed direction here is an interpretation of their shared engineering ideas; it is not a verbatim account or an endorsement of every technique described.

### Related reading in this repository

- [Agentic Development: A Vision for Verified, Composable Engineering](agentic-development-vision.md)
- [Determinism in Agent Workflows](determinism-in-agent-workflows.md)
