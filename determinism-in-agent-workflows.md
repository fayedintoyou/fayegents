# Determinism in Agent Workflows

## Executive summary

Determinism in an agent workflow should not mean scripting the entire workflow or replacing an interactive agent session with a fixed headless run. It means identifying the parts of the work whose correct behavior is already known, encoding those parts in software, and leaving the agent to reason about the parts that genuinely require judgment.

The useful split is:

> Let the model decide what requires interpretation. Let executable mechanisms handle what can be computed, repeated, or objectively verified.

An agent can remain adaptive while operating inside deterministic boundaries. It can explore an unfamiliar codebase, decompose a problem, choose an implementation, interpret failures, and revise its plan. Formatting, static rules, architecture boundaries, behavioral contracts, security policy, and completion checks do not need to depend on the model remembering instructions.

The goal is not deterministic model output. The goal is predictable mechanics, explicit constraints, objective feedback, and enforceable invariants around probabilistic reasoning.

## Core distinctions

### Determinism is not the same as headless execution

A Jira-triggered, headless agent run is one orchestration topology. It can still behave unpredictably if its instructions are ambiguous, its tools are unconstrained, and its completion criteria are subjective.

Likewise, an interactive harness session is not inherently uncontrolled. It can use stable scripts, constrained tools, schemas, tests, permissions, CI checks, and approval gates while preserving the agent's ability to adapt.

> Headless is an invocation mode, not a synonym for deterministic. Interactive is a collaboration mode, not a synonym for uncontrolled.

### A deterministic primitive is not automatically an enforced workflow

A formatter, linter, test suite, or validation script can behave deterministically when invoked. If the model merely receives an instruction to run it, however, the workflow does not guarantee that invocation.

This creates three levels of control:

| Control level | Examples | Strength |
| --- | --- | --- |
| Guidance | Repository instructions, skills, runbooks, task templates, completion checklists | Improves consistency, but the model can misunderstand or omit a step |
| Deterministic primitives | Formatters, linters, scripts, typed tools, schemas, tests | Produce repeatable execution and objective results when invoked |
| Enforced boundaries | Required CI checks, branch protection, permissions, approval gates, managed hooks and policies | Provide guarantees independent of model memory |

Instructions express intent. Checks produce evidence. External gates provide enforcement.

### Do not ask the model to deduce what software can decide

Formatting is the simplest example. Instead of asking an agent to infer formatting conventions, run Prettier. Instead of repeatedly telling it not to use TypeScript non-null assertions, encode an ESLint rule. The agent can reason about how to satisfy the rule; it should not be responsible for deciding whether the rule was satisfied.

> The agent reasons about how to satisfy an invariant. An executable check determines whether the invariant holds.

## Where deterministic mechanisms can help

Formatting and linting are only the first rung. The same principle extends from surface-level consistency into architecture, behavior, security, operations, and release management.

| Area | Instead of repeatedly telling the agent... | Encode it as... |
| --- | --- | --- |
| Mechanical consistency | Follow our formatting, import, and generated-file conventions | Formatter, import sorter, generated-file validation |
| Language safety | Avoid non-null assertions, unsafe casts, or unhandled variants | Linter rules, compiler options, exhaustiveness checks |
| Architecture | Domain code must not import infrastructure code | Dependency-boundary checks or architecture tests |
| Behavioral correctness | Preserve this behavior across relevant inputs | Unit, regression, integration, or property-based tests |
| Interface compatibility | Do not break existing consumers | API schema diff, consumer contract test, compatibility checker |
| Data integrity | Keep migrations compatible and preserve required constraints | Migration validator, schema checks, data invariants |
| Security and policy | Do not use prohibited APIs or bypass authorization boundaries | Static analysis, policy-as-code, authorization tests |
| Operational quality | Do not exceed latency, bundle-size, query-count, or memory limits | Benchmarks and performance budgets |
| Release completeness | Remember version changes, generated clients, migrations, and release artifacts | Release validation script or manifest check |

Not every check proves complete correctness. A contract test only proves the contract it encodes, and a schema validates structure rather than semantic quality. The value is that known, detectable failure modes no longer require another human or model judgment call.

## What should remain adaptive

Agent reasoning is most useful where the path cannot be specified cheaply in advance:

- Discovering relevant context in an unfamiliar codebase
- Decomposing an ambiguous or novel problem
- Choosing among valid implementation strategies
- Identifying the cause of a failed check
- Revising an approach after new evidence appears
- Explaining tradeoffs and surfacing missing requirements
- Recognizing when no existing deterministic rule captures the real issue

