# Agentic Development: A Vision for Verified, Composable Engineering

> **Status:** Foundational vision / evolving design thesis, not a declaration that all capabilities already exist.
>
> **Core thesis:** Agentic development is the systematic translation of human intent into verified outcomes through an evolving combination of human judgment, model reasoning, deterministic software, durable context, and composable capabilities. The goal is **verified autonomy**, not autonomous activity for its own sake.

## 1. The problem is larger than writing code

A powerful coding model can produce a technically correct implementation of the wrong interpretation. It can pass tests it has seen while missing the behavior those tests were meant to represent. It can repeatedly rediscover procedures another agent already established, forget a decision from a previous session, or optimize locally while drifting from the purpose of the larger project.

Increasing model capability helps, but does not by itself solve these problems. The surrounding engineering system must preserve intent, allocate responsibility, verify results, accumulate knowledge, and decide when work requires a person.

This vision treats agentic development as an **operating model** spanning agents, repositories, CI, specifications, teams and tools—not as a single orchestration framework or a requirement to adopt one methodology.

### Desired outcome

A developer or team should be able to state a meaningful objective, preserve the reasons and boundaries behind it, have the system select appropriate working methods, execute bounded pieces of work, evaluate their outcomes, and reuse what was learned. Human attention should concentrate on direction, ambiguity, exceptions and consequential acceptance—not manually shepherding every intermediate action.

The important unit of progress is a **verified, intent-aligned outcome per unit of human attention and total cost**, not number of agents, tokens, PRs, generated tests or automated steps.

## 2. Three actors with different strengths

**Humans** are the authority on purpose and consequential tradeoffs. They are best positioned to recognize whether an outcome is actually wanted, but their attention is limited. Human involvement should be retained where it changes the quality or legitimacy of a decision.

**Models** reason flexibly under ambiguity: exploring unfamiliar code, decomposing tasks, researching, designing, implementing, interpreting failures and judging semantic quality. Their decisions are probabilistic and can be wrong, self-justifying or overfit to known criteria.

**Code** performs repeatable operations cheaply and predictably: parsing, transforms, linting, tests, structural checks, policy checks, reference resolution and mechanical orchestration.

The aim is not to replace model reasoning with a giant fixed pipeline. It is to **minimize unnecessary inference while preserving useful agent autonomy**. As patterns stabilize, move the computable portions into scripts and validators; leave genuine judgment with models and consequential authority with humans.

An executable check and an enforced gate are also different things. A script that an agent *could* run does not guarantee that it *will* run. Distinguish guidance (skills and instructions), deterministic primitives (tools and checks), and enforced boundaries (CI, permissions, approvals and policies). See [Determinism in Agent Workflows](determinism-in-agent-workflows.md).

## 3. Intent is a durable engineering artifact

The **intent gap** is the distance between the human's actual desired outcome and the agent's plausible interpretation. More words are not always the cure; a lengthy specification can still encode the wrong interpretation.

Preserve, in a form downstream workers can retrieve:

- **What:** the outcome, behavior or change sought.
- **Why:** the motivation, user need, tradeoff or problem.
- **Why not:** rejected interpretations, exclusions, non-goals and unacceptable shortcuts.
- **Constraints:** compatibility, security, performance, cost, UX and operational invariants.
- **Evidence:** what would count as success, and which questions remain unsettled.
- **Decision history:** alternatives considered, reasons for choices, and what would warrant revisiting them.

For consequential work, an explicit plan is an agreement on **what we are building and what we are not**. It should be reviewed before implementation, but planning should not become a universal ceremony for trivial work.

Decomposition must preserve intent across boundaries. A worker completing its narrow assignment is not proof that the assembled product satisfies the originating objective. Trace from origin and rationale through plans, changes, tests, reviews and observed outcomes.

Specifications such as OpenSpec can represent selected requirements and changes; approaches such as linked-intent development can represent relationships between intent and implementation. The durable context layer should cooperate with these methods, not require that they be replaced.

## 4. Context is shared, but not indiscriminately

Agent sessions are transient; engineering knowledge should not be. Repository-backed context offers a useful foundation because it is versioned, reviewable, branchable, attributable and naturally connected to the work it describes.

Context may include working preferences, project descriptions, architectural decisions, rationale, team practices, reusable skills, tool guidance, spec links, test evidence, historical findings and unresolved questions.

**A hub is a navigable body of context, not necessarily one document or one fixed schema.** It may expose multiple kinds of content, directories, capabilities and relationships, while remaining discoverable through MCP and other clients. Context Kit should help clients locate relevant material and understand its authority without dictating every consumer's execution workflow.

