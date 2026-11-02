# Structured handoffs and approval gates in agent workflows

*Research note, 2 November 2026, compiled with Claude from public sources. It follows up [How leading firms run coding agents](frontier-coding-agents.md) and covers issues [#2](https://github.com/fayedintoyou/fayegents/issues/2) and [#3](https://github.com/fayedintoyou/fayegents/issues/3). Quoted wording was checked against each source's text. Most figures are self-reported, and framework features are listed as capabilities, with no claim about who uses them.*

## Bottom line

Production coding-agent systems use deterministic orchestration, but almost none publish runtime schema checks on what one agent hands the next. What moves between stages is usually the code itself, a diff, a test or a Markdown note. Executable checks (parse, build, test, coverage, mutation kills) and LLM judges decide whether it moves on.

Human approval sits mostly at merge, deploy or another consequential action. Only a few named deployments put a person earlier in the flow, and each one guards a costly step. Atlassian's HULA has engineers approve a plan and then the code before a pull request exists. Google's migration drivers inspect every generated change before code owners see it. Replit's agent pauses for user consent and for deployment approval.

The best data on gate placement points the same way. In HULA, engineers approved 82% of plans but only a quarter of the code generated from them, so most work was stopped at code review, after the plan had passed. Meta moved its engineers from reviewing every generated test to handling only ranked exceptions and cut review load by 70%. A preprint on repeat reviewers of agent PRs found approvals rising and scrutiny falling over time.

The framework docs agree. OpenAI's Agents SDK, LangGraph and Claude Code all point approvals at side effects and irreversible actions. Two details matter for anyone building gates: few frameworks check at runtime what passes between stages, and an approved action can run again when a paused run resumes.

The useful question is where contracts and gates earn their cost. Structure pays off when code consumes it, as when Cloudflare's severity field decides whether to block a merge. Gates pay off in front of steps that are expensive or hard to undo. The evidence doesn't support a human checkpoint after every artifact an agent produces.

## Three claims to keep apart

Write-ups often blur three different claims:

1. The company uses deterministic orchestration, meaning code sequences the steps and runs the checks.
2. Its system validates structured contracts between agent steps at runtime.
3. It requires human approval at those transitions.

Evidence for the first doesn't establish the other two. Every production system below does the first. Almost none documents the second, and most do the third only at merge or deploy.

## Coding-agent systems

| System | What passes between stages | Checked by code? | Where a person approves | Evidence |
| --- | --- | --- | --- | --- |
| [Stripe Minions](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2) | A Slack thread and its links go in, and a branch and PR come out. Blueprint nodes are defined in code, and the data passed between them isn't described | Lint and CI checks, with at most two CI rounds | The engineer who launched the run reviews the branch, opens the PR and asks a colleague to review it | Company posts |
| [OpenAI Symphony](https://openai.com/index/open-source-codex-orchestration-symphony) (public reference workflow) | The ticket becomes a templated prompt. The agent hands back a Markdown "Codex Workpad" comment, the PR and walkthrough media | The config and templates are validated ("Unknown variables MUST fail rendering"). The workpad's structure is set only by the prompt. The `land` skill merges once checks pass and no review comments are open | A person moves tickets from Backlog to Todo and from Human Review to Merging, and a reviewer who wants changes sends the ticket to Rework. Inside a run, Codex never stops for approval (`approval_policy: never`) | Company post and [repo](https://github.com/openai/symphony) |
| [OpenAI's agentic software factory](https://newsletter.pragmaticengineer.com/p/openai-software-factory) | Not described in the free sections | Specialist review agents, with review depth set by risk | A person reviews high-risk changes after the agents finish and approves each change before it goes to production. Low-risk areas can opt into approval by an agent | Newsletter, based on interviews |
| [Spotify Honk](https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3) | An intake agent turns a conversation with the engineer into a free-text prompt for the coding agent | Verifiers behind one tool, and a stop hook that keeps the PR from opening until they pass | For a dataset migration, owning teams tested and merged the PRs. Most of the 2.5 million automated maintenance PRs through Fleet Management merged with no person involved | Company posts |
| [Anthropic's long-running harnesses](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) | A JSON feature list (category, description, steps, passes) written by an initializer agent, and files passed between [generator and evaluator](https://www.anthropic.com/engineering/harness-design-long-running-apps) | The feature list's rules are enforced only through the prompt. An LLM evaluator applies a hard threshold to each criterion | None at runtime. These are research harnesses | Lab posts |
| [GitHub Copilot cloud agent](https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/risks-and-mitigations) | An issue becomes a draft PR on a `copilot/` branch | Branch protections and required checks | Someone other than the requester must approve, and the agent can't approve or merge. Actions workflows on its PRs wait for approval by default | Product docs |
| [Kiro specs](https://kiro.dev/docs/specs/best-practices/) | requirements.md, design.md and tasks.md, all Markdown | An optional LLM pass that looks for gaps and conflicts | In Feature Specs you review each document before the next is generated. Quick Spec runs "without approval gates" | Product docs |
| [Uber uReview](https://www.uber.com/blog/ureview/) | Review comments tagged with their origin, category and a confidence score | Confidence thresholds per assistant, language and category, plus deduplication and suppression of low-value categories | Developers act on the comments | Company post |
| [Shopify Roast](https://github.com/Shopify/roast) | Ruby objects, text by default, with a lenient JSON parser | A command that exits non-zero fails its step | The framework has no approval step | Company post and repo |
| [Cloudflare AI code review](https://blog.cloudflare.com/ai-code-review/) | Specialist reviewers return findings as structured XML with a severity, and a coordinator posts one review comment | The AI approves or blocks the merge request | A human reviewer can force approval with "break glass," used on 0.6% of merge requests | Company post |

Symphony's gates come from which ticket states count as active. The agent runs only in Todo, In Progress, Merging and Rework, so nothing happens in Backlog or Human Review until a person moves the ticket. The workflow treats Rework as "a full approach reset, not incremental patching": the agent closes the PR, deletes its workpad and starts a fresh branch. OpenAI's internal workflow may differ from this public one.

## Other named deployments

| Deployment | What passes between stages | Checks | Where a person approves | Outcome | Evidence |
| --- | --- | --- | --- | --- | --- |
| [Google ID migrations](https://arxiv.org/abs/2504.09691) (int32 to int64) | LLM-generated diffs | Six checks, cheapest first: the call succeeded, the change is more than whitespace, the AST parses and differs, an LLM "Punt" judge says the change was needed, the build passes and tests pass. A file that fails any check is left for developers to migrate by hand | The migration driver inspects and edits every change before code owners review it. For two IDs, owners were involved again before a slow rollout | Of 595 changes, 36% were LLM-only, 38% LLM then human, and 26% human-only. The team estimates a 50% time saving but has no wall-clock measurements | FSE 2025 paper |
| [Google's other migrations](https://arxiv.org/abs/2501.06972) (JUnit 3 to 4 and others) | Diffs | Build and tests, with ML repair | A person checks each change, then normal reviewers | For JUnit 3 to 4, about 87% of the AI-generated code was committed unchanged. Reviewer speed was the bottleneck, and the team slowed some migrations so owning teams weren't overwhelmed | Experience report (preprint) |
| [Meta ACH](https://arxiv.org/abs/2501.12862), then [JiT tests](https://engineering.fb.com/2026/10/29/security/catching-bugs-before-they-land-jittestings-human-on-the-loop-approach-at-full-scale/) | Mutated code marked off by comment delimiters, then tests | A test must build, pass, not flake and kill the mutant, and an LLM judge screens out equivalent mutants | In ACH, engineers reviewed every test and accepted 73%. In JiT, engineers are contacted only when a candidate ranks highly enough | JiT cut review load by 70%. Engineers report 60% true positives, and 74 serious privacy bugs were caught at the diff stage in the first half of 2026 | Papers and Meta blog |
| [Atlassian HULA](https://arxiv.org/abs/2411.12924) | A per-file plan, then code | A compiler and linter loop | An engineer approves the plan, then the code, and then the PR goes to normal review | Of 663 issues, 527 got plans (79%), 433 plans were approved (82%), 376 got code (87%), 95 became PRs (25%) and 56 merged (59%) | Paper with Atlassian authors |
| [Uber AutoCover](https://homes.cs.washington.edu/~rjust/publ/auto_cover_icse_2026.pdf) (built on LangGraph) | Sub-graphs pass scenario maps, target maps and candidate tests | A test must run and raise coverage, plus bounded mutation testing and lint | A merge request to the owning team, or an explicit accept in the IDE | About 11% of new tests, and about 44% acceptance in the IDE | Paper with Uber authors |
| [Amazon Q Java upgrades](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/how-CT-works.html) | A transformation plan, then code | Rebuilds and unit tests after each step | The developer accepts the diff | Amazon's CEO said developers shipped 79% of the generated code reviews unchanged ([Aug 2024](https://simonwillison.net/2024/Aug/24/andy-jassy-amazon-ceo/)) | CEO post and product docs |
| [Replit Agent](https://temporal.io/resources/case-studies/replit-uses-temporal-to-power-replit-agent-reliably-at-scale) | One Temporal workflow per agent | Not described | The agent pauses for user consent and for deployment approval | No numbers published | Temporal case study |
| [Allianz Project Nemo](https://allianz.com/en/mediacenter/news/articles/251103-when-the-storm-clears-so-should-the-claim-queue.html) (insurance claims) | Seven agents, with an audit agent summarizing their decisions | Not described | Payout decisions always stay with a claims professional | 80% faster claim processing, with no baseline given | Company newsroom |

## What the frameworks offer

These are capabilities, documented by the vendors. None of them is evidence that a given company uses the feature in production.

| Framework | How stages pass data | Checked at runtime? | Where an approval can sit | Survives a restart? | Docs' advice on where to gate |
| --- | --- | --- | --- | --- | --- |
| [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/human_in_the_loop/) | A typed final output (`output_type`, through structured outputs) | Yes, for the final output. Invalid output raises `ModelBehaviorError` | Per tool call, optionally decided from the call's arguments (`needs_approval`) | Only if you serialize the run state and store it yourself | "Pause before side effects like cancellations, edits, shell commands, or sensitive MCP actions" ([guide](https://developers.openai.com/api/docs/guides/agents/guardrails-approvals)) |
| [LangGraph](https://docs.langchain.com/oss/python/langgraph/interrupts) | One shared graph state (TypedDict, dataclass or Pydantic) | Only the first node's input: "Run-time validation only occurs on inputs to the first node in the graph, not on subsequent nodes or outputs" ([docs](https://docs.langchain.com/oss/python/langgraph/use-graph-api)) | Any node or tool call, through `interrupt()` | Yes, with a durable checkpointer. The interrupted node re-runs from its start on resume | "Pause before executing critical actions (API calls, database changes, financial transactions)" |
| [Temporal](https://docs.temporal.io/develop/python/message-passing) | Serializable, typed workflow and activity arguments | Update validators can "reject an Update before it is written to History" | Anywhere in a workflow that waits on a Signal or Update | Yes, by replaying the workflow's event history | No placement advice on the pages checked |
| [Microsoft Agent Framework](https://learn.microsoft.com/en-us/agent-framework/workflows/human-in-the-loop) | Typed messages between executors | Left to each executor | A request port between steps, or a tool that needs approval | Yes. Pending requests are saved in [checkpoints](https://learn.microsoft.com/en-us/agent-framework/workflows/checkpoints), with Cosmos DB for cross-process use | No placement advice on the pages checked |
| [Claude Code and Agent SDK](https://code.claude.com/docs/en/permissions) | A final output checked against a JSON Schema ([Agent SDK](https://code.claude.com/docs/en/agent-sdk/structured-outputs)) | Yes. The SDK re-prompts on a mismatch and returns an error after its retry limit | Per tool call through allow, ask and deny rules or a PreToolUse hook, and at the end of a run through a Stop hook | Sessions resume. Whether a pending prompt survives a restart wasn't confirmed | Keep `ask` rules for sensitive operations. Auto mode blocks production deploys, force pushes and IAM changes by default ([permission modes](https://code.claude.com/docs/en/permission-modes)) |
| [Codex CLI](https://learn.chatgpt.com/docs/non-interactive-mode) (`codex exec`) | A final message requested against a JSON Schema (`--output-schema`), plus a JSON Lines event stream (`--json`) | The docs say the flag requests a conforming response. They don't say it's validated | A sandbox, read-only by default | `codex exec resume` picks a session back up | Not stated on the page |

Four things stand out.

Typed doesn't mean validated. Only the final-output schemas (OpenAI, Claude's Agent SDK) and Temporal's Update validators reject bad data at runtime. LangGraph checks only the first node's input, and Microsoft leaves checks to each executor. Even a validated schema only proves the JSON has the right shape. It can't show that the content still carries the original intent.

Approval doesn't mean exactly-once. OpenAI's docs say "Approval permits a tool call to execute; it does not guarantee exactly-once execution," and advise giving each logical action a "stable idempotency key." LangGraph re-runs the interrupted node from its start on resume, and Temporal retries activities without limit by default. An approval has to be tied to the exact action and arguments that were approved.

The docs agree on where to gate. OpenAI, LangGraph and Claude Code all point approvals at side effects and irreversible or high-risk actions. None recommends an approval after every step. Microsoft's built-in sequential, concurrent and group-chat orchestrations "do not pause for free-form user input on their own," though they can still pause for a tool that needs approval. A stop between steps takes a request port in a custom workflow.

A gate can fail open. Claude Code's permission rules "are enforced by Claude Code, not by the model," but its hooks don't act as gates by default: "On most events, when a hook fails or times out, Claude Code still carries out the action. A policy hook with a wrong path or a crashing script then lets everything through" ([hooks](https://code.claude.com/docs/en/hooks)). Failing closed takes `"onFailure": "block"` (v2.1.295 or later). Any home-built gate deserves the same check.

## Where gates earn their cost

Five patterns run through the evidence.

Gates sit in front of steps that are costly or hard to undo. People approve before code generation (HULA), before production (OpenAI, Replit), before a payout (Allianz) and before a risky rollout (Google). Where the artifact is cheap to check by machine, like a test, a mutant or a diff, companies replace the human gate with executable checks and an LLM judge and keep a person at the end.

In the one place a plan gate has been measured, it stopped relatively little. HULA's engineers approved 82% of plans, and the big drop came at code review, where only a quarter of the generated code went on to become a PR. Approving a plan doesn't replace checking the code.

Review capacity sets the pace. Google slowed its migrations so owning teams weren't overwhelmed, and Meta moved its engineers from reviewing every test to handling ranked exceptions. The common fixes are to group changes, rank what gets escalated and route work to the teams that own it.

Approval rate isn't a quality signal. A June 2026 preprint followed 400 repeat reviewers through 11,429 reviews of agent PRs ([Habituation at the Gate](https://arxiv.org/abs/2606.22721)). As reviewers gained experience, approvals rose from 30.1% to 36.8%, change requests fell from 11.2% to 5.6% and median review time went from 3.9 to 13.5 hours. Inline comments per review dropped from 1.01 in reviewers' first decile of experience to 0.79 in their tenth. The authors can't rule out that the agents simply got better. Either way, adding checkpoints doesn't guarantee scrutiny at each one.

Structure appears where code consumes it. Cloudflare's severity field decides whether to block a merge, and Symphony validates its config and tool results. Plans, specs and handoff notes stay Markdown or free text in every system above. Wrapping agent prose in JSON makes it parseable, but it says nothing about whether the content is right. A contract earns its place when it points at things code can check, such as a test run, a diff, a CI result or a risk class that routes the work to the right gate.

## What this means for a design with stage gates

For a pipeline that passes structured outputs between agent sessions and gates each stage, the research suggests:

1. Validate what code consumes. Keep plans and notes as text, and link them to artifacts code can check, such as tests, diffs and CI runs.
2. Put human approval in front of expensive or irreversible steps (merge, deploy, data access, privileged actions), tiered by risk. If you add a plan gate, measure what it catches.
3. Tie each approval to the exact action and arguments, with an idempotency key, because a resumed run can execute an approved step again.
4. Make gates fail closed, and check any hook or policy engine for fail-open defaults.
5. Keep the requester and the approver separate, as GitHub enforces for Copilot's PRs.
6. Measure each gate: what it rejects, how long work waits on it and what gets past it. When a gate moves, report the true-positive rate of escalations alongside the approval rate, as Meta does.

To settle the plan-gate question, run matched tasks three ways with independent acceptance tests:

- A: an autonomous loop with CI and human review at the PR.
- B: the same, plus human approval at the planning and design stages.
- C: approval only on risky transitions, such as privileged access, destructive operations, major design decisions and deployment.

Compare accepted outcomes, elapsed time, review minutes, rework and escaped defects, split by how risky and ambiguous the tasks are.

## Gaps

- No source measures escaped defects against where gates sit. Meta's 20% drop in privacy incidents over the same period is the closest, and it has no control group.
- Internal setups aren't public, including Stripe's blueprint format, the state passed between its nodes and OpenAI's own Symphony workflow.
- Outcome numbers at gates are scarce. Spotify's LLM judge vetoed about a quarter of sessions, Cloudflare's override was used on 0.6% of merge requests, and HULA published its funnel. No system reports rework or rejection rates at its human gates.
- No bank has published an intermediate gate with measured outcomes.
- The Codex CLI page says `--output-schema` requests a conforming response but doesn't say whether the output is validated.

## Sources

**Coding-agent systems**

- [Stripe: Minions, part 1](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents) and [part 2](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2) (Feb 2026)
- [OpenAI: Symphony](https://openai.com/index/open-source-codex-orchestration-symphony) (Apr 2026); Symphony repo [SPEC.md](https://github.com/openai/symphony/blob/main/SPEC.md), [WORKFLOW.md](https://github.com/openai/symphony/blob/main/elixir/WORKFLOW.md) and [land skill](https://github.com/openai/symphony/blob/main/.codex/skills/land/SKILL.md) (accessed Nov 2026); [Pragmatic Engineer: Inside OpenAI's agentic software factory](https://newsletter.pragmaticengineer.com/p/openai-software-factory) (Sep 2026)
- [Spotify: Honk part 1](https://engineering.atspotify.com/2025/11/spotifys-background-coding-agent-part-1) (Nov 2025), [part 3](https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3) (Dec 2025), [part 4](https://engineering.atspotify.com/2026/4/background-coding-agents-dataset-migrations-honk-part-4) (Apr 2026), [Coding is no longer the constraint](https://engineering.atspotify.com/2026/6/code-with-claude-coding-is-no-longer-the-constraint) (Jun 2026)
- [Anthropic: Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) (Nov 2025); [Harness design for long-running apps](https://www.anthropic.com/engineering/harness-design-long-running-apps) (Mar 2026)
- [GitHub: Copilot risks and mitigations](https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/risks-and-mitigations); [Reviewing Copilot's PRs](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/coding-agent/review-copilot-prs)
- [Kiro: Specs](https://kiro.dev/docs/specs/), [Best practices](https://kiro.dev/docs/specs/best-practices/) and [Quick Spec](https://kiro.dev/docs/specs/quick-spec/)
- [Uber: uReview](https://www.uber.com/blog/ureview/) (Aug 2025)
- [Shopify: Introducing Roast](https://shopify.engineering/introducing-roast) (Jun 2025); [Roast repo](https://github.com/Shopify/roast)
- [Cloudflare: AI code review](https://blog.cloudflare.com/ai-code-review/) (Apr 2026)

**Other deployments**

- [Google: Migrating code at scale with LLMs](https://arxiv.org/abs/2504.09691) (FSE 2025); [How is Google using AI for internal code migrations?](https://arxiv.org/abs/2501.06972) (Jan 2025)
- [Meta: Mutation-guided LLM-based test generation](https://arxiv.org/abs/2501.12862) (Jan 2025); [Just-in-time catching tests](https://arxiv.org/abs/2601.22832) (Jan 2026); [Catching bugs before they land](https://engineering.fb.com/2026/10/29/security/catching-bugs-before-they-land-jittestings-human-on-the-loop-approach-at-full-scale/) (Oct 2026)
- [Atlassian: HULA paper](https://arxiv.org/abs/2411.12924) (Jan 2025) and [blog post](https://www.atlassian.com/blog/atlassian-engineering/hula-blog-autodev-paper-human-in-the-loop-software-development-agents) (Apr 2025)
- [Uber: AutoCover paper](https://homes.cs.washington.edu/~rjust/publ/auto_cover_icse_2026.pdf) (2026)
- [Simon Willison quoting Andy Jassy on Amazon Q](https://simonwillison.net/2024/Aug/24/andy-jassy-amazon-ceo/) (Aug 2024); [AWS: how code transformation works](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/how-CT-works.html)
- [Temporal: Replit case study](https://temporal.io/resources/case-studies/replit-uses-temporal-to-power-replit-agent-reliably-at-scale) (Sep 2025)
- [Allianz: Project Nemo](https://allianz.com/en/mediacenter/news/articles/251103-when-the-storm-clears-so-should-the-claim-queue.html) (Nov 2025)
- [Habituation at the Gate](https://arxiv.org/abs/2606.22721) (preprint, Jun 2026)

**Frameworks**

- OpenAI Agents SDK: [human in the loop](https://openai.github.io/openai-agents-python/human_in_the_loop/), [guardrails](https://openai.github.io/openai-agents-python/guardrails/), [guardrails and approvals guide](https://developers.openai.com/api/docs/guides/agents/guardrails-approvals)
- LangGraph: [interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts), [graph API](https://docs.langchain.com/oss/python/langgraph/use-graph-api)
- Temporal: [message passing](https://docs.temporal.io/develop/python/message-passing), [retry policies](https://docs.temporal.io/encyclopedia/retry-policies), [OpenAI Agents SDK integration](https://docs.temporal.io/develop/python/integrations/openai-agents)
- Microsoft Agent Framework: [human in the loop](https://learn.microsoft.com/en-us/agent-framework/workflows/human-in-the-loop), [checkpoints](https://learn.microsoft.com/en-us/agent-framework/workflows/checkpoints)
- Claude Code: [permissions](https://code.claude.com/docs/en/permissions), [permission modes](https://code.claude.com/docs/en/permission-modes), [hooks](https://code.claude.com/docs/en/hooks), [Agent SDK structured outputs](https://code.claude.com/docs/en/agent-sdk/structured-outputs)
- Codex CLI: [non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode)