Trying to encode these activities as a rigid graph can create orchestration machinery that is more brittle than the agent it is attempting to control. The workflow should constrain known invariants without prematurely prescribing every intermediate step.

## Practical patterns without custom hooks

In environments where developers cannot define or distribute their own hooks, the core approach still works. Hooks are only one possible lifecycle mechanism.

### Canonical repository commands

Aggregate standard checks behind stable entry points such as:

- `make verify`
- `npm run validate`
- `./scripts/check-change`

This prevents every agent session from inventing its own command sequence. The command can be run by a human, Claude Code, Codex, another harness, or CI.

### Narrow scripts for repeated mechanics

Move repeatable shell work into scripts rather than asking the model to regenerate a procedure every time. Useful candidates include migrations, code generation, dependency upgrades, environment setup, compatibility checks, and release preparation.

The model should interpret results and decide how to respond. The script should handle ordering, arguments, file selection, and mechanical failure detection.

### Repository instructions and reusable procedures

Files such as `CLAUDE.md`, `AGENTS.md`, skills, or equivalent harness instructions can define:

- When a workflow applies
- Which canonical command to run
- What evidence to return
- What counts as complete
- When to stop and ask for human judgment

These increase reliability and portability, but they remain model-mediated guidance unless an external mechanism enforces them.

### Executable acceptance criteria

When possible, turn requirements into tests or validation artifacts that the agent can run during the session. A useful pattern is:

1. State the invariant.
2. Add or identify the check that demonstrates it.
3. Let the agent choose the implementation.
4. Require the resulting evidence at handoff.

For example, replace "make sure this API change is backward compatible" with a compatibility test and a canonical verification command.

### Existing external enforcement

Use the controls already available outside the harness:

- CI checks
- Branch protections
- Required approvals
- Security scanners
- Deployment policies
- Human review for consequential actions

These are where deterministic primitives become actual guarantees.

## Hooks in an enterprise-managed environment

Hooks are one way a harness can automatically invoke a command at a lifecycle boundary. They are useful conceptually because they demonstrate the difference between asking the model to remember a step and guaranteeing that the step runs.

In the current corporate environment, hooks are enterprise-defined and developers cannot add or distribute custom hooks. They should therefore be presented as a platform-level control, not as an immediately available practitioner recommendation.

A reasonable enterprise opportunity would be to offer a small set of governed hook capabilities for broadly useful controls such as:

- Standard verification entry points
- Secret scanning
- Audit logging
- Protected-action approval
- Required handoff evidence

This does not require allowing arbitrary developer-supplied shell hooks. A curated or declarative mechanism may fit corporate security constraints better.

## Cross-harness portability

The conceptual model should stay vendor-neutral even if Claude Code is the only tool currently available. The durable unit is not a Claude-specific hook or a Codex-specific instruction file. It is a repository-owned contract that multiple harnesses can discover and execute.

| Portable concept | Claude Code example | Codex example |
| --- | --- | --- |
| Repository guidance | `CLAUDE.md` | `AGENTS.md` |
| Reusable procedure | Skills or commands | Skills |
| Deterministic mechanics | Repository scripts and tools | Repository scripts and tools |
| Execution constraints | Managed permissions and hooks | Sandbox, approvals, and policy rules |
| Hard verification | CI and branch protection | CI and branch protection |

The most portable investments are tests, schemas, scripts, task-runner commands, policy rules, and CI checks. They remain valuable regardless of which model or harness invokes them.

## The improvement flywheel

The deeper value of determinism is not selecting every rule up front. It is turning repeated experience into reusable infrastructure.

1. A human or model catches a recurring mistake.
2. The team asks whether the mistake is mechanically detectable.
3. If it is, the team encodes it as a rule, test, schema, script, or policy.
4. The check is added to a canonical verification workflow.
5. Where risk justifies it, an external system makes the check mandatory.
6. Human and model judgment shifts to the remaining ambiguous cases.

> Every recurring, mechanically detectable correction is a candidate to become part of the harness.

This produces cumulative improvement. Future sessions inherit an executable lesson rather than another paragraph of prompt text.

## Common mistakes in this discussion

- Treating determinism as an all-or-nothing property of the entire workflow
- Treating a headless invocation as deterministic by definition
- Treating prompts, repository instructions, or skills as hard enforcement
- Asking an LLM evaluator to provide a deterministic guarantee
- Hardcoding open-ended reasoning paths that benefit from adaptation
- Creating large orchestration frameworks before encoding basic repository invariants
- Automating a check without making its failure actionable to the agent
- Confusing reproducible mechanics with guaranteed correctness

## Recommended one-slide narrative

### Title

**Deterministic rails around adaptive agent work**