### Provenance and trust

Every durable claim or artifact should be able to express *where it came from* and *what confidence or authority it deserves*. OKF-style concept metadata is one useful representation, but the semantics must not depend on all content being written in OKF.

Useful optional metadata and links include:

- Origin: repository, path, commit/ref, document, issue, PR, meeting, conversation or external source.
- Authorship: human or agent identity, with agents never impersonating human verification.
- Time: creation, observation, update, verification and expiration/freshness.
- Status: proposed, draft, observed, reviewed, adopted, superseded or deprecated.
- Authority: owner, maintainer, approving human, canonical source and scope.
- Evidence: test results, review findings, links to artifacts, supporting and contrary observations.
- Relationships: derived-from, depends-on, implements, contradicts, supersedes, verified-by.
- Confidence and limits: what was actually tested, where the conclusion applies and where it remains speculative.

A model-generated note is not a human-verified fact. A test passing is not proof of user satisfaction. A branch preview is not a production deployment. These distinctions should survive summarization and retrieval.

Metadata should be **progressively adoptable**: rich frontmatter when appropriate, sidecars or indexes for formats that cannot carry it, links and conventions for existing repositories, and minimal markers for lightweight notes. Avoid forcing every team to migrate its documents into a proprietary format.

### Repository provenance and publication

Discovery should expose not only *what a hub contains* but, when authorized, *where its source of truth lives*: repository upstream, canonical relative path, revision, and supported publication workflow. A read-only MCP server does not imply the underlying repository is unwritable through another authorized integration.

Clients should be able to distinguish capture of an unreviewed note, a proposed change/PR, direct authorized commit, and an unsupported write. External writes require a refresh/reconciliation story: a GitHub commit does not prove a local checkout or MCP index has already updated.

Private provenance must not be disclosed to unauthorized clients. Not every hub is Git-backed; the design must also support local-only and other backing stores.

### Team collaboration

Context is not only personal memory. Teams should be able to collaboratively propose, review, correct, supersede and maintain shared knowledge. The design should support ownership, permissions, conflicting interpretations, attribution, history and clear distinction between personal preferences, team conventions and repository-specific facts.

A team can share working methods without granting every agent permission to silently rewrite authoritative guidance. Human-reviewed knowledge and unreviewed intake should remain distinguishable. Collaborative updates should favor normal version-control review and evidence-backed changes, not opaque background mutation.

### Selective context and independence

Durability does not imply putting every fact into every prompt. A builder may need implementation history; an independent evaluator may need the original intent and final artifact **without** the builder's reasoning or held-out scenarios. A router may need only risk indicators and prior outcomes.

Context should be discoverable, scoped, cited, fresh enough for the decision and intentionally withheld where evaluation independence requires it. Provenance helps decide what to trust; context selection helps decide what to reveal.

## 5. Reusable capabilities: Lego blocks, not a mandatory framework

The reusable unit is a **repeatable capability**. It may be a skill, script, CLI, agent workflow, service, test harness or composition of these. It need not be a formally packaged executable or a new abstraction layer.

A research block, for example, might use a model to interpret the question and synthesize evidence, code to validate references and structure, and an independent fresh-context judge to assess properties that code cannot. During development, this mixture helps establish confidence that the block works. Operational use need not repeat every development-time judge on every invocation.

Reliability is contextual: a capability proven for a bounded class of tasks is not universally proven. Useful descriptions identify purpose, inputs, outputs, assumptions, limitations, failure behavior and evidence of prior success, with formality proportional to risk and reuse.

Agents should **discover existing capabilities before improvising new ones**. Otherwise a rich skill library becomes shelfware. Composition should permit research → design → implementation → testing → triage → reporting without mandating one universal pipeline.

This separation matters to Context Kit: it can expose durable context, methods, capability discovery and evidence while leaving actual execution to Claude Code, Codex, T3 Code, CI, scripts, or other runtimes.

## 6. Adaptive routing: effort proportional to risk

Not all tasks warrant the same workflow. A typo, a small UI change and a shared interface migration differ in ambiguity, blast radius and reversibility.

A task router can choose a **fast** or **full** path—or finer-grained variants—based on scope, risk, novelty, availability of deterministic checks, dependencies and historical failure rates. The precise number of agents and stages is an implementation choice, not a universal rule.