### Subtitle

**Let the model reason where the path is uncertain; use software where the rule is already known.**

### Visual structure

Show three layers surrounding or supporting an adaptive agent session:

1. **Guidance**
   - Repository instructions
   - Skills and runbooks
   - Explicit acceptance criteria

2. **Executable feedback**
   - Formatters and linters
   - Type, schema, and architecture checks
   - Tests, contracts, security checks, and performance budgets

3. **Enforced boundaries**
   - CI and branch protection
   - Permissions and approvals
   - Enterprise-managed hooks and policies

Place the adaptive work in the center:

- Explore
- Decompose
- Implement
- Diagnose
- Revise

### Slide takeaway

> Move known invariants out of model discretion, not every decision out of the agent.

## Short speaker notes

Determinism does not require turning every agent interaction into a fixed, headless workflow. The better question is where we already know the rule and can encode it. We do not ask the model to reason about our formatting rules; we run a formatter. We do not repeatedly remind it to avoid a TypeScript construct; we add a lint rule. That pattern extends into architecture tests, API compatibility checks, security policy, and performance budgets.

The model still provides value by exploring the codebase, choosing an implementation, and diagnosing failures. Deterministic mechanisms give it objective feedback. Instructions and skills improve consistency, but hard guarantees come from CI, permissions, approvals, or other enforcement outside the model.

Our managed environment limits custom hooks, so hooks are a platform-level example rather than the main recommendation. The practices teams can adopt now are portable repository scripts, canonical verification commands, executable acceptance criteria, and existing CI controls. Those investments will work with Claude Code today and Codex or another harness later.

## Paste-ready prompt for a slide-building agent

```text
Create one concise presentation slide about the role of determinism in agentic software-development workflows.

Audience:
Software engineers and engineering leaders who may equate deterministic agent workflows with completely scripted, headless agent runs.

Core argument:
Determinism is not an all-or-nothing execution model. Preserve model autonomy for exploration, decomposition, implementation choices, failure diagnosis, and plan revision. Move known rules, repeatable mechanics, safety constraints, and objective completion checks into executable mechanisms.

Use this title:
Deterministic rails around adaptive agent work

Use this subtitle:
Let the model reason where the path is uncertain; use software where the rule is already known.

Show three levels of control:

1. Guidance
- Repository instructions
- Skills and runbooks
- Explicit acceptance criteria
- Improves consistency but remains model-mediated

2. Executable feedback
- Formatters and linters
- Type, schema, and architecture checks
- Tests, contracts, security rules, and performance budgets
- Produces objective evidence when invoked

3. Enforced boundaries
- CI and branch protection
- Permissions and approvals
- Enterprise-managed hooks and policies
- Provides guarantees independent of model memory

Include an adaptive agent loop containing:
- Explore context
- Decompose the problem
- Choose and implement an approach
- Interpret failures
- Revise the plan

Use these examples to make the progression concrete:
- Prettier instead of asking the model to infer formatting
- ESLint instead of reminding it to avoid TypeScript non-null assertions
- Architecture tests for dependency boundaries
- Contract tests for API compatibility
- Policy-as-code for security requirements
- Performance budgets for operational constraints

Include these two statements:
“The agent reasons about how to satisfy an invariant. An executable check determines whether the invariant holds.”
“Move known invariants out of model discretion, not every decision out of the agent.”

Represent hooks only as one example of enterprise-level enforcement. Note that in the current environment hooks are centrally managed and developers cannot define or distribute custom hooks. Do not recommend custom hooks as an immediately available team practice.

Keep the content vendor-neutral. Claude Code is currently available and Codex may be available later, but the main recommendations should be portable: repository scripts, stable commands, tests, schemas, policies, CI checks, and approval gates.

Avoid:
- Presenting deterministic and agentic workflows as opposites
- Treating headless execution as deterministic by definition
- Claiming instructions or skills guarantee behavior
- Treating model-based evaluation as deterministic
- Suggesting every workflow should become a fixed DAG
- Centering the slide on custom hooks
```

## References

- [Anthropic: Automate workflows with hooks](https://docs.anthropic.com/en/docs/claude-code/hooks-guide)
- [Anthropic: How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system)
- [OpenAI: Using skills to accelerate OSS maintenance](https://developers.openai.com/blog/skills-agents-sdk)
- [OpenAI: Custom Code Review rules for Codex](https://developers.openai.com/blog/custom-code-review-rules-for-codex)
- [OpenAI: Build an Agent Improvement Loop with Traces, Evals, and Codex](https://developers.openai.com/cookbook/examples/agents_sdk/agent_improvement_loop)