Routing can begin with simple rules for obvious cases and a lightweight model for ambiguous ones. A trained classifier becomes useful when there are enough labeled outcomes to justify it. Evaluate routing decisions because under-reviewing high-risk work is generally more costly than over-reviewing trivial work.

A full route might include human plan approval, decomposition, isolated workers, independent evaluation and integration review. A fast route might use a single worker plus automated checks. These are examples, not requirements.

The objective is to remove **unnecessary orchestration decisions** from the human while retaining deliberate escalation for meaningful uncertainty and consequence.

## 7. The execution and correction loop

The smallest autonomous loop is:

```text
Intent → agent action → deterministic checks
                      ↘ failure evidence → agent correction → checks again
```

The model reasons about how to satisfy an invariant; code establishes whether the invariant holds. A failed check should produce actionable evidence. The human should not need to mediate every retry.

Loops must be bounded by retry, cost, time or lack-of-progress conditions. Oscillation and repeated failure should trigger a different strategy or escalation, not infinite retries.

A passing suite is evidence only for the properties covered by that suite. The workflow must not equate “green” with “the human got what they meant.”

## 8. A behavior-first testing strategy for agent-generated software

The relevant question is **which test gives the most confidence that the user's intended behavior works, for the total cost of writing, running, diagnosing and maintaining it?** Do not optimize for test count, line coverage, or adherence to a prescribed unit/integration/E2E ratio.

Traditional test-pyramid advice was partly an economic response to expensive, slow and brittle broad-stack UI tests. Those concerns still exist, but the cost structure is changing. Modern browser automation, accessible locators, traces, isolated test environments, parallel execution and model-assisted test generation make realistic end-to-end scenarios easier to author, debug and maintain. In particular, agents can operate the application through its public UI much like a user, discover broken flows and turn those observations into executable regression tests.

**This favors greater emphasis on behavior-oriented integration and end-to-end verification in agentic development—but does not imply that unit tests are obsolete or that E2E should dominate every suite.** Generating a test may be cheap while running it in CI, provisioning dependencies, diagnosing failures and stabilizing it remain expensive. Flaky checks decrease trust. A broad test often detects failure without precisely locating the defect.

### Prefer evidence at the meaningful boundary

Test through the highest-value boundary that is **realistic, stable and economical for the behavior in question**:

| Verification level | Best use | Typical limitation |
| --- | --- | --- |
| Static checks, schemas and type systems | Mechanical invariants, structural validity and forbidden patterns | Cannot prove user behavior |
| Focused unit and property-based tests | Pure algorithms, calculations, state transitions, edge cases and combinatorial invariants | Can validate an implementation that is wired incorrectly |
| Integration and contract tests | APIs, database/queue/Redis behavior, serialization, cross-component interaction and consumer guarantees | May miss the assembled user journey |
| E2E / functional tests | Critical real user journeys through a public boundary, including frontend-to-backend behavior | Environment cost, slower diagnosis and occasional flakiness |
| Independent exploratory evaluation | Usability, unexpected journeys, semantic intent and conditions not anticipated in the coded suite | Probabilistic judgment; requires evidence and appropriate human acceptance |

“E2E” is not synonymous with “browser.” A command-line tool can be tested through its CLI; an MCP server through its public tool interface; a backend through a real API or message-driven flow. The meaningful property is that the test exercises the intended observable contract across the relevant boundaries.

### Test the intended product, not the code the builder happened to write

Agent-generated tests introduce a particular risk: the same agent may invent an implementation **and then invent tests that confirm its own interpretation**. Such tests can be tautological or overfit to internal details. An agent may create dozens of mocks that reproduce its assumptions, assert private call ordering, snapshot incidental markup, or modify assertions until the current implementation passes.

A useful default is to derive behavioral scenarios from the **original intent, independently established contracts and user-observable effects** before treating generated implementation tests as proof. Tests should survive reasonable internal refactoring and fail when a real behavioral guarantee breaks.

Guardrails worth considering:
- Require a behavioral reason for each new test; prefer a small number of meaningful cases over redundant test inflation.
- Use stable public interfaces, semantic selectors and observable outcomes rather than private internals wherever practical.
- Mock at genuine external boundaries, not so extensively that the integration being verified disappears.
- Include negative/error behavior and invariants when they matter, not merely happy-path examples.
- Review changes that remove, weaken, skip or auto-repair tests—especially independent acceptance tests. A test-healing agent must not silently turn a product defect into a passing assertion.
- Use regression failures, mutation testing or deliberate fault injection selectively to ask whether a test detects meaningful breakage, rather than treating coverage percentage as a quality score.
- Prefer deterministic assertions for observable behavior; use model judgment for qualitative aspects that cannot be encoded reasonably.

### Keep unit tests where they provide leverage

Good unit tests remain unusually useful for fast feedback on complicated domain logic, numerical correctness, boundary conditions, parsers, reducers, state machines and algorithms with many possible inputs. Property-based tests can express broad invariants much more economically than enumerating UI interactions. They also localize errors far faster than a failing browser flow.

Conversely, testing whether every helper was called with a particular mock argument often adds little confidence if that interaction is not the public contract. A heavily mocked unit suite can be green while the actual application is unusable.

The tradeoff is not “E2E versus unit.” It is **meaningful confidence versus test-maintenance burden**, evaluated per behavior.

### Separate exploratory agent use from durable tests

An agent independently navigating the product as a user can uncover failures that authored tests missed. Such exploration is valuable even when no reusable automated script exists yet.

When an important failure is found, capture a reproducible scenario, observed result, expected result, environment and relevant artifacts (logs, traces, screenshots) where appropriate. Convert recurring critical paths into deterministic tests. Do not confuse a model's statement “I tried the app and it works” with recorded verification evidence.

For CI, keep a small reliable suite of critical journeys and use focused checks for broader edge-case coverage. Add more E2E scenarios when they reveal gaps not economically covered elsewhere. Track runtime, flakiness, diagnosis and escaped defects; adjust the portfolio from observed evidence rather than a rigid testing pyramid or inverted pyramid.

### Relationship to vertical slices

A vertical slice is not complete merely because its internal services compile. It should demonstrate the intended outcome through an actual caller-facing boundary, with only out-of-scope collaborators stubbed. This does not require a full browser E2E test for every PR: a real CLI/API integration test may be the correct boundary. The key is proving a working path through the slice before broadening the implementation.

## 9. Independent validation, review and human acceptance

These are separate questions:

1. **Validation:** Does observable behavior meet specified expectations?
2. **Review:** Is the solution maintainable, secure, appropriate and architecturally coherent?
3. **Acceptance:** Does the integrated outcome satisfy the human's original intent?

Deterministic checks should cover mechanically verifiable properties. A fresh-context model judge can evaluate semantic quality without inheriting the builder's entire reasoning trajectory. Independence reduces some self-confirmation, but does not guarantee unbiased judgment.

### Visible development tests versus blind acceptance tests

**Held-out behavioral scenarios** provide confidence that a builder did not merely optimize to pass tests it could see. This is a deliberate **information boundary**, not simply another test category.

The implementing agent should receive the original intent, constraints, legitimate public acceptance criteria and whatever visible tests it needs to work effectively. Separately, an evaluator can own additional scenarios derived from that same original intent—ideally created or reviewed independently of the implementation—and execute them **without exposing their exact inputs, expected outputs or test code to the builder**.

A possible arrangement is:

```text
Human intent / agreed contract
     ├─→ Builder → implementation → visible checks → correction
     └─→ Independent evaluator → reserved scenarios / holdouts
                                        ↓
                           pass or actionable failure class
                                        ↓
                           builder correction or escalation
```

Isolation must be real: a file marked "hidden" inside the builder's accessible working tree is not held out. It may require separate repository permissions, evaluator-only artifacts or CI stages that reveal outcomes without granting access to the test definitions. The evaluating agent may need fresh context that intentionally excludes the builder's reasoning and self-authored tests.

Held-out scenarios should test **declared behavior**, not undisclosed product requirements or arbitrary implementation preferences. When a holdout fails, return sufficient evidence to diagnose the behavior; repeatedly disclosing exact cases can exhaust the holdout's independence. Rotate, broaden or regenerate reserved scenarios when appropriate, and preserve reproducible traces for authorized reviewers.

The builder can still author many useful tests. It simply must not be **the sole author and judge of the evidence used to certify its work**. An independent reviewer or evaluator should challenge whether the testing strategy covers plausible failures, not merely whether the suite is green. Blind tests do not guarantee correctness: validators and scenarios themselves need review, and human acceptance remains authoritative for consequential intent judgments.

Adversarial builder–critic “death loops” should not be the default. They can consume substantial compute while endlessly disputing subjective points. Use narrowly scoped adversarial examination only for material unresolved risks, with explicit exit criteria. Ordinary independent validation and review should aim at **bounded convergence**.

Reviewers should report evidence and concrete corrections, not merely generate additional opinions. A reviewer must be able to say “sufficient” and stop.

## 10. Decomposition, vertical slices and parallel execution

Decompose work into small, meaningful units with clear behavior and preserved intent. Prefer **vertical slices** that exercise a real caller-to-outcome path rather than completing disconnected architectural layers. A small PR is useful when it reduces integration risk and feedback latency, not as a metric in itself. See [Simple Workflow Ideation](workflow.md).

Independent tasks can run in separate worktrees, sandboxes and context windows. Different models can handle research, design, implementation or review according to observed suitability and cost. Isolation limits interference, but introduces merge conflicts, shared-dependency risks and coordination overhead.

Parallelism is beneficial only when work is actually independent enough. Maintain an integration step that rechecks aggregate intent, cross-slice compatibility and end-to-end behavior.

## 11. Model routing, caching and economics

Model choice is a resource-allocation decision, not a fixed hierarchy. Use stronger reasoning where ambiguity and consequences warrant it; use less expensive models for well-specified bounded work with good checks. Evaluate actual dispatches and outcomes rather than assuming a model's advertised tier predicts success on every task.

Cost includes inference, latency, retries, independent reviews, orchestration overhead, cache misses, integration work and human recovery. A cheaper worker that repeatedly fails can cost more than a stronger first pass.

Prompt caching and durable results solve related but different problems:

- **Prompt caching** avoids recomputing repeated input prefixes; stable instructions, schemas and shared context may improve cache reuse.
- **Artifact/result reuse** avoids repeating the underlying task or reasoning at all when an earlier verified result remains valid.
- **Deterministic scripts** avoid model inference for computable operations.
- **Selective context retrieval** avoids flooding every worker with irrelevant or biasing history.

Design workflows to preserve stable cacheable context when practical, while not sacrificing independence or correctness just to maximize cache hits. Durable outputs require dependency and freshness tracking; stale cached decisions can propagate errors faster than fresh reasoning.

## 12. An end-to-end conceptual lifecycle

These are **responsibilities**, not a mandatory eleven-stage ceremony:

1. **Establish intent:** desired outcome, why, why-not, constraints and success evidence.
2. **Classify:** risk, ambiguity, scope and suitable execution route.
3. **Plan selectively:** resolve consequential design decisions and obtain approval where needed.
4. **Decompose:** preserve intent in bounded work packets and identify independent slices.
5. **Discover capabilities:** reuse existing scripts, skills, methods and validated workflows.
6. **Execute:** combine model judgment with deterministic primitives in suitable isolated environments.
7. **Correct:** run objective checks, return evidence and iterate within explicit bounds.
8. **Evaluate:** verify user-observable behavior at the most economical meaningful boundary, with independent judges and held-out scenarios where uncertainty warrants.
9. **Review and integrate:** assess quality, cross-slice compatibility and system-level behavior.
10. **Accept or escalate:** reserve human attention for intent, exceptions and consequential choices.
11. **Preserve evidence:** store decisions, provenance, results, failures and lessons for future work.

The cycle should become more efficient as experience accumulates: recurring judgments may become rules, failures become checks, successful methods become reusable capabilities, and routing improves from observed outcomes.

## 13. Architectural boundaries: what Context Kit should and should not own

**Context Kit's potential role:** portable discovery and delivery of durable context and working methods; hub navigation; provenance, freshness and authority; collaborative knowledge lifecycle; capability discovery; selective context for agent roles; links to specs, implementations and verification evidence; machine-readable pointers to authoritative sources and supported publication paths.

**Not necessarily Context Kit's role:** running every agent, mandating a planning methodology, owning GitHub/CI, imposing a universal skill format, replacing OpenSpec or linked-intent tools, requiring OKF for every artifact, or providing a monolithic orchestration engine.

The valuable boundary is **portable context and working methods across heterogeneous runtimes**. A Claude Code session, Codex CLI, T3 Code orchestration, CI worker and human editor should be able to encounter the same authoritative knowledge through appropriate interfaces while retaining their own execution mechanics.

Integration can be incremental:
- Link existing repository instructions and skills rather than duplicating them.
- Represent provenance and confidence in an extensible, format-agnostic way.
- Distinguish canonical knowledge from unreviewed intake and stale observations.
- Expose the hub's backing location and authorized write paths.
- Carry intent and evidence across OpenSpec changes, slices, commits, PRs and tests.
- Make reusable capabilities discoverable and their limits visible.
- Feed observed failures and successes back into methods and routing policies.

A hub may contain multiple distinct parts; its MCP surface should expose the **hub as a whole**, not arbitrarily hide it behind one curated document. Progressive disclosure should keep navigation practical.

## 14. Measurement and falsifiable improvement

Evaluate the operating model through outcomes, not orchestration theater:

| Dimension | Possible evidence |
| --- | --- |
| Intent alignment | Human acceptance, rejected interpretations, post-merge behavior |
| Reliability | Independent behavioral test results, escaped defects, meaningful regression failures |
| Autonomy | Human interventions per verified outcome; appropriate escalations |
| Efficiency | End-to-end latency, total model/tool cost, CI/test runtime, retries, review overhead |
| Reuse | Existing capability reuse versus rediscovery; stale-result incidents |
| Convergence | Review cycles to resolution, oscillation, abandoned loops |
| Verification quality | Critical user journeys covered, flaky tests, assertion weakening, duplicated or implementation-coupled tests |
| Collaboration | Attribution completeness, update latency, unresolved conflicts |
| Portability | Same context/method usable across tools and repositories |

Avoid claiming causality from metrics alone. Compare similar tasks, preserve counterexamples and assess the cost of failures that are not immediately visible.

## 15. Open questions and practical next steps

This is a **direction**, not a backlog commitment. Before implementing more abstractions:

1. **Inventory reality.** Inspect current Context Kit hubs, MCP tools, skills, OpenSpec integrations, scripts, issues and actual PR/workflow history. Mark what already exists, what is partial and what is missing.
2. **Define a minimum provenance contract.** Explore canonical source, author, timestamps, trust/status, evidence and relationships in an extensible representation that does not require OKF everywhere.
3. **Close the publication/discovery loop.** Expose authoritative repository/path and write-policy hints; distinguish MCP read-only mode from independently authorized GitHub writes; define refresh semantics.
4. **Pilot collaborative knowledge changes.** Try a real multi-person or multi-agent proposal → review → update → supersede cycle; distinguish human adoption from generated notes.
5. **Pilot one reusable capability.** Pick a real repeated task, separate scripts from judgment, evaluate in fresh context, record limitations and observe subsequent reuse.
6. **Test selective context boundaries.** Compare builder, reviewer and router contexts; keep holdouts independent while preserving provenance. Establish a small reliable set of real user/API journeys, then evaluate whether extra unit, integration or E2E tests actually add confidence.
7. **Measure routing and economics.** Collect real task classes, outcomes, costs, cache behavior and escalation reasons before training a specialized classifier.
8. **Keep implementations small.** Favor vertical slices and evidence from real usage over a premature universal orchestrator.

## 16. The long-term destination

The shift is from prompting an agent to **engineering the system that engineers**: a network of durable, discoverable, composable capabilities that turns intent into observable outcomes, learns from evidence, and scales useful work without scaling human supervision proportionally.

This does **not** require one agent that does everything, one mandated document format, one model provider, or one orchestrator. It requires clear authority, preserved intent, appropriate division of labor, reliable feedback, bounded convergence, portable context and the ability to reuse established knowledge.

**Final principle:** Maximize verified, intent-aligned outcomes while minimizing unnecessary inference, coordination and human intervention. Preserve human authority over what matters; make the rest progressively more repeatable, transparent and composable.

---

### Testing references

- [Playwright: Best Practices](https://playwright.dev/docs/best-practices) — user-visible behavior and test isolation.
- [Playwright: Test Agents](https://playwright.dev/docs/test-agents) — model-assisted planning, generation and repair of browser tests; a capability, not automatic assurance that repairs preserve intent.
- [Martin Fowler: Test Pyramid](https://martinfowler.com/bliki/TestPyramid.html) and [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html) — cost, breadth and reliability tradeoffs; even the classic framing allows exceptions when high-level checks are inexpensive.
- [Kent C. Dodds: Static vs Unit vs Integration vs E2E](https://kentcdodds.com/blog/static-vs-unit-vs-integration-vs-e2e-tests) — choosing tests for confidence rather than strict counts.

### Related documents in this repository

- [Determinism in Agent Workflows](determinism-in-agent-workflows.md) — guidance, deterministic primitives and enforced boundaries.
- [Simple Workflow Ideation](workflow.md) — thin vertical behavior slices.
- [OpenSpec Adoption Plan](openspec-adoption-plan.md) — specification and implementation workflow ideas.

This document is deliberately broader than any one of those techniques and should evolve through real implementation evidence rather than being treated as an immutable manifesto.
